---
title: "Migrer des réseaux virtuels associés à ExpressRoute d’un déploiement classique vers Resource Manager - Azure : PowerShell | Microsoft Docs"
description: "Cette page explique comment migrer des réseaux virtuels associés vers Resource Manager après avoir déplacé votre circuit."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 964ea38569062a7127f60dd6309b328db263bf6f
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017

---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrer des réseaux virtuels associés à ExpressRoute d’un déploiement classique vers Resource Manager

Cet article explique comment migrer des réseaux virtuels associés à ExpressRoute du modèle de déploiement classique au modèle de déploiement Azure Resource Manager après avoir déplacé votre circuit ExpressRoute. 


## <a name="before-you-begin"></a>Avant de commencer
* Vérifiez que vous disposez de la dernière version des modules Azure PowerShell. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Vous devez avoir bien compris les limites et les limitations.
* Vérifiez que le circuit est totalement opérationnel dans le modèle de déploiement classique.
* Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.
* Passez en revue la documentation de migration de ressources suivante :

    * [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Passer en revue les erreurs les plus courantes de la migration et leur prévention](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scénarios pris en charge et non pris en charge

* Il est possible de migrer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans aucun temps d’arrêt. Vous pouvez déplacer n’importe quel circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager sans temps d’arrêt. Suivez les instructions relatives au [déplacement des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell](expressroute-howto-move-arm.md). C’est une condition requise pour déplacer des ressources connectées au réseau virtuel.
* Il est possible de migrer les réseaux virtuels, les passerelles et les déploiements associés au sein du réseau virtuel qui sont attachés à un circuit ExpressRoute se trouvant dans le même abonnement vers l’environnement Resource Manager sans le moindre temps d’arrêt. Vous pouvez suivre les étapes décrites plus loin pour migrer des ressources telles que des réseaux virtuels, des passerelles et des machines virtuelles déployées au sein du réseau virtuel. Vous devez vous assurer que les réseaux virtuels sont configurés correctement avant de les migrer. 
* La migration des réseaux virtuels, des passerelles et des déploiements associés au sein du réseau virtuel qui ne se trouvent pas dans le même abonnement que le circuit ExpressRoute nécessite un temps d’arrêt. La dernière section du document décrit les étapes à suivre pour migrer des ressources.
* Un réseau virtuel doté d’une passerelle ExpressRoute et d’une passerelle VPN ne peut pas être migré.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Déplacer un circuit ExpressRoute d’un déploiement classique vers Resource Manager
Vous devez déplacer un circuit ExpressRoute de l’environnement classique à l’environnement Resource Manager avant d’essayer de migrer des ressources attachées au circuit ExpressRoute. Pour accomplir cette tâche, consultez les articles suivants :

* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md).
* [Migrer un circuit d’un déploiement classique vers Resource Manager à l’aide d’Azure PowerShell](expressroute-howto-move-arm.md).
* Utilisez le portail de gestion des services Azure. Vous pouvez suivre le flux de travail pour [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et sélectionner l’option d’importation. 

Cette opération n’implique pas de temps d’arrêt. Vous pouvez continuer à transférer des données entre votre site et Microsoft pendant la migration.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrer des réseaux virtuels, des passerelles et les déploiements associés

Les étapes à suivre pour migrer varient selon que vos ressources se trouvent dans le même abonnement, dans des abonnements différents ou les deux.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrer les réseaux virtuels, les passerelles et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute
Cette section décrit les étapes à suivre pour migrer un réseau virtuel, une passerelle et les déploiements associés se trouvant dans le même abonnement que le circuit ExpressRoute. Cette migration n’implique aucun temps d’arrêt. Vous pouvez continuer à utiliser toutes les ressources pendant le processus de migration. Le plan de gestion est verrouillé pendant la migration. 

1. Assurez-vous que le circuit ExpressRoute a été déplacé de l’environnement classique à l’environnement Resource Manager.
2. Assurez-vous que le réseau virtuel a été préparé correctement pour la migration.
3. Inscrivez votre abonnement pour la migration de ressources. Pour inscrire votre abonnement pour la migration de ressources, utilisez l’extrait de code PowerShell suivant :

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Effectuez la validation, la préparation et la migration. Pour déplacer le réseau virtuel, utilisez l’extrait de code PowerShell suivant :

  ```powershell
  Move-AzureVirtualNetwork -Prepare $vnetName  
  Move-AzureVirtualNetwork -Commit $vnetName
  ```

  Vous pouvez également abandonner la migration en exécutant l’applet de commande PowerShell suivante :

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Étapes suivantes
* [Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Étude technique approfondie de la migration prise en charge par la plateforme de ressources Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [FAQ : Migration prise en charge par la plateforme de ressources IaaS Classic vers Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Passer en revue les erreurs les plus courantes de la migration et leur prévention](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

