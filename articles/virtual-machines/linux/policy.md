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
ms.date: 04/13/2016
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: b48a4e2fa913b865cf4b57693ef281e446541328
ms.lasthandoff: 04/04/2017


---
# <a name="apply-security-and-policies-to-linux-vms-with-azure-resource-manager"></a>Appliquer la sécurité et des stratégies aux machines virtuelles Linux avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous allons décrire comment utiliser les stratégies d'Azure Resource Manager pour définir le comportement souhaité pour les machines virtuelles de votre organisation.

Les grandes lignes des étapes sont présentées ci-dessous.

1. Introduction aux stratégies Azure Resource Manager
2. Définition d’une stratégie pour votre machine virtuelle
3. Création de la stratégie
4. Application de la stratégie

## <a name="azure-resource-manager-policy-101"></a>Introduction aux stratégies Azure Resource Manager
Pour débuter avec les stratégies Azure Resource Manager, nous vous conseillons de lire l’article ci-dessous et de continuer avec les étapes de cet article. L'article ci-dessous décrit la définition de base et la structure d'une stratégie, la façon dont les stratégies sont évaluées, et divers exemples de définitions de stratégie.

* [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](../../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Définition d’une stratégie pour votre machine virtuelle
L'un des scénarios courants pour une entreprise peut consister à autoriser uniquement les utilisateurs à créer des machines virtuelles à partir de systèmes d'exploitation spécifiques dont la compatibilité avec une application métier a été vérifiée. À l'aide d'une stratégie Azure Resource Manager, cette tâche peut être accomplie en quelques étapes.
Dans cet exemple de stratégie, nous allons autoriser uniquement la création de machines virtuelles Ubuntu 14.04.2-LTS. La définition de stratégie se présente comme suit

```
"if": {
  "allOf": [
    {
      "field": "type",
      "equals": "Microsoft.Compute/virtualMachines"
    },
    {
      "not": {
        "allOf": [
          {
            "field": "Microsoft.Compute/virtualMachines/imagePublisher",
            "equals": "Canonical"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "UbuntuServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "14.04.2-LTS"
          }
        ]
      }
    }
  ]
},
"then": {
  "effect": "deny"
}
```

La stratégie ci-dessus peut facilement être modifiée pour un scénario dans lequel vous souhaitez autoriser l’utilisation de n'importe quelle image Ubuntu LTS pour un déploiement de machines virtuelles avec les modifications ci-dessous

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*LTS"
}
```

#### <a name="virtual-machine-property-fields"></a>Champs de propriété de la machine virtuelle
Le tableau ci-dessous décrit les propriétés de machine virtuelle qui peuvent être utilisées en tant que champs dans votre définition de stratégie. Pour plus d’informations sur les champs de stratégies, consultez [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](../../resource-manager-policy.md).

| Nom du champ | Description |
| --- | --- |
| imagePublisher |Spécifie l’éditeur de l'image |
| imageOffer |Spécifie l'offre pour l'éditeur d'image sélectionné |
| imageSku |Spécifie le SKU de l’offre choisie |
| imageVersion |Spécifie la version de l’image du SKU choisi |

## <a name="create-the-policy"></a>Création de la stratégie
Une stratégie peut facilement être créée directement à l'aide de l'API REST ou avec des applets de commande PowerShell. Vous pouvez en savoir plus sur la [création et l’affectation d’une stratégie](../../resource-manager-policy.md).

## <a name="apply-the-policy"></a>Application de la stratégie
Après avoir créé la stratégie, vous devez l’appliquer sur une étendue définie. L’étendue peut être appliquée à un abonnement, à un groupe de ressources ou même à une ressource. Vous pouvez en savoir plus sur la [création et l’affectation d’une stratégie](../../resource-manager-policy.md).

