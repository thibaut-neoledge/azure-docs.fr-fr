<properties
	pageTitle="Activer automatiquement les paramètres de diagnostic à l’aide d’un modèle Resource Manager | azure.microsoft.com/ Azure"
	description="Découvrez comment utiliser un modèle Resource Manager pour créer des paramètres de diagnostic qui activeront la diffusion en continu de vos journaux de diagnostic vers Event Hubs ou leur stockage dans un compte de stockage."
	authors="johnkemnetz"
	manager="rboucher"
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/17/2016"
	ms.author="johnkem"/>

# Activer automatiquement les paramètres de diagnostic lors de la création de ressources à l’aide d’un modèle Resource Manager
Dans cet article, nous vous expliquons comment utiliser un [modèle Azure Resource Manager](../resource-group-authoring-templates.md) pour configurer les paramètres de diagnostic d’une ressource lors de sa création. Cela vous permet de démarrer automatiquement la diffusion en continu de vos journaux de diagnostic et des mesures vers Event Hubs ou leur archivage dans un compte de stockage lorsqu’une ressource est créée.

La méthode d’activation des journaux de diagnostic à l’aide d’un modèle Resource Manager varie selon le type de ressource.

- Les ressources **non liées au calcul** (p. ex. les groupes de sécurité réseau, les applications logiques, Automation) utilisent les [paramètres de diagnostic décrits dans cet article](./monitoring-overview-of-diagnostic-logs.md#diagnostic-settings).
- Les ressources **de calcul** (basées sur WAD/LAD) utilisent le [fichier de configuration de WAD/LAD décrit dans cet article](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md).

Dans cet article, nous expliquons comment configurer les diagnostics à l’aide de deux méthodes.

Procédure de base :

1. Créez un modèle sous la forme d’un fichier JSON qui décrit comment créer la ressource et activer les diagnostics.
2. [Déployez le modèle à l’aide de n’importe quelle méthode de déploiement](../resource-group-template-deploy.md).

Ci-dessous, nous vous donnons un exemple de modèle de fichier JSON que vous devez générer pour les ressources de calcul et non liées au calcul.

## Modèle de ressource non liée au calcul
Pour les ressources non liées au calcul, vous devrez effectuer les deux opérations suivantes :

1. Ajouter des paramètres à l’objet blob de paramètres pour le nom de compte de stockage et l’ID de règle de Service Bus (activation de l’archivage des journaux de diagnostic dans un compte de stockage et/ou de la diffusion en continu des journaux vers Event Hubs).

    ```
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
    }
    ```
2. Ajouter une ressource de type `[resource namespace]/providers/diagnosticSettings` dans le tableau de ressources de la ressource pour laquelle vous souhaitez activer les journaux de diagnostic.

    ```
    "resources": [
      {
        "type": "providers/diagnosticSettings",
        "name": "azure.microsoft.com/.Insights/service",
        "dependsOn": [
          "[/*resource Id for which Diagnostic Logs will be enabled>*/]"
        ],
        "apiVersion": "2015-07-01",
        "properties": {
          "storageAccountId": "[resourceId('azure.microsoft.com/.Storage/storageAccounts', parameters('storageAccountName'))]",
          "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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

L’objet blob de propriétés pour le paramètre de diagnostic suit [le format décrit dans cet article](https://msdn.azure.microsoft.com/.com/library/azure/dn931931.aspx).

Voici un exemple complet qui crée un groupe de sécurité réseau et active la diffusion en continu vers Event Hubs et le stockage dans un compte de stockage.

```
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
				"description":"Name of the Storage Account in which Diagnostic Logs should be saved."
			}
		},
		"serviceBusRuleId": {
			"type": "string",
			"metadata": {
				"description":"Service Bus Rule Id for the Service Bus Namespace in which the Event Hub should be created or streamed to."
			}
		}
    },
    "variables": {},
    "resources": [
        {
            "type": "azure.microsoft.com/.Network/networkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "apiVersion": "2016-03-30",
            "location": "westus",
            "properties": {
                "securityRules": []
            },
            "resources": [
				{
					"type": "providers/diagnosticSettings",
					"name": "azure.microsoft.com/.Insights/service",
					"dependsOn": [
						"[resourceId('azure.microsoft.com/.Network/networkSecurityGroups', parameters('nsgName'))]"
					],
					"apiVersion": "2015-07-01",
					"properties": {
						"storageAccountId": "[resourceId('azure.microsoft.com/.Storage/storageAccounts', parameters('storageAccountName'))]",
                        "serviceBusRuleId": "[parameters('serviceBusRuleId')]",
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

## Modèle de ressource de calcul
Pour activer les diagnostics pour une ressource de calcul, par exemple un cluster Service Fabric ou une machine virtuelle, vous devez :

1. Ajouter l’extension Azure Diagnostics à la définition de ressource de machine virtuelle.
2. Spécifier un compte de stockage et/ou un Event Hub comme paramètre.
3. Ajouter le contenu de votre fichier WADCfg XML dans la propriété XMLCfg, en échappant correctement tous les caractères XML.

> [AZURE.WARNING] Cette dernière étape peut être difficile à réaliser. [Consultez cet article](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md#diagnostics-configuration-variables) pour obtenir un exemple qui montre comment fractionner le schéma de configuration de diagnostics en variables échappées et mises en forme correctement.

L’intégralité du processus, y compris des exemples, est décrite [dans ce document](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md).


## Étapes suivantes
- [En savoir plus sur les journaux de diagnostic Azure](./monitoring-overview-of-diagnostic-logs.md)
- [Stream Azure Diagnostic Logs to Event Hubs](./monitoring-stream-diagnostic-logs-to-event-hubs.md) (Diffuser en continu les journaux de diagnostic Azure vers Event Hubs)

<!---HONumber=AcomDC_0817_2016-->