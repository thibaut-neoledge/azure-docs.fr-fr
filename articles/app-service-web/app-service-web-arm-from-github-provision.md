---
title: "Déployer une application Web liée à un référentiel GitHub | Microsoft Docs"
description: "Utiliser un modèle Azure Resource Manager pour déployer une application Web qui contient un projet issu d&quot;un référentiel GitHub."
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 32739607-85fe-43c8-a4dc-1feb46d93a4d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: 92408d6358516cdb2cea068553757c036143e955
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Déployer une application Web liée à un référentiel GitHub
Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application Web liée à un projet dans un référentiel GitHub. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pour le modèle complet, consultez [Application Web liée au modèle GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-deploy"></a>Ce que vous allez déployer
Avec ce modèle, vous allez déployer une application Web qui contient le code d'un projet dans GitHub.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL
L'URL du référentiel GitHub qui contient le projet à déployer. Ce paramètre contient une valeur par défaut, mais cette valeur est uniquement destinée à vous montrer comment fournir l'URL du référentiel. Vous pouvez utiliser cette valeur lorsque vous testez le modèle, mais vous devrez fournir l'URL de votre propre référentiel lors de l'utilisation du modèle.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>branche
La branche du référentiel à utiliser lors du déploiement de l'application. La valeur par défaut est master, mais vous pouvez fournir le nom de n'importe quelle branche dans le référentiel que vous souhaitez déployer.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }

## <a name="resources-to-deploy"></a>Ressources à déployer
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Application web
Crée l'application Web qui est liée au projet dans GitHub. 

Vous spécifiez le nom de l’application web par le biais du paramètre **siteName** et l’emplacement de l’application web par le biais du paramètre **siteLocation**. Dans l'élément **dependsOn** , le modèle définit l'application Web comme dépendante du plan d'hébergement de service. Étant donné qu'elle dépend du plan d'hébergement, l'application Web n'est pas créée tant que le plan d'hébergement est en cours de création. L'élément **dependsOn** est uniquement utilisé pour spécifier l'ordre de déploiement. Si vous ne marquez pas l'application Web comme dépendante du plan d'hébergement, Azure Resource Manager tentera de créer les deux ressources en même temps et vous pourriez recevoir une erreur si l'application Web est créée avant le plan d'hébergement.

L'application Web a également une ressource enfant qui est définie dans la section des **ressources** ci-dessous. Cette ressource enfant définit le contrôle de code source pour le projet déployé avec l'application Web. Dans ce modèle, le contrôle de code source est lié à un référentiel GitHub particulier. Le référentiel GitHub est défini avec le code **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** Vous pouvez coder en dur l'URL du référentiel lorsque vous souhaitez créer un modèle qui déploie de façon répétée un projet unique tout en nécessitant le nombre minimal de paramètres.
Au lieu de coder en dur l'URL du référentiel, vous pouvez ajouter un paramètre pour l'URL du référentiel et utiliser cette valeur pour la propriété **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Commandes pour l’exécution du déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Interface de ligne de commande Azure

    azure group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json

### <a name="azure-cli-20"></a>Azure CLI 2.0

    az group deployment create -g {resource-group-name} --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json --parameters '@azuredeploy.parameters.json'

> [!NOTE] 
> Pour le contenu du fichier de paramètres JSON, consultez [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.parameters.json).
>
>


