---
title: "Configuration du mappage réseau pour la réplication vers Azure de machines virtuelles Hyper-V dans les clouds VMM avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment configurer le mappage réseau lors de la réplication vers Azure de machines virtuelles Hyper-V dans les clouds VMM avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>Étape 9 : Configurer le mappage réseau pour la réplication Hyper-V (avec VMM) vers Azure

Après avoir configuré les [paramètres de réplication source et cible](vmm-to-azure-walkthrough-source-target.md), utilisez cet article pour configurer le mappage réseau pour mapper des réseaux de machines virtuelles VMM locaux et des réseaux Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

- En savoir plus sur le [mappage réseau](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Préparez VMM pour le mappage réseau](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Vérifiez que les machines virtuelles se trouvant sur le serveur VMM sont connectées à un réseau de machines virtuelles et que vous avez créé au moins un réseau virtuel Azure. Plusieurs réseaux de machines virtuelles peuvent être mappés au même réseau Azure.

## <a name="configure-mapping"></a>Configuration du mappage

Configurez le mappage comme suit :

1. Dans **Infrastructure Site Recovery** > **Mappages réseau** > **Mappage réseau**, cliquez sur l’icône**+Mappage réseau**.

    ![Mappage réseau](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. Dans la zone **Ajouter un mappage réseau**, sélectionnez le serveur VMM source et choisissez **Azure** comme cible.
3. Vérifiez l’abonnement et le modèle de déploiement après le basculement.
4. Dans **Réseau source**, choisissez le réseau de machines virtuelles local source à mapper à partir de la liste associée au serveur VMM.
5. Dans **Réseau cible**, choisissez le réseau Azure dans lequel les machines virtuelles réplica Azure se trouveront, une fois créées. Cliquez ensuite sur **OK**.

    ![Mappage réseau](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Voici le processus exécuté lorsque le mappage réseau démarre :

* Les machines virtuelles qui existent dans le réseau de machines virtuelles source sont connectées au réseau cible, lorsque le mappage commence. Les nouvelles machines virtuelles connectées au réseau de machines virtuelles source sont connectées au réseau Azure mappé, lors de la réplication.
* Si vous modifiez un mappage réseau existant, les machines virtuelles de réplication se connectent à l’aide des nouveaux paramètres.
* Si le réseau cible est associé à plusieurs sous-réseaux et que l’un d’eux présente le même nom que le sous-réseau dans lequel se trouve la machine virtuelle source, la machine virtuelle de réplication se connecte à ce sous-réseau cible après le basculement.
* S’il n’existe aucun sous-réseau cible avec un nom correspondant, la machine virtuelle se connecte au premier sous-réseau du réseau.



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 10 : Créer une stratégie de réplication](vmm-to-azure-walkthrough-replication.md)

