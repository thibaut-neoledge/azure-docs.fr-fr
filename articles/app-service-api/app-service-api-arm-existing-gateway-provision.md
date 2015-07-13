<properties 
	pageTitle="Déployer une application API avec une passerelle existante" 
	description="Utilisez un modèle Azure Resource Manager pour déployer une application API qui utilise une passerelle et un plan App Service existants." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>

# Provisionner une application API avec une passerelle existante

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager qui déploie une application API Microsoft Azure et une passerelle existante. Vous allez apprendre comment définir les ressources à déployer et configurer les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour plus d'informations sur le déploiement d'applications, consultez la rubrique [Déployer une application complexe de manière prévisible dans Microsoft Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).

Pour accéder au modèle complet, voir [Application API avec le nouveau modèle de passerelle](../../templates/app-service-api-arm-existing-gateway-provision/).

## Ce que vous allez déployer

Dans ce modèle, vous allez déployer une application API associée à une passerelle et à un plan d’hébergement App Service existants.

## Paramètres

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

Identificateur du plan d’hébergement existant.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Paramètres du plan d’hébergement existant.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## Variables

Ce modèle définit une variable, utilisée lors du déploiement des ressources.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Cette valeur est utilisée ci-dessous, en tant que **variables('IdPackage')**.

## Ressources à déployer

### Application web pour héberger l’application API

Crée une application web qui héberge l’application API.

Vous pouvez voir que le paramètre **kind** est défini sur **apiApp**, ce qui indique au portail Microsoft Azure que cette application web héberge une passerelle. Le portail masque l’application web du panneau de navigation de l’application web. L’application comprend une extension pour installer le package d’application API vide par défaut. Un lien est défini entre l’application API et l’application d’hébergement web. La section Paramètres de l’application inclut les valeurs requises pour l’hébergement de l’application API.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### Application API

Créez l’application API.

Notez que les noms de la passerelle et de l’application web d’hébergement sont définis en tant que propriétés dans l’application API.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json


 

<!---HONumber=62-->