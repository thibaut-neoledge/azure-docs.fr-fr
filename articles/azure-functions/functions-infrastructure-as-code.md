---
title: "Automatiser le déploiement de ressources pour Azure Functions | Microsoft Docs"
description: "Découvrez comment créer un modèle Azure Resource Manager qui déploie votre application Azure Functions dans Microsoft Azure."
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Automatiser le déploiement de ressources pour votre application Azure Functions

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application de fonctions. Vous allez apprendre à définir la ligne de base des ressources requises pour une application Azure Functions et les paramètres spécifiés lors de l’exécution du déploiement. En fonction des [déclencheurs et des liaisons](functions-triggers-bindings.md) utilisés dans votre fonction, vous pouvez avoir besoin de ressources supplémentaires de déploiement pour englober toute votre application comme infrastructure sous forme de code.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pour obtenir des exemples de modèle complet, consultez [Create consumption-based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Créer une application Azure Function basée sur l’utilisation) et/ou [Create an App Service Plan based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Créer une application Azure Function basée sur un plan App Service).

## <a name="what-is-deployed"></a>Ce qui est déployé

Les exemples ci-dessous créent une application Azure Function de la ligne de base. Les ressources requises pour une Function App sont les suivantes :

* Compte de [stockage Azure](../storage/index.md)
* Plan d’hébergement (Plan d’utilisation ou plan App Service)
* Function App (Microsoft.Web/Site d’une sorte de **functionapp**)

## <a name="parameters"></a>Paramètres

Azure Resource Manager vous permet de définir des paramètres pour les valeurs que vous voulez spécifier lorsque le modèle est déployé. Le modèle inclut une section appelée Paramètres, qui contient toutes les valeurs de paramètres. Vous devez définir les paramètres des valeurs qui varient selon le projet que vous déployez, ou l’environnement dans lequel vous effectuez le déploiement.

Les [variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) sont utiles pour les valeurs qui ne changent pas en fonction d’un déploiement individuel ou les paramètres qui requièrent une transformation avant leur utilisation dans un modèle (par exemple, pour transmettre des règles de validation).

Lorsque vous définissez les paramètres, utilisez le champ **allowedValues** pour spécifier les valeurs qu'un utilisateur peut fournir au cours du déploiement. Utilisez le champ **defaultValue** pour affecter une valeur au paramètre, si aucune valeur n'est fournie pendant le déploiement.

Nous allons décrire les paramètres de notre modèle.

### <a name="appname"></a>appName

Nom de la fonction que vous souhaitez créer.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Emplacement dans lequel déployer Function App.

> [!NOTE]
> Le paramètre **defaultValue** est utilisé pour hériter de l’emplacement du groupe de ressources, ou si une valeur de paramètre n’est pas spécifiée lors d’un déploiement Powershell ou de l’interface de ligne de commande. En cas de déploiement à partir du portail, une zone de liste déroulante est fournie pour sélectionner les **allowedValues**.

> [!TIP]
> Pour obtenir une liste actualisée des régions dans lesquelles Azure Functions est disponible, rendez-vous sur la page [Products available by region](https://azure.microsoft.com/regions/services/) (Disponibilité des produits par région).

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (facultatif)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>Variables

En plus des paramètres, les modèles Azure Resource Manager disposent également d’un concept de variables qui peuvent intégrer des paramètres pour créer des paramètres plus spécifiques à utiliser par votre modèle.

Dans l’exemple ci-dessous, vous pouvez voir que nous exploitons des variables pour appliquer les [fonctions des modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) de manière à prendre l’appName fourni et à le convertir en minuscules pour s’assurer que les [exigences d’attribution de nom](../storage/storage-create-storage-account.md#create-a-storage-account) pour les comptes de stockage Azure sont remplies.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Ressources à déployer

### <a name="storage-account"></a>Compte de stockage

Un compte de stockage Azure est une ressource requise dans Azure Functions.

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>Plan d’hébergement : plan de consommation par rapport au plan App Service

Il existe des scénarios lors de la création de fonctions dans lesquels vous voudrez peut-être que la mise à l’échelle de vos fonctions soit entièrement gérée, ce qui signifie une mise à l’échelle à la demande par la plateforme (consommation). Vous pourriez également choisir une mise à l’échelle gérée par l’utilisateur dans laquelle vos fonctions s’exécutent 24h/24 et 7 j/7 sur un matériel dédié (plan App Service) dans lequel le nombre d’instances peut être configuré manuellement ou automatiquement. La décision d’utiliser un plan plutôt qu’un autre peut reposer sur les fonctionnalités disponibles dans le plan, ou une décision basée sur l’architecture par coût. Pour en savoir plus sur les différents plans d’hébergements, consultez l’article [Scaling Azure Functions](functions-scale.md) (Mettre à l’échelle Azure Functions).

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

#### <a name="app-service-plan"></a>Plan App Service

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

### <a name="function-app-site"></a>Function App (site)

Une fois l’option de mise à l’échelle sélectionnée, créez le conteneur qui contiendra toutes vos fonctions, et connu sous le nom de Function App.

Une Function App dispose de nombreuses ressources enfants dont vous pouvez tirer parti, notamment les **Paramètres de l’application** et les **Options de contrôle de la souce**. Vous pouvez choisir de supprimer la ressource enfant **sourcecontrols** en faveur d’une autre [option de déploiement](functions-continuous-deployment.md).

> [!IMPORTANT]
> Il est important de comprendre comment les ressources sont déployées dans Azure pour s’assurer de créer une configuration d’infrastructure sous forme de code réussie pour votre applications lors de l’utilisation d’Azure Resource Manager. Dans cet exemple, vous remarquerez que des configurations de niveau supérieur sont appliquées à l’aide de **siteConfig**. Il est important de les définir à un niveau supérieur, dans la mesure où elles apportent du sens au moteur de déploiement et d’exécution Azure Functions et sont requises avant l’application de la ressource enfant **sourcecontrols/web**. Bien que ces paramètres puissent être configurés dans la ressource de niveau enfant **configuration/appSettings**, il existe des scénarios dans lesquels votre Function App et vos fonctions doivent être déployés *avant* d’appliquer les **configuration/appsettings**, dans la mesure où vos fonctions sont une dépendance d’une autre ressource, par exemple une [application logique](../logic-apps/index.md).

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
> Dans ce modèle, le paramètre d’application [Projet](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), qui définit le répertoire de base dans lequel le moteur de déploiement de fonctions (Kudu) recherche le code pouvant être déployé, est utilisé. Dans cet exemple, nous avons défini cette valeur sur `src` dans la mesure où notre référentiel GitHub contient un dossier `src`, dont nos fonctions sont des enfants. Si vous disposez d’un référentiel dans lequel vos fonctions se trouvent directement à la racine, ou si vous n’effectuez pas de déploiement à partir du contrôle de source, ce paramètre d’application peut être supprimé.

## <a name="deploying-your-template"></a>Déploiement du modèle

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [INTERFACE DE LIGNE DE COMMANDE](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portail](../azure-resource-manager/resource-group-template-deploy-portal.md)
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

Maintenant que vous avez la possibilité de déployer une application de fonction à partir de code, profitez-en pour en savoir plus sur le développement et la configuration d’Azure Functions :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Comment configurer les paramètres d’application Azure Functions](functions-how-to-use-azure-function-app-settings.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


