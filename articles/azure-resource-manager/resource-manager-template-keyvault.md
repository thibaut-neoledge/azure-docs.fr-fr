---
title: "Modèle Resource Manager pour le coffre de clés | Microsoft Docs"
description: "Affiche le schéma Resource Manager pour le déploiement de coffres de clés par le biais d&quot;un modèle."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>Schéma d’un modèle de coffre de clés
Crée un coffre de clés.

## <a name="schema-format"></a>Format de schéma
Pour créer un coffre de clés, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## <a name="values"></a>Valeurs
Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| --- | --- |
| type |Enum<br />Requis<br />**Microsoft.KeyVault/vaults**<br /><br />Le type de ressource à créer. |
| apiVersion |Enum<br />Requis<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />La version de l’API à utiliser pour la création de la ressource. |
| Nom |String<br />Requis<br />Un nom unique dans Azure.<br /><br />Nom du coffre de clés à créer. Vous pouvez utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms afin de créer un nom unique, comme illustré dans l’exemple ci-dessous. |
| location |String<br />Requis<br />Région valide pour les coffres de clés. Pour déterminer les régions valides, consultez la rubrique [Régions prises en charge](resource-manager-supported-services.md#supported-regions).<br /><br />Région dans laquelle héberger le coffre de clés. |
| properties |Object<br />Requis<br />[objet propriétés](#properties)<br /><br />Objet qui spécifie le type de coffre de clés à créer. |
| les ressources |Tableau<br />Facultatif<br />Valeurs autorisées : [ressources de secrets Key Vault](resource-manager-template-keyvault-secret.md)<br /><br />Ressources enfants pour le coffre de clés. |

<a id="properties" />

### <a name="properties-object"></a>objet propriétés
| Nom | Valeur |
| --- | --- |
| enabledForDeployment |Booléen<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour le déploiement d’une machine virtuelle ou Service Fabric. |
| enabledForTemplateDeployment |Booléen<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour une utilisation dans les déploiements de modèle Resource Manager. Pour plus d'informations, consultez [Passage de valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md) |
| enabledForDiskEncryption |Boolean<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour le chiffrement de volume. |
| tenantId |String<br />Requis<br />**Identificateur global unique**<br /><br />Identificateur de client pour l’abonnement. Vous pouvez le récupérer à l’aide de l’applet de commande [PowerShell Get-AzureRmSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou de la commande d’interface de ligne de commande (CLI) Azure **azure account show** . |
| accessPolicies |Tableau<br />Requis<br />[objet accessPolicies](#accesspolicies)<br /><br />Tableau contenant jusqu’à 16 objets qui spécifient les autorisations de l’utilisateur ou du principal du service. |
| sku |Object<br />Requis<br />[objet SKU](#sku)<br /><br />Référence (SKU) du coffre de clés. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>Objet properties.accessPolicies
| Nom | Valeur |
| --- | --- |
| tenantId |String<br />Requis<br />**Identificateur global unique**<br /><br />Identificateur du client Azure Active Directory contenant le paramètre **objectId** de cette stratégie d’accès. |
| objectId |String<br />Requis<br />**Identificateur global unique**<br /><br />Identificateur d’objet de l’utilisateur ou du principal de service Azure Active Directory qui aura accès au coffre. Vous pouvez récupérer la valeur à l’aide de l’applet de commande PowerShell [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx), ou de la commande d’interface de ligne de commande (CLI) Azure **azure ad user** ou **azure ad sp**. |
| Autorisations |Object<br />Requis<br />[objet permissions](#permissions)<br /><br />Autorisations accordées pour ce coffre à l’objet Active Directory. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>Objet properties.accessPolicies.permissions
| Nom | Valeur |
| --- | --- |
| clés |Tableau<br />Requis<br />**toutes**, **sauvegarder**, **créer**, **déchiffrer**, **supprimer**, **chiffrer**, **obtenir**, **importer**, **lister**, **restaurer**, **signer**, **ne pas inclure la clé**, **mettre à jour**, **vérifier**, **inclure la clé**<br /><br />Autorisations accordées à cet objet Active Directory pour les clés de ce coffre. Cette valeur doit être spécifiée comme un tableau d'une ou plusieurs valeurs autorisées. |
| secrets |Tableau<br />Requis<br />**toutes**, **supprimer**, **obtenir**, **lister**, **définir**<br /><br />Autorisations accordées à cet objet Active Directory pour les clés secrètes de ce coffre. Cette valeur doit être spécifiée comme un tableau d'une ou plusieurs valeurs autorisées. |

<a id="sku" />

### <a name="propertiessku-object"></a>Objet properties.sku
| Nom | Valeur |
| --- | --- |
| Nom |Enum<br />Requis<br />**standard**, ou **premium** <br /><br />Le niveau de service du coffre de clés à utiliser.  Supports standard des clés secrètes et clés protégées par un logiciel  Le niveau Premium ajoute la prise en charge pour les clés protégées par HSM. |
| famille |Enum<br />Requis<br />**A** <br /><br />Famille de référence (SKU) à utiliser. |

## <a name="examples"></a>Exemples
L'exemple suivant déploie un coffre de clés et la clé secrète.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForDiskEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## <a name="quickstart-templates"></a>Modèles de démarrage rapide
Le modèle de démarrage rapide suivant déploie un coffre de clés.

* [Création d'un coffre de clés](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main d’Azure Key Vault](../key-vault/key-vault-get-started.md).
* Pour obtenir un exemple de référencement d'une clé secrète de coffre de clés lors du déploiement, consultez [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).


