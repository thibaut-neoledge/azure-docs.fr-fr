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
ms.date: 04/13/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 652a8a0dcb3216e9b762b85de56673beda74edc4
ms.openlocfilehash: 83470aaf607a666a63bae5b9897d0b273e327377


---
# <a name="apply-security-and-policies-to-windows-vms-with-azure-resource-manager"></a>Appliquer la sécurité et des stratégies aux machines virtuelles Windows avec Azure Resource Manager
Avec les stratégies, une organisation peut appliquer différentes conventions et règles à travers l'entreprise. L’application du comportement souhaité peut vous aider à atténuer les risques tout en contribuant à la réussite de l'organisation. Dans cet article, nous allons décrire comment utiliser les stratégies d'Azure Resource Manager pour définir le comportement souhaité pour les machines virtuelles de votre organisation.

Les grandes lignes des étapes sont présentées ci-dessous.

1. Introduction aux stratégies Azure Resource Manager
2. Définition d’une stratégie pour votre machine virtuelle
3. Création de la stratégie
4. Application de la stratégie

## <a name="azure-resource-manager-policy-101"></a>Introduction aux stratégies Azure Resource Manager
Pour débuter avec les stratégies Azure Resource Manager, nous vous conseillons de lire l’article ci-dessous et de continuer avec les étapes de cet article. L'article ci-dessous décrit la définition de base et la structure d'une stratégie, la façon dont les stratégies sont évaluées, et divers exemples de définitions de stratégie.

* [Utiliser le service Policy pour gérer les ressources et contrôler l’accès](../azure-resource-manager/resource-manager-policy.md)

## <a name="define-a-policy-for-your-virtual-machine"></a>Définition d’une stratégie pour votre machine virtuelle
L'un des scénarios courants pour une entreprise peut consister à autoriser uniquement les utilisateurs à créer des machines virtuelles à partir de systèmes d'exploitation spécifiques dont la compatibilité avec une application métier a été vérifiée. À l'aide d'une stratégie Azure Resource Manager, cette tâche peut être accomplie en quelques étapes.
Dans cet exemple de stratégie, nous allons autoriser uniquement la création de machines virtuelles Windows Server 2012 R2 Datacenter. La définition de stratégie se présente comme suit

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
            "equals": "MicrosoftWindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageOffer",
            "equals": "WindowsServer"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/imageSku",
            "equals": "2012-R2-Datacenter"
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

La stratégie ci-dessus peut facilement être modifiée pour un scénario dans lequel vous souhaitez autoriser l’utilisation de n'importe quelle image Windows Server Datacenter pour un déploiement de machines virtuelles avec les modifications ci-dessous

```
{
  "field": "Microsoft.Compute/virtualMachines/imageSku",
  "like": "*Datacenter"
}
```

#### <a name="virtual-machine-property-fields"></a>Champs de propriété de la machine virtuelle
Le tableau ci-dessous décrit les propriétés de machine virtuelle qui peuvent être utilisées en tant que champs dans votre définition de stratégie. Pour plus d'informations sur les champs de stratégie, consultez l'article ci-dessous :

* [Champs et sources](../azure-resource-manager/resource-manager-policy.md#conditions)

| Nom du champ | Description |
| --- | --- |
| imagePublisher |Spécifie l’éditeur de l'image |
| imageOffer |Spécifie l'offre pour l'éditeur d'image sélectionné |
| imageSku |Spécifie le SKU de l’offre choisie |
| imageVersion |Spécifie la version de l’image du SKU choisi |

## <a name="create-the-policy"></a>Création de la stratégie
Une stratégie peut facilement être créée directement à l'aide de l'API REST ou avec des applets de commande PowerShell. Pour créer la stratégie, consultez l'article ci-dessous :

* [Création d’une stratégie](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)

## <a name="apply-the-policy"></a>Application de la stratégie
Après avoir créé la stratégie, vous devez l’appliquer sur une étendue définie. L’étendue peut être appliquée à un abonnement, à un groupe de ressources ou même à une ressource. Pour appliquer la stratégie, consultez l'article ci-dessous :

* [Création d’une stratégie](../azure-resource-manager/resource-manager-policy.md#create-and-assign-a-policy)



<!--HONumber=Jan17_HO4-->


