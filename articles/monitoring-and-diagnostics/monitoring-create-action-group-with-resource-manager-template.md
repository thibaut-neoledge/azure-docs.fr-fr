---
title: "Créer des groupes d’actions avec les modèles Resource Manager | Microsoft Docs"
description: "Les groupes d’actions permettent de vous informer par le biais de webhooks sous forme d’e-mail, de SMS ou d’appel lorsque certains événements se produisent."
author: anirudhcavale
manager: orenr
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
ms.translationtype: HT
ms.sourcegitcommit: cddb80997d29267db6873373e0a8609d54dd1576
ms.openlocfilehash: 88e7b2e7781b80ea360531f4c3a45256de83b594
ms.contentlocale: fr-fr
ms.lasthandoff: 07/18/2017

---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Créer un groupe d’actions avec un modèle Resource Manager
Cet article explique comment vous pouvez utiliser un [modèle Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) pour configurer les groupes d’action. Les modèles vous permettent de configurer automatiquement des groupes d’actions sur vos ressources lorsqu’elles sont créées pour vous assurer que toutes les parties concernées reçoivent une notification lorsqu’une alerte est déclenchée.

Procédure de base :

1.  Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer le groupe d’actions.
2.  [Déployez le modèle à l’aide de n’importe quelle méthode de déploiement](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Ci-dessous, nous décrivons comment créer un modèle Resource Manager pour un groupe d’actions où les définitions d’action sont codées en dur dans le modèle, puis pour un modèle qui se sert des informations de configuration Webhook en tant que paramètres d’entrée lorsque vous déployez le modèle.

## <a name="resource-manager-template-for-an-action-group"></a>Modèle Resource Manager pour un groupe d’actions

Pour créer un groupe d’actions à l’aide d’un modèle Resource Manager, vous créez une ressource de type `Microsoft.Insights/actionGroups` et renseignez toutes les propriétés. Voici quelques exemples de modèles qui créent un groupe d’actions.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2017-04-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [groupes d’actions](monitoring-action-groups.md)  
En savoir plus sur les [alertes](monitoring-overview-alerts.md)  
[Ajouter des alertes à l’aide d’un modèle Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)

