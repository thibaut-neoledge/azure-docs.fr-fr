<properties 
	pageTitle="Créer une application logique à l’aide de modèles Azure Resource Manager dans Azure App Service | Microsoft Azure" 
	description="Utiliser un modèle Azure Resource Manager pour déployer une application logique vide pour la définition de workflows." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="deonhe"/>

# Créer une application logique à l'aide d'un modèle

Utilisez un modèle Azure Resource Manager pour créer une application logique vide qui peut être utilisée pour définir des workflows. Vous pouvez définir les ressources à déployer et les paramètres qui sont spécifiés lors de l’exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements, ou le personnaliser afin qu’il réponde à vos besoins.

Pour plus d'informations sur les propriétés de l'application logique, consultez l'[API de gestion du flux de travail d'application logique](https://msdn.microsoft.com/library/azure/mt643788.aspx).

Pour obtenir des exemples sur la définition proprement dite, consultez [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

Pour plus d'informations sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez le [modèle d'application logique](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## Ce que vous allez déployer

Avec ce modèle, vous déployez une application logique.

Pour exécuter automatiquement le déploiement, sélectionnez le bouton ci-dessous :

[![Déploiement sur Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## Paramètres

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Ressources à déployer

### Application logique

Crée l'application logique

Le modèle utilise une valeur de paramètre pour le nom de l'application logique. Il définit l'emplacement de l'application logique sur le même emplacement que le groupe de ressources.

Cette définition spécifique s’exécute une fois par heure et exécute une commande ping sur l’emplacement spécifié dans le paramètre **testUri**.

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## Commandes pour l’exécution du déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=AcomDC_0727_2016-->