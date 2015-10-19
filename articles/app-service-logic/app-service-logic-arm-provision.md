<properties 
	pageTitle="Créer une application logique" 
	description="Utiliser un modèle Azure Resource Manager pour déployer une application logique vide pour la définition de workflows." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>

# Créer une application logique à l'aide d'un modèle

Dans cette rubrique, vous allez apprendre à créer un modèle Azure Resource Manager pour créer une application logique vide qui peut être utilisée pour définir des workflows. Vous allez apprendre comment définir les ressources qui sont déployées et comment définir les paramètres qui sont spécifiés lors de l'exécution du déploiement. Vous pouvez utiliser ce modèle pour vos propres déploiements ou le personnaliser pour répondre à vos besoins.

Pour plus d'informations sur les propriétés de l'application logique, consultez l'[API de gestion du flux de travail d'application logique](https://msdn.microsoft.com/library/azure/dn948513.aspx). Pour obtenir des exemples sur la définition proprement dite, consultez [Créer des définitions d'application logique](app-service-logic-author-definitions.md).

Pour plus d'informations sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager](../resource-group-authoring-templates.md).

Pour le modèle complet, consultez le [modèle d'application logique](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## Ce que vous allez déployer

Avec ce modèle, vous allez déployer une application logique.

Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous :

[![Déploiement sur Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## Paramètres

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Ressources à déployer

### Plan App Service

Crée un plan de service d'application.

Il utilise le même emplacement que le groupe de ressources sur lequel il est déployé.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### Application logique

Crée l'application logique

Le modèle utilise une valeur de paramètre pour le nom de l'application logique. Il définit l'emplacement de l'application logique sur le même emplacement que le groupe de ressources.

Cette définition spécifique s'exécute une fois par heure et exécute une commande ping sur l'emplacement spécifié dans le paramètre **testUri**.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Commandes pour exécuter le déploiement

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interface de ligne de commande Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=Oct15_HO2-->