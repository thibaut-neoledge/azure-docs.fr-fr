---
title: "Comment utiliser l’identité du service administré Azure dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment utiliser l’identité du service administré Azure dans Gestion des API"
services: api-management
documentationcenter: 
author: miaojiang
manager: anneta
editor: 
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: 70bf207cc173caf7d8cae3c4c9111ee2f427405b
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2017
---
# <a name="how-to-use-azure-managed-service-identity-in-azure-api-management"></a>Comment utiliser l’identité du service administré Azure dans Gestion des API Azure

> [!Note]
> L’identité du service administré pour Gestion des API Azure est actuellement disponible en préversion.

Cette rubrique vous montre comment créer une identité de service administré pour une instance de service Gestion des API et comment accéder à d’autres ressources. Une identité de service administré générée par Azure Active Directory (AAD) permet à votre instance Gestion des API d’accéder facilement et en toute sécurité aux autres ressources protégées par AAD telles qu’Azure Key Vault. Gérée par Azure, cette identité de service administré ne vous oblige pas à approvisionner ou permuter des secrets. Pour plus d’informations sur l’identité du service administré Azure, consultez la [vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md).

## <a name="creating-an-api-management-instance-with-an-identity-using-an-azure-resource-manager-template"></a>Création d’une instance Gestion des API avec une identité à l’aide d’un modèle Azure Resource Manager

Vous pouvez créer une instance Gestion des API avec une identité en incluant la propriété suivante dans la définition de la ressource. 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ce code indique à Azure de créer et de manager l’identité de votre instance Gestion des API. 

Par exemple, un modèle complet peut se présenter comme suit :

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "serviceName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The name of the api management service"
            }
        },
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "admin@contoso.com",
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "minLength": 1,
            "defaultValue": "Contoso",
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": [
                "Developer",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "[parameters('serviceName')]",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "[parameters('sku')]",
                "capacity": "[parameters('skuCount')]"
            },
            "properties": {
                "publisherEmail": "[parameters('publisherEmail')]",
                "publisherName": "[parameters('publisherName')]"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```

## <a name="obtaining-a-certificate-from-azure-key-vault"></a>Obtention d’un certificat à partir d’Azure Key Vault

L’exemple ci-dessous montre comment obtenir un certificat auprès d’Azure Key Vault. Il contient les étapes suivantes :

* Créer une instance Gestion des API avec une identité
* Mettre à jour les stratégies d’accès d’une instance Azure Key Vault et permettre à l’instance Gestion des API d’obtenir des secrets à partir de cette dernière
* Mettre à jour l’instance Gestion des API en définissant un nom de domaine personnalisé à l’aide d’un certificat obtenu à partir de l’instance Key Vault

> [!Important]
> Si la version de l’objet du certificat n’est pas fournie, le service Gestion des API obtiendra automatiquement la version la plus récente du certificat après son chargement dans Key Vault. 

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur l’identité du service administré Azure
  * [Vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md)
  * [Voir d’autres modèles Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates)

