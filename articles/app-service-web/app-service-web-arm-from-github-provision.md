<properties 
	pageTitle="Déployer une application Web liée à un référentiel GitHub" 
	description="Utiliser un modèle Azure Resource Manager pour déployer une application Web qui contient un projet issu d'un référentiel GitHub." 
	services="app-service" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/16/2015" 
	ms.author="tomfitz"/>

# Déployer une application Web liée à un référentiel GitHub

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application Web liée à un projet dans un référentiel GitHub. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez [Application Web liée au modèle GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Ce que vous allez déployer

Avec ce modèle, vous allez déployer une application Web qui contient le code d'un projet dans GitHub.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## Paramètres

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### repoURL

L'URL du référentiel GitHub qui contient le projet à déployer. Ce paramètre contient une valeur par défaut, mais cette valeur est uniquement destinée à vous montrer comment fournir l'URL du référentiel. Vous pouvez utiliser cette valeur lorsque vous testez le modèle, mais vous devrez fournir l'URL de votre propre référentiel lors de l'utilisation du modèle.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### branche

La branche du référentiel à utiliser lors du déploiement de l'application. La valeur par défaut est master, mais vous pouvez fournir le nom de n'importe quelle branche dans le référentiel que vous souhaitez déployer.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## Ressources à déployer

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Application web

Crée l'application Web qui est liée au projet dans GitHub.

Vous spécifiez le nom de l'application Web par le biais du paramètre **siteName** et l'emplacement de l'application Web par le biais du paramètre **siteLocation**. Dans l'élément **dependsOn**, le modèle définit l'application Web comme dépendante du plan d'hébergement de service. Étant donné qu'elle dépend du plan d'hébergement, l'application Web n'est pas créée tant que le plan d'hébergement est en cours de création. L'élément **dependsOn** est uniquement utilisé pour spécifier l'ordre de déploiement. Si vous ne marquez pas l'application Web comme dépendante du plan d'hébergement, Azure Resource Manager tentera de créer les deux ressources en même temps et vous pourriez recevoir une erreur si l'application Web est créée avant le plan d'hébergement.

L'application Web a également une ressource enfant qui est définie dans la section des **ressources** ci-dessous. Cette ressource enfant définit le contrôle de code source pour le projet déployé avec l'application Web. Dans ce modèle, le contrôle de code source est lié à un référentiel GitHub particulier. Le référentiel GitHub est défini avec le code **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** Vous pouvez coder en dur l'URL du référentiel lorsque vous souhaitez créer un modèle qui déploie de façon répétée un projet unique tout en nécessitant le nombre minimal de paramètres. Au lieu de coder en dur l'URL du référentiel, vous pouvez ajouter un paramètre pour l'URL du référentiel et utiliser cette valeur pour la propriété **RepoUrl**. Vous pouvez voir un exemple du paramètre URL du référentiel dans le [modèle Application Web avec tâches Web](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 

<!---HONumber=AcomDC_1217_2015-->