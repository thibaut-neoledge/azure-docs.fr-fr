---
title: "Mappage de réseaux pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment mapper des réseaux lors de la réplication de machines virtuelles Hyper-V sur un site secondaire VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>Étape 7 : Mapper des réseaux pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire


Après avoir configuré les [paramètres source et cible](vmm-to-vmm-walkthrough-source-target.md) pour la réplication de machines virtuelles Hyper-V sur un site System Center Virtual Machine Manager (VMM) secondaire, utilisez cet article pour configurer le mappage réseau pour la réplication d’une machine virtuelle Hyper-V sur un site secondaire, à l’aide [d’Azure Site Recovery](site-recovery-overview.md).

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

- En savoir plus sur le [mappage réseau](vmm-to-vmm-walkthrough-network.md#network-mapping-overview) avant de démarrer.
- Vérifiez que les machines virtuelles sur les serveurs VMM sont connectées à un réseau de machines virtuelles.

## <a name="configure-network-mapping"></a>Configurer le mappage réseau

1. Dans **Mappage réseau** > **Mappages réseau**, cliquez sur **+Mappage réseau**.
2. Dans l’onglet **Ajouter un mappage réseau**, sélectionnez les serveurs VMM source et cible. Les réseaux de machines virtuelles associés aux serveurs VMM sont récupérés.
3. Dans la zone **Réseau source**, sélectionnez le réseau à utiliser dans la liste de réseaux de machines virtuelles associés au serveur VMM principal.
4. Dans **Réseau cible**, sélectionnez le réseau que vous souhaitez utiliser sur le serveur VMM secondaire. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

Voici le processus exécuté lorsque le mappage réseau démarre :

* Toute machine virtuelle de réplication existante qui correspond au réseau de machines virtuelles source sera connectée aux réseaux de machines virtuelles cibles.
* Les nouvelles machines virtuelles qui sont connectées au réseau de machines virtuelles source seront connectées au réseau mappé cible après la réplication.
* Si vous modifiez un mappage existant avec un nouveau réseau, les machines virtuelles de réplication seront connectées à l'aide des nouveaux paramètres.
* Remarque : si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, l’ordinateur virtuel de réplication est connecté à ce sous-réseau cible après le basculement. S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle sera connectée au premier sous-réseau du réseau.



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 8 : Configurer une stratégie de réplication](vmm-to-vmm-walkthrough-replication.md).