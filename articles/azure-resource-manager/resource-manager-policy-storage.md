---
title: "Stratégies de ressources Azure pour les comptes de stockage | Microsoft Docs"
description: "Décrit les stratégies d’Azure Resource Manager pour gérer le déploiement de comptes de stockage."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/05/2017
ms.author: tomfitz
ms.openlocfilehash: 6612ee61f5c50e743241b92030660cea7ae7094d
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/29/2017
---
# <a name="apply-resource-policies-to-storage-accounts"></a>Appliquer des stratégies de ressources à des comptes de stockage
Cette rubrique montre plusieurs [stratégies de ressources](resource-manager-policy.md) que vous pouvez appliquer aux comptes de stockage Azure. Ces stratégies garantissent la cohérence entre les comptes de stockage déployés dans votre organisation. 

## <a name="define-permitted-storage-account-types"></a>Définir les types de compte de stockage autorisés

La stratégie suivante restreint les [types de compte de stockage](../storage/common/storage-redundancy.md) qu’il est possible de déployer :

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/sku.name",
          "in": [
            "Standard_LRS",
            "Standard_GRS"
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Une règle de stratégie similaire avec un paramètre permettant d’accepter les références SKU autorisées est disponible sous la forme d’une définition de stratégie prédéfinie. La stratégie prédéfinie possède l’ID de ressource `/providers/Microsoft.Authorization/policyDefinitions/7433c107-6db4-4ad1-b57a-a76dce0154a1`. 

## <a name="define-permitted-access-tier"></a>Définir le niveau d’accès autorisé

La stratégie suivante spécifie le type de [niveau d’accès](../storage/blobs/storage-blob-storage-tiers.md) qu’il est possible de spécifier pour les comptes de stockage :

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="ensure-encryption-is-enabled"></a>Vérifier que le chiffrement est activé

La stratégie suivante requiert que tous les comptes de stockage activent le [chiffrement des services de stockage](../storage/common/storage-service-encryption.md) :

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/enableBlobEncryption",
          "equals": "true"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Cette règle de stratégie est également disponible sous forme de définition de stratégie prédéfinie avec l’ID de ressource `/providers/Microsoft.Authorization/policyDefinitions/7c5a74bf-ae94-4a74-8fcf-644d1e0e6e6f`.

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](resource-manager-policy-create-assign.md). 
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).

