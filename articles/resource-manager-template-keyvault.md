<properties
   pageTitle="Modèle Resource Manager pour le coffre de clés | Microsoft Azure"
   description="Montre le schéma de Resource Manager pour les coffres de clés."
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
   ms.date="12/15/2015"
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

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Oui | **Microsoft.KeyVault/vaults** | Type de ressource à créer. |
| apiVersion | enum | Oui | **2015-06-01** <br /> **2014-12-19-aperçu** | La version de l'API à utiliser pour la création de la ressource. | 
| name | string | Oui | | Le nom du coffre de clés à créer. Le nom doit être unique dans tout Azure. Envisagez d’utiliser la fonction [uniqueString](resource-group-template-functions.md#uniquestring) avec votre convention d’affectation de noms, comme illustré dans l’exemple ci-dessous. |
| location | string | Oui | Pour déterminer les régions valides, consultez [Régions prises en charge](resource-manager-supported-services.md#supported-regions). | Région dans laquelle héberger le coffre de clés. |
| properties | objet | Oui | ([voir ci-dessous](#properties)) | Objet qui spécifie le type de coffre de clés à créer. |
| les ressources | array | Non | [Clés secrètes de coffre de clés](resource-manager-template-keyvault-secret.md) | Ressources enfants pour le coffre de clés. |

<a id="properties" />
### objet propriétés

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | booléenne | Non | **true** ou **false** | Spécifie si le coffre est activé pour le déploiement d’une machine virtuelle ou Service Fabric. |
| enabledForTemplateDeployment | booléenne | Non | **true** ou **false** | Spécifie si le coffre est activé pour une utilisation dans les déploiements de modèle Resource Manager. Pour plus d'informations, consultez [Passage de valeurs sécurisés pendant le déploiement](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | booléenne | Non | **true** ou **false** | Spécifie si le coffre est activé pour le chiffrement de volume. |
| tenantId | string | Oui | Identificateur global unique | L'identificateur de client pour l'abonnement. Vous pouvez le récupérer à l’aide de l’applet de commande PowerShell **Get-AzureRMSubscription**. |
| accessPolicies | array | Oui | ([voir ci-dessous](#accesspolicies)) | Un tableau contenant jusqu’à 16 objets spécifiant les autorisations de l'utilisateur ou du principal du service. |
| sku | objet | Oui | ([voir ci-dessous](#sku)) | Référence (SKU) du coffre de clés. |

<a id="accesspolicies" />
### Objet properties.accessPolicies

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | string | Oui | Identificateur global unique | Identificateur du locataire Azure Active Directory contenant le paramètre **objectId** de cette stratégie d'accès |
| objectId | string | Oui | Identificateur global unique | Identificateur d'objet de l'utilisateur AAD ou du principal du service qui aura accès au coffre. Vous pouvez récupérer la valeur à partir de l’applet de commande **Get-AzureRMADUser** ou **Get-AzureRMADServicePrincipal**. |
| Autorisations | objet | Oui | ([voir ci-dessous](#permissions)) | Les autorisations accordées pour ce coffre à l'objet Active Directory. |

<a id="permissions" />
### Objet properties.accessPolicies.permissions

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| clés | array | Oui | Une liste séparée par des virgules contenant les valeurs suivantes : <br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | Les autorisations accordées à cet objet Active Directory pour les clés de ce coffre. Cette valeur doit être spécifiée comme un tableau de valeurs autorisées. |
| secrets | array | Oui | Une liste séparée par des virgules contenant les valeurs suivantes :<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | Les autorisations accordées à cet objet Active Directory pour les clés secrètes de ce coffre. Cette valeur doit être spécifiée comme un tableau de valeurs autorisées. |

<a id="sku" />
### Objet properties.sku

| Nom | Type | Requis | Valeurs autorisées | Description |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | Oui | **standard**<br />**premium** | Le niveau de service du coffre de clés à utiliser. Supports standard des clés secrètes et clés protégées par un logiciel Le niveau Premium ajoute la prise en charge pour les clés protégées par HSM. |
| famille | enum | Oui | **A** | Famille de référence (SKU) à utiliser. 
 
	
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
                "tags": { "displayName": "secret" },
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }


## Étapes suivantes

- Pour obtenir des informations générales sur les coffres de clés, consultez [Prise en main du coffre de clés Azure](./key-vault/key-vault-get-started.md).
- Pour obtenir un exemple de référencement d'une clé secrète de coffre de clés lors du déploiement, consultez [Passage de valeurs sécurisées lors du déploiement](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_1217_2015-->