---
title: "Appliquer la sécurité avec des stratégies sur des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Comment appliquer une stratégie à une machine virtuelle Azure Resource Manager Linux"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 06778ab4-f8ff-4eed-ae10-26a276fc3faa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: singhkay
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: c1ad80a56627695f3594f4d9b60cd623fa9bcce3
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-linux-vms-with-azure-resource-manager"></a>Appliquer des stratégies aux machines virtuelles Linux avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous expliquons comment utiliser les stratégies d’Azure Resource Manager pour définir le comportement souhaité pour les machines virtuelles de votre organisation.

Pour une introduction aux stratégies, consultez [Utiliser une stratégie pour gérer les ressources et le contrôle d’accès](../../azure-resource-manager/resource-manager-policy.md).

## <a name="define-policy-for-permitted-virtual-machines"></a>Définition d’une stratégie pour les machines virtuelles autorisées
Pour vous assurer que les ordinateurs virtuels de votre entreprise sont compatibles avec une application, vous pouvez limiter les systèmes d’exploitation autorisés. Dans l’exemple de stratégie suivant, vous autorisez uniquement la création de machines virtuelles Ubuntu 14.04.2-LTS.

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/disks",
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "Canonical"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "UbuntuServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "14.04.2-LTS"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
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

Utilisez un caractère générique pour modifier la stratégie précédente afin qu’elle autorise toutes les images Ubuntu LTS : 

```json
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

Pour plus d’informations sur les champs de la stratégie, consultez les [alias de stratégie](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="define-policy-for-using-managed-disks"></a>Définition d’une stratégie d’utilisation des disques gérés

Pour exiger l’utilisation de disques gérés, utilisez la stratégie suivante :

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes
* Après avoir défini une règle de stratégie (comme le montrent les exemples précédents), vous devez créer la définition de stratégie et l’attribuer à une étendue. L’étendue peut être un abonnement, un groupe de ressources ou une ressource. Pour affecter des stratégies via le portail, consultez [Utiliser le portail Azure pour affecter et gérer les stratégies de ressources](../../azure-resource-manager/resource-manager-policy-portal.md). Pour affecter des stratégies via l’API REST, PowerShell ou Azure CLI, consultez [Affecter et gérer des stratégies via un script](../../azure-resource-manager/resource-manager-policy-create-assign.md).
* Pour une introduction aux stratégies de ressources, consultez la page [Vue d’ensemble des stratégies de ressources](../../azure-resource-manager/resource-manager-policy.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](../../azure-resource-manager/resource-manager-subscription-governance.md).

