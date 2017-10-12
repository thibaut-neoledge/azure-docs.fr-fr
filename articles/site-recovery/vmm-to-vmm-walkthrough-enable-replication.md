---
title: "Activation de la réplication Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment activer la réplication de machines virtuelles Hyper-V sur un site secondaire System Center VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>Étape 9 : Activer la réplication sur un site secondaire pour des machines virtuelles Hyper-V


Après avoir configuré une stratégie de réplication, utilisez cet article pour activer la réplication sur un site System Center Virtual Machine Manager (VMM) secondaire pour des machines virtuelles Hyper-V locales, à l’aide [d’Azure Site Recovery](site-recovery-overview.md).

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="select-vms-to-replicate"></a>Sélectionner les machines virtuelles à répliquer

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. 

    ![Activer la réplication](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. Dans **Source**, sélectionnez le serveur VMM et le cloud hébergeant les hôtes Hyper-V à répliquer. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. Dans **Cible**, vérifiez le serveur VMM secondaire et le cloud.
4. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez protéger dans la liste.

    ![Activer la protection pour les machines virtuelles](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

Vous pouvez suivre la progression de l’action **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Lorsque le travail de **finalisation de la protection** est terminé, la réplication initiale est également terminée et la machine virtuelle est prête à être basculée.

Notez les points suivants :

- Vous pouvez également activer la protection des machines virtuelles dans la console VMM. Cliquez sur **Activer la protection** dans la barre d’outils dans les propriétés de la machine virtuelle > onglet **Azure Site Recovery**.
- Une fois que vous avez activé la réplication, vous pouvez afficher les propriétés de la machine virtuelle dans **Éléments répliqués**. Dans le tableau de bord **Essentials**, vous pouvez voir des informations sur la stratégie de réplication pour la machine virtuelle et son état. Cliquez sur **Propriétés** pour obtenir plus de détails.

## <a name="onboard-existing-vms"></a>Intégrer des machines virtuelles existantes

Si vous avez des machines virtuelles existantes dans VMM qui sont répliquées à l’aide du réplica Hyper-V, vous pouvez les intégrer à la réplication Azure Site Recovery comme suit :

1. Vérifiez que le serveur Hyper-V qui héberge la machine virtuelle se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle de réplication se trouve dans le cloud secondaire.
2. Assurez-vous que la stratégie de réplication est configurée pour le cloud VMM principal.
3. Activez la réplication de la machine virtuelle principale. Azure Site Recovery et VMM s’assurent que la même machine virtuelle et le même hôte de réplication seront détectés, et Azure Site Recovery réutilisera et rétablira la réplication à l’aide des paramètres spécifiés.


## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 10 : Exécuter un test de basculement](vmm-to-vmm-walkthrough-test-failover.md).
