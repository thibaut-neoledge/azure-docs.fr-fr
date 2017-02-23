---
title: "Automatiser le déploiement de ressources pour une application Azure Functions | Microsoft Docs"
description: "Découvrez comment créer un modèle Azure Resource Manager qui déploie votre application Azure Functions."
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: Azure Functions, fonctions, architecture sans serveur, infrastructure sous forme code, azure resource manager
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Automatiser le déploiement de ressources pour votre application Azure Functions

Vous pouvez utiliser un modèle Azure Resource Manager pour déployer une application Azure Functions. Apprenez à définir les ressources de ligne de base requises pour une application Azure Functions et les paramètres spécifiés lors du déploiement. En fonction des [déclencheurs et des liaisons](functions-triggers-bindings.md) dans votre application Functions, pour garantir la réussite de la configuration de l’Infrastructure as Code de votre application, vous devrez peut-être déployer des ressources supplémentaires.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pour obtenir des exemples de modèles complets, consultez [Create a Consumption plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Créer une application Azure Functions basée sur l’utilisation) et [Create an App Service plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Créer une application Azure Functions basée sur un plan App Service).

## <a name="required-resources"></a>Ressources nécessaires

Vous pouvez utiliser les exemples de cet article pour créer une application Azure Functions de ligne de base. Vous aurez besoin des ressources suivantes pour votre application :

* Compte de [stockage Azure](../storage/index.md)
* Plan d’hébergement (plan d’utilisation ou plan Azure App Service)
* Application Functions (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque votre modèle est déployé. La section **Paramètres** d’un modèle contient toutes les valeurs des paramètres. Définissez les paramètres des valeurs qui varient selon le projet que vous déployez, ou l’environnement dans lequel vous effectuez le déploiement.

Les [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) sont utiles pour les valeurs qui ne changent pas en fonction d’un déploiement individuel et les paramètres qui requièrent une transformation avant leur utilisation dans un modèle (par exemple, pour transmettre des règles de validation).

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs qu’un utilisateur peut fournir au cours du déploiement. Utilisez le champ **defaultValue** pour affecter une valeur au paramètre, si aucune valeur n’est fournie pendant le déploiement.

Un modèle Azure Resource Manager utilise les paramètres suivants.

### <a name="appname"></a>appName

Le nom de l’application Azure Functions que vous souhaitez créer.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Emplacement de déploiement de l’application Functions.

> [!NOTE]
> Utilisez le paramètre **defaultValue** pour hériter de l’emplacement du groupe de ressources, ou si une valeur de paramètre n’est pas spécifiée lors d’un déploiement PowerShell ou de l’interface de ligne de commande Azure. Si vous déployez votre application à partir du portail Azure, sélectionnez une valeur dans la zone de liste déroulante du paramètre **allowedValues**.

> [!TIP]
> Pour obtenir une liste actualisée des régions dans lesquelles vous pouvez utiliser Azure Functions, rendez-vous sur la page [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (facultatif)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (facultatif)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (facultatif)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>Variables

Les modèles Azure Resource Manager utilisent des variables pour incorporer des paramètres ; vous pouvez donc utiliser des paramètres plus spécifiques dans votre modèle.

Dans l’exemple suivant, afin de répondre aux [critères d’attribution de noms](../storage/storage-create-storage-account.md#create-a-storage-account) du compte de stockage Azure, nous utilisons des variables pour appliquer [les fonctions du modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) et ainsi convertir la valeur **appName** saisie en minuscules.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Ressources à déployer

### <a name="storage-account"></a>Compte de stockage

Un compte de stockage Azure est nécessaire pour une application Azure Functions.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>Plan d’hébergement : utilisation ou App Service

Dans certains scénarios, vous souhaiterez peut-être que vos fonctions soient mises à l’échelle à la demande par la plateforme, opération également appelée mise à l’échelle complètement gérée (en utilisant un plan d’hébergement basé sur l’utilisation). Ou vous préférez peut-être que vos fonctions soient mises à l’échelle par l’utilisateur. Si vous optez pour la mise à l’échelle gérée par l’utilisateur, vos fonctions s’exécuteront 24 h/24 et 7 j/7 sur du matériel dédié (en utilisant un plan d’hébergement App Service). Le nombre d’instances peut être défini manuellement ou automatiquement. Le choix du plan d’hébergement peut se faire en fonction des éléments proposés dans le plan ou de l’architecture par coût. Pour en savoir plus sur les plans d’hébergement, consultez [Mise à l’échelle des fonctions Azure](functions-scale.md).

#### <a name="consumption-plan"></a>Plan de consommation

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>Plan App Service

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Application Functions (un site)

Une fois que vous avez sélectionné une option de mise à l’échelle, créez une application Functions. L’application est le conteneur regroupant toutes vos fonctions.

Une application Functions dispose de nombreuses ressources enfant que vous pouvez utiliser dans votre développement, notamment les paramètres de l’application et les options de contrôle de code source. Vous pouvez également choisir de supprimer la ressource enfant **sourcecontrols** et utiliser une autre [option de déploiement](functions-continuous-deployment.md) à la place.

> [!IMPORTANT]
> Il est important de comprendre comment les ressources sont déployées dans Azure pour s’assurer de créer une configuration Infrastructure as Code réussie pour votre application en utilisant Azure Resource Manager. Dans l’exemple suivant, les configurations de niveau supérieur sont appliquées à l’aide de **siteConfig**. Il est important de définir ces configurations à un niveau supérieur, car elles fournissent des informations au moteur de déploiement et au runtime Azure Functions. Des informations de niveau supérieur sont requises avant que la ressource enfant **sourcecontrols/web** soit appliquée. Bien qu’il soit possible de configurer ces paramètres dans la ressource **configuration/appSettings** au niveau enfant, dans certains scénarios, votre application Functions et les fonctions doivent être déployées *avant* que la ressource **configuration/appSettings** soit appliquée. Dans ce cas par exemple, dans [Logic Apps](../logic-apps/index.md), vos fonctions sont une dépendance d’une autre ressource.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Dans ce modèle, la valeur du paramètre d’application [Projet](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), qui définit le répertoire de base dans lequel le moteur de déploiement de fonctions (Kudu) recherche le code pouvant être déployé, est utilisée. Dans notre référentiel, nos fonctions se trouvent dans un sous-dossier du dossier **src**. Par conséquent, dans l’exemple précédent, nous définissons la valeur de paramètres d’application sur `src`. Si vos fonctions sont à la racine de votre référentiel, ou si vous ne déployez pas de contrôle de code source, vous pouvez supprimer cette valeur de paramètres d’application.

## <a name="deploy-your-template"></a>Déployer votre modèle

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Bouton Déployer dans Azure

Remplacez ```<url-encoded-path-to-azuredeploy-json>``` par une version [d’URL encodée](https://www.bing.com/search?q=url+encode) du chemin d’accès brut de votre fichier `azuredeploy.json` dans GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le développement et la configuration d’Azure Functions.

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Comment configurer les paramètres d’application Azure Functions](functions-how-to-use-azure-function-app-settings.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


