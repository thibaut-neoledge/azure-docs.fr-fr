---
title: "Créer une alerte de journal d’activité avec un modèle Resource Manager | Microsoft Docs"
description: "Soyez informé lorsque vos ressources Azure sont créées."
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Créer une alerte de journal d’activité avec un modèle Resource Manager
Cet article explique comment vous pouvez utiliser un [modèle Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) pour configurer les alertes de journal d’activité. Cela vous permet de configurer automatiquement des alertes sur vos ressources lorsqu’elles sont créées dans le cadre de votre processus de déploiement automatisé.

Procédure de base :

1.    Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer l’alerte de journal d’activité.
2.    [Déployez le modèle à l’aide de n’importe quelle méthode de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Ci-dessous, nous décrivons comment créer un modèle Resource Manager, d’abord pour une alerte de journal d’activité uniquement, puis pour une alerte de journal d’activité lors de la création d’une autre ressource.

## <a name="resource-manager-template-for-an-activity-log-alert"></a>Modèle Resource Manager pour une alerte de journal d’activité
Pour créer une alerte de journal d’activité à l’aide d’un modèle Resource Manager, vous créez une ressource de type `microsoft.insights/activityLogAlerts` et renseignez toutes les propriétés. Voici un modèle qui crée une alerte de journal d’activité.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-03-01-preview",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [alertes](monitoring-overview-alerts.md)  
[Ajouter des groupes d’actions à l’aide d’un modèle Resource Manager](monitoring-create-action-group-with-resource-manager-template.md)

