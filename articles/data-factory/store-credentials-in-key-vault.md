---
title: "Stocker des informations d’identification dans Azure Key Vault | Microsoft Docs"
description: "Découvrez comment stocker les informations d’identification des magasins de données utilisées par un coffre de clés Azure, qui peuvent être récupérées automatiquement par Azure Data Factory au moment de l’exécution."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fc8b4f1ecf664c7db5ab2e535245dee90415fa65
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="store-credential-in-azure-key-vault"></a>Stocker des informations d’identification dans Azure Key Vault

Vous pouvez stocker les informations d’identification des magasins de données dans un coffre de clés [Azure Key Vault](../key-vault/key-vault-whatis.md). Azure Data Factory récupère les informations d’identification lors de l’exécution d’une activité qui utilise le magasin de données.

> [!NOTE]
> Pour le moment, seul le [connecteur Dynamics](connector-dynamics-crm-office-365.md) prend en charge cette fonctionnalité. 

## <a name="steps"></a>Étapes

Lorsque vous créez une fabrique de données, une identité de service est créée en même temps que la fabrique. L’identité du service est une application managée qui est inscrite auprès d’Azure Active Directory et qui représente la fabrique de données en question. Les informations sur l’identité du service se trouvent sur le portail Azure sous : votre fabrique de données -> Propriétés : 

- ID de l’identité du service
- Locataire de l’identité du service
- ID d’application de l’identité du service

Pour référencer des informations d’identification stockées dans Azure Key Vault, vous devez :

1. Copier l’ID d’application de l’identité du service généré avec la fabrique de données.
2. Autoriser l’identité du service à accéder à votre coffre de clés Azure Key Vault. Dans votre coffre de clés -> Contrôle d’accès -> Ajouter -> recherchez l’ID d’application de l’identité du service pour lui attribuer une autorisation de lecture. Cela permet à la fabrique désignée d’accéder au secret du coffre de clés.
3. Créer un service lié pointant vers votre coffre de clés Azure Key Vault. Reportez-vous à la section [Service lié Azure Key Vault](#azure-key-vault-linked-service).
4. Créer un service lié de magasin de données, dans lequel référencer le secret correspondant qui est stocké dans le coffre de clés. Reportez-vous à la section [Référencer les informations d’identification stockées dans le coffre de clés](#reference-credential-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Service lié Azure Key Vault

Les propriétés suivantes sont prises en charge pour le service lié Azure Key Vault :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type doit être définie sur **AzureKeyVault**. | Oui |
| baseUrl | Spécifiez l’URL d’Azure Key Vault (nom DNS). | Oui |

**Exemple :**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Référencer les informations d’identification stockées dans le coffre de clés

Les propriétés suivantes sont prises en charge lorsque vous configurez un champ dans le service lié qui référence un secret de coffre de clés :

| Propriété | Description | Requis |
|:--- |:--- |:--- |
| type | La propriété de type du champ doit être définie sur **AzureKeyVaultSecret**. | Oui |
| secretName | Nom du secret dans le coffre de clés Azure. | Oui |
| secretVersion | Version du secret dans le coffre de clés Azure.<br/>Si elle n’est pas spécifiée, la version la plus récente du secret est utilisée.<br/>Si elle est spécifiée, elle utilise la version spécifiée.| Non |
| store | Fait référence au service lié Azure Key Vault que vous utilisez pour stocker les informations d’identification. | Oui |

**Exemple : (voir la section « password »)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs par l’activité de copie dans Azure Data Factory, consultez le tableau [Magasins de données pris en charge](copy-activity-overview.md##supported-data-stores-and-formats).
