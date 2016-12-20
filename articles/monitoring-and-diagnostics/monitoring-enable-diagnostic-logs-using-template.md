---
title: "Activer automatiquement les paramètres de diagnostic à l’aide d’un modèle Resource Manager | Microsoft Docs"
description: "Découvrez comment utiliser un modèle Resource Manager pour créer des paramètres de diagnostic qui activeront la diffusion en continu de vos journaux de diagnostic vers Event Hubs ou leur stockage dans un compte de stockage."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a8a88a8c-4a48-4df6-8f7e-d90634d39c57
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 30b023429cfdc671ac68175f94ffb48379c58dda


---
# <a name="automatically-enable-diagnostic-settings-at-resource-creation-using-a-resource-manager-template"></a>Activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager
Dans cet article, nous vous expliquons comment utiliser un [modèle Azure Resource Manager](../resource-group-authoring-templates.md) pour configurer les paramètres de diagnostic d’une ressource lors de sa création. Cela vous permet de démarrer automatiquement la diffusion en continu de vos journaux de diagnostic et des mesures vers Event Hubs, leur archivage dans un compte de stockage ou leur envoi à Log Analytics lorsqu’une ressource est créée.

La méthode d’activation des journaux de diagnostic à l’aide d’un modèle Resource Manager varie selon le type de ressource.

* Les ressources **Non-Compute** (comme Network Security Groups, Logic Apps, Automation) utilisent les [paramètres de diagnostic décrits dans cet article](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
* **de calcul** (basées sur WAD/LAD) utilisent le [fichier de configuration de WAD/LAD décrit dans cet article](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Dans cet article, nous expliquons comment configurer les diagnostics à l’aide de deux méthodes.

Procédure de base :

1. Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer la ressource et activer les diagnostics.
2. [Déployez le modèle à l’aide de n’importe quelle méthode de déploiement](../resource-group-template-deploy.md).

Ci-dessous, nous vous donnons un exemple de modèle de fichier JSON que vous devez générer pour les ressources de calcul et non liées au calcul.

## <a name="non-compute-resource-template"></a>Modèle de ressource non liée au calcul
Pour les ressources non liées au calcul, vous devrez effectuer les deux opérations suivantes :

1. Ajouter des paramètres à l’objet Blob de paramètres pour le nom de compte de stockage, l’ID de règle de Service Bus et/ou l’ID d’espace de travail OMS Log Analytics (activation de l’archivage des journaux de diagnostic dans un compte de stockage, de la diffusion en continu des journaux vers Event Hubs et/ou de l’envoi de journaux à Log Analytics).
   
    ```json
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId":{
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
    ```
2. Ajouter une ressource de type `[resource namespace]/providers/diagnosticSettings`dans le tableau de ressources de la ressource pour laquelle vous souhaitez activer les journaux de diagnostic.
   
    ```json
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "Microsoft.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
          "workspaceId": "[parameters('workspaceId')]",
          "logs": [ 
            {
              "category": "/* log category name */",
              "enabled": true,
              "retentionPolicy": {
                "days": 0,
                "enabled": false
              }
            }
          ]
        }
      }
    ]
    ```

L’objet blob de propriétés pour le paramètre de diagnostic suit [le format décrit dans cet article](https://msdn.microsoft.com/library/azure/dn931931.aspx).

Voici un exemple complet qui crée un groupe de sécurité réseau et active la diffusion en continu vers Event Hubs et le stockage dans un compte de stockage.

```json

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nsgName": {
      "type": "string",
      "metadata": {
        "description": "Name of the NSG that will be created."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Storage Account in which Diagnostic Logs should be saved."
      }
    },
    "serviceBusRuleId": {
      "type": "string",
      "metadata": {
        "description": "Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
      }
    },
    "workspaceId": {
      "type": "string",
      "metadata": {
        "description": "Log Analytics workspace ID for the Log Analytics workspace to which logs will be sent."
      }
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('nsgName')]",
      "apiVersion": "2016-03-30",
      "location": "westus",
      "properties": {
        "securityRules": []
      },
      "resources": [
        {
          "type": "providers/diagnosticSettings",
          "name": "Microsoft.Insights/service",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('nsgName'))]"
          ],
          "apiVersion": "2015-07-01",
          "properties": {
            "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
            "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
            "workspaceId": "[parameters('workspaceId')]",
            "logs": [
              {
                "category": "NetworkSecurityGroupEvent",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              },
              {
                "category": "NetworkSecurityGroupRuleCounter",
                "enabled": true,
                "retentionPolicy": {
                  "days": 0,
                  "enabled": false
                }
              }
            ]
          }
        }
      ],
      "dependsOn": []
    }
  ]
}

```

## <a name="compute-resource-template"></a>Modèle de ressource de calcul
Pour activer les diagnostics pour une ressource de calcul, par exemple un cluster Service Fabric ou une machine virtuelle, vous devez :

1. Ajouter l’extension Azure Diagnostics à la définition de ressource de machine virtuelle.
2. Spécifier un compte de stockage et/ou un Event Hub comme paramètre.
3. Ajouter le contenu de votre fichier WADCfg XML dans la propriété XMLCfg, en échappant correctement tous les caractères XML.

> [!WARNING]
> Cette dernière étape peut être difficile à réaliser. [Consultez cet article](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) pour obtenir un exemple qui montre comment fractionner le schéma de configuration de diagnostics en variables échappées et mises en forme correctement.
> 
> 

L’intégralité du processus, y compris des exemples, est décrite [dans ce document](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
* [Stream Azure Diagnostic Logs to Event Hubs (Diffuser en continu les journaux de diagnostic Azure vers Event Hubs)](monitoring-stream-diagnostic-logs-to-event-hubs.md)




<!--HONumber=Nov16_HO3-->


