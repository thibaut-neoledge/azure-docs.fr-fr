---
title: "Appliquer la sécurité avec des stratégies sur des machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Comment appliquer une stratégie à une machine virtuelle Azure Resource Manager Windows"
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0b71ba54-01db-43ad-9bca-8ab358ae141b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: kasing
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 9874a825ea81ebb191710ebd46dceb70c1f20e60
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017


---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Appliquer des stratégies aux machines virtuelles Windows avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous expliquons comment utiliser les stratégies d’Azure Resource Manager afin de définir le comportement souhaité pour les machines virtuelles de votre entreprise.

Pour une introduction aux stratégies, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](../../azure-resource-manager/resource-manager-policy.md).

## <a name="define-policy-for-permitted-virtual-machines"></a>Définition d’une stratégie pour les machines virtuelles autorisées
Pour vous assurer que les machines virtuelles de votre entreprise sont compatibles avec une application, vous pouvez limiter les systèmes d’exploitation autorisés. Dans l’exemple de stratégie suivant, vous autorisez uniquement la création de machines virtuelles Windows Server 2012 R2 Datacenter :

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
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-Datacenter"
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

Utilisez un caractère générique pour modifier la stratégie précédente afin qu’elle autorise toutes les images Windows Server Datacenter :

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Pour plus d’informations sur les champs de la stratégie, consultez les [alias de stratégie](../../azure-resource-manager/resource-manager-policy.md#aliases).

## <a name="define-policy-for-using-managed-disks"></a>Définir une stratégie d’utilisation des disques gérés

Pour exiger l’utilisation de disques gérés, employez la stratégie suivante :

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

