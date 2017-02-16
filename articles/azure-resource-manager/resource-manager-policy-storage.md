---
title: "Stratégies d’Azure Resource Manager pour le stockage | Microsoft Docs"
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
ms.date: 01/19/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: ce31bbcb96a6afe19cf6c25dd9a68d6c2d4d080c
ms.openlocfilehash: f2141304f6db6d137065e06558e10c721b11f892


---
# <a name="apply-azure-resource-policies-to-storage-accounts"></a>Appliquer des stratégies Azure Resource Manager aux comptes de stockage
Grâce aux stratégies d’Azure Resource Manager, vous définissez des règles cohérentes décrivant les modalités de déploiement des ressources dans votre organisation. Vous créez des stratégies personnalisées pour vous assurer que les utilisateurs de votre organisation respectent les conventions nécessaires pour gérer les ressources de votre organisation. Cette rubrique présente plusieurs stratégies qui définissent les règles applicables aux comptes de stockage Azure. Pour plus d’informations sur les stratégies, consultez [Use resource policies to manage resources (Utiliser les stratégies de ressource pour gérer les ressources)](resource-manager-policy.md).

Les exemples de cette rubrique montrent des valeurs codées en dur dans la règle de stratégie. Toutefois, vous pouvez utiliser des paramètres pour transmettre les valeurs utilisées lors de l’attribution de la stratégie. Pour plus d’informations, consultez les [paramètres de la stratégie](resource-manager-policy.md#parameters).

## <a name="define-permitted-storage-account-types"></a>Définir les types de compte de stockage autorisés

La stratégie suivante restreint les [types de compte de stockage](../storage/storage-redundancy.md) qu’il est possible de déployer :

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

## <a name="define-permitted-access-tier"></a>Définir le niveau d’accès autorisé

La stratégie suivante spécifie le type de [niveau d’accès](../storage/storage-blob-storage-tiers.md) qu’il est possible de spécifier pour les comptes de stockage :

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

La stratégie suivante requiert que tous les comptes de stockage activent le [chiffrement des services de stockage](../storage/storage-service-encryption.md) :

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

## <a name="create-and-assign-policies"></a>Créer et attribuer une stratégie

Après avoir défini une règle de stratégie (comme indiqué dans les exemples précédents), vous devez créer la stratégie et lui attribuer une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour obtenir des exemples de création et d’affectation de stratégies, consultez [Créer et attribuer une stratégie](resource-manager-policy.md#create-and-assign-a-policy). 

## <a name="next-steps"></a>Étapes suivantes
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).




<!--HONumber=Jan17_HO3-->


