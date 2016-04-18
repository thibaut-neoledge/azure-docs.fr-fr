<properties
   pageTitle="Modèle Resource Manager pour une clé secrète dans un coffre de clés | Microsoft Azure"
   description="Affiche le schéma Resource Manager pour le déploiement de clés secrètes de coffres de clés par le biais d'un modèle."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Schéma d’un modèle de clé secrète de coffre de clés

Crée une clé secrète stockée dans un coffre de clés. Ce type de ressource est souvent déployé comme ressource enfant d’un [coffre de clés](resource-manager-template-keyvault.md).

## Format de schéma

Pour créer une clé secrète de coffre de clés, ajoutez le schéma suivant à votre modèle. La clé secrète peut être définie comme une ressource enfant d'un coffre de clés ou comme une ressource de niveau supérieur. Vous pouvez la définir comme une ressource enfant lorsque le coffre de clés est déployé dans le même modèle. Vous devez définir la clé secrète comme une ressource de niveau supérieur lorsque le coffre de clés n'est pas déployé dans le même modèle, ou lorsque vous créez plusieurs clés secrètes en effectuant une boucle sur le type de ressource.

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type | Enum<br />Requis<br />**secrets** (si déployé comme une ressource enfant d’un coffre de clés) ou <br /> **Microsoft.KeyVault/vaults/secrets** (si déployé comme une ressource de niveau supérieur)<br /><br />The Type de ressource à créer. |
| apiVersion | Enum<br />Requis<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />La version de l’API à utiliser pour la création de la ressource. | 
| name | String<br />Requis<br />Mot unique si déployé comme une ressource enfant d’un coffre de clés, ou au format **{key-vault-name}/{secret-name}** si déployé comme une ressource de niveau supérieur à ajouter à un coffre de clés existant.<br /><br />Nom de la clé secrète à créer. |
| properties | Object<br />Requis<br />[properties object](#properties)<br /><br />Objet qui spécifie la valeur de la clé secrète à créer. |
| dependsOn | Array<br />Facultatif<br />Une liste séparée par des virgules de noms de ressource ou d'identificateurs de ressource uniques.<br /><br />La collection de ressources dont dépend ce lien. Si le coffre de clés pour la clé secrète est déployé dans le même modèle, ajoutez le nom du coffre de clés à cet élément pour vous assurer qu'il est d'abord déployé. |

<a id="properties" />
### objet propriétés

| Nom | Valeur |
| ---- | ---- | 
| value | String<br />Requis<br /><br />Valeur de clé secrète à stocker dans le coffre de clés. Lors du passage d'une valeur pour cette propriété, utilisez un paramètre de type **securestring**. |

	
## Exemples

Le premier exemple déploie une clé secrète comme une ressource enfant d'un coffre de clés.

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
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
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
            "enableVaultForVolumeEncryption": {
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
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
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

Le deuxième exemple déploie la clé secrète comme une ressource de niveau supérieur stockée dans un coffre de clés existant.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
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
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d'une clé secrète de coffre de clés lors du déploiement, consultez [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0406_2016-->