<properties
   pageTitle="Modèle Resource Manager pour le coffre de clés | Microsoft Azure"
   description="Affiche le schéma Resource Manager pour le déploiement de coffres de clés par le biais d'un modèle."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/04/2016"
   ms.author="tomfitz"/>

# Schéma d’un modèle de coffre de clés

Crée un coffre de clés.

## Format de schéma

Pour créer un coffre de clés, ajoutez le schéma suivant à la section des ressources de votre modèle.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
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

## Valeurs

Les tableaux suivants décrivent les valeurs que vous devez définir dans le schéma.

| Nom | Valeur |
| ---- | ---- | 
| type | Enum<br />Requis<br />**Microsoft.KeyVault/vaults**<br /><br />Le type de ressource à créer. |
| apiVersion | Enum<br />Requis<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />La version de l’API à utiliser pour la création de la ressource. | 
| name | String<br />requis<br />Un nom unique dans Azure.<br /><br />Le nom du coffre de clés à créer. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms afin de créer un nom unique, comme illustré dans l’exemple ci-dessous. |
| location | String<br />Requis<br />Une région valide pour les coffres de clés. Pour déterminer les régions valides, consultez [Régions prises en charge](resource-manager-supported-services.md#supported-regions).<br /><br />Région dans laquelle héberger le coffre de clés. |
| properties | Object<br />Requis<br />[properties object](#properties)<br /><br />Objet qui spécifie le type de coffre de clés à créer. |
| les ressources | Array<br />Facultatif<br />Valeurs autorisées : [Key vault secret resources](resource-manager-template-keyvault-secret.md)<br /><br />Ressources enfants pour le coffre de clés. |

<a id="properties" />
### objet propriétés

| Nom | Valeur |
| ---- | ---- | 
| enabledForDeployment | Boolean<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour le déploiement d’une machine virtuelle ou Service Fabric. |
| enabledForTemplateDeployment | Boolean<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour une utilisation dans les déploiements de modèle Resource Manager. Pour plus d'informations, consultez [Passage de valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Boolean<br />Facultatif<br />**true** ou **false**<br /><br />Spécifie si le coffre est activé pour le chiffrement de volume. |
| tenantId | String<br />Requis<br />**Globally-unique identifier**<br /><br />L'identificateur de locataire pour l'abonnement. Vous pouvez le récupérer à l’aide de l’applet de commande PowerShell [Get-AzureRMSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) ou de la ligne de commande Azure CLI **azure account show**. |
| accessPolicies | Array<br />Requis<br />[accessPolicies object](#accesspolicies)<br /><br />Un tableau contenant jusqu’à 16 objets spécifiant les autorisations de l'utilisateur ou du principal du service. |
| sku | Object<br />Requis<br />[sku object](#sku)<br /><br />Référence (SKU) du coffre de clés. |

<a id="accesspolicies" />
### Objet properties.accessPolicies

| Nom | Valeur |
| ---- | ---- | 
| tenantId | String<br />Requis<br />**Globally-unique identifier**<br /><br />Identificateur du locataire Azure Active Directory contenant le paramètre **objectId** de cette stratégie d'accès |
| objectId | String<br />Requis<br />**Globally-unique identifier**<br /><br />Identificateur d'objet de l'utilisateur ou du principal de service Azure Active Directory qui aura accès au coffre. Vous pouvez récupérer la valeur à l’aide de l’applet de commande PowerShell [Get-AzureRMADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou [Get-AzureRMADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx), ou de l’interface de ligne de commande CLI **azure ad user** ou **azure ad sp**. |
| Autorisations | Object<br />Requis<br />[permissions object](#permissions)<br /><br />Les autorisations accordées pour ce coffre à l'objet Active Directory. |

<a id="permissions" />
### Objet properties.accessPolicies.permissions

| Nom | Valeur |
| ---- | ---- | 
| clés | Array<br />Requis<br />**tout**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />Les autorisations accordées à cet objet Active Directory pour les clés de ce coffre. Cette valeur doit être spécifiée comme un tableau d'une ou plusieurs valeurs autorisées. |
| secrets | Array<br />Requis<br />**all**, **delete**, **get**, **list**, **set**<br /><br />Les autorisations accordées à cet objet Active Directory pour les clés secrètes de ce coffre. Cette valeur doit être spécifiée comme un tableau d'une ou plusieurs valeurs autorisées. |

<a id="sku" />
### Objet properties.sku

| Nom | Valeur |
| ---- | ---- | 
| name | Enum<br />Requis<br />**standard**, ou **premium** <br /><br />Le niveau de service du coffre de clés à utiliser. Supports standard des clés secrètes et clés protégées par un logiciel Le niveau Premium ajoute la prise en charge pour les clés protégées par HSM. |
| famille | Enum<br />Requis<br />**A** <br /><br />Famille de référence (SKU) à utiliser. |
 
	
## Exemples

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

## Modèles de démarrage rapide

Le modèle de démarrage rapide suivant déploie un coffre de clés.

- [Création d'un coffre de clés](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)


## Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d'une clé secrète de coffre de clés lors du déploiement, consultez [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0406_2016-->