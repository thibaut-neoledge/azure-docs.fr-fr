---
title: "Créer une application logique à l’aide d’un modèle dans Azure | Microsoft Docs"
description: "Utiliser un modèle Azure Resource Manager pour déployer une application logique pour la définition de workflows."
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2f407a428aa176cc5c2a3b6bb236b522bda5ab64
ms.openlocfilehash: 1305fdc4cf87905eef701ec5471d6329006c2b5f


---
# <a name="create-a-logic-app-using-a-template"></a>Créer une application logique à l'aide d'un modèle
Les modèles constituent un moyen rapide d’utiliser des connecteurs différents au sein d’une application logique. Les applications logiques comportent des modèles Azure Resource Manager qui vous permettent de créer une application logique à mettre à profit pour la définition de workflows professionnels. Vous pouvez définir les ressources à déployer et la méthode de définition des paramètres lors du déploiement de votre application logique. Vous pouvez utiliser ce modèle pour vos propres scénarios professionnels, ou le personnaliser selon vos besoins.

Pour plus d'informations sur les propriétés de l'application logique, consultez l' [API de gestion du flux de travail d'application logique](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Pour obtenir des exemples sur la définition proprement dite, consultez [Créer des définitions d'application logique](logic-apps-author-definitions.md). 

Pour plus d'informations sur la création de modèles, consultez la rubrique [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pour le modèle complet, consultez le [modèle d'application logique](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-deploy"></a>Que déployer ?
Avec ce modèle, vous déployez une application logique.

Pour exécuter automatiquement le déploiement, sélectionnez le bouton ci-dessous :  

[![Déploiement sur Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Paramètres
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>Ressources à déployer
### <a name="logic-app"></a>Application logique
Crée l'application logique

Le modèle utilise une valeur de paramètre pour le nom de l'application logique. Il définit l'emplacement de l'application logique sur le même emplacement que le groupe de ressources. 

Cette définition spécifique s’exécute une fois par heure et exécute une commande ping sur l’emplacement spécifié dans le paramètre **testUri** . 

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


## <a name="commands-to-run-deployment"></a>Commandes pour l’exécution du déploiement
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Interface de ligne de commande Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup






<!--HONumber=Jan17_HO5-->


