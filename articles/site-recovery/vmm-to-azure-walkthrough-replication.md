---
title: "Configuration d’une stratégie de réplication pour la réplication des machines virtuelles Hyper-V (avec VMM) vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit comment configurer une stratégie de réplication pour la réplication des machines virtuelles Hyper-V (avec VMM) vers Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 1bf66eaa272ad1e2bf400707929243662eb1f140
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>Étape 10 : Configurer une stratégie de réplication pour la réplication de machines virtuelles Hyper-V (avec VMM) vers Azure


Après avoir configuré le [mappage réseau](vmm-to-azure-walkthrough-network-mapping.md), utilisez cet article pour configurer une stratégie de réplication pour répliquer des machines virtuelles Hyper-V locales gérées dans les clouds System Center Virtual Machine Manager (VMM) vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) sur le portail Azure.

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="create-a-policy"></a>Création d’une stratégie

1. Pour créer une stratégie de réplication, cliquez sur **Préparer l’infrastructure** > **Paramètres de réplication** > **+Créer et associer**.

    ![Réseau](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. Dans **Créer et associer une stratégie**, indiquez le nom de la stratégie.
3. Dans **Fréquence de copie**, spécifiez la fréquence selon laquelle répliquer les données delta après la réplication initiale (toutes les 30 secondes ou toutes les 5 ou 15 minutes).

    > [!NOTE]
    >  Une fréquence de 30 secondes n’est pas prise en charge lors de la réplication sur un Stockage Premium. La limitation est déterminée par le nombre d’instantanés par objet blob (100) pris en charge par le Stockage Premium. [En savoir plus](../storage/storage-premium-storage.md#snapshots-and-copy-blob)

4. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines protégées peuvent être récupérées à tout moment pendant cette fenêtre temporelle.
5. Dans **Fréquence des captures instantanées cohérentes de l’application**, spécifiez la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications (entre 1 et 12 heures). Hyper-V utilise deux types d’instantanés : un instantané standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérent avec l'application qui prend un instantané des données d'application d'une machine virtuelle. Les instantanés cohérents avec l'application utilisent le service VSS (Volume Shadow Copy Service) pour s'assurer que les applications sont dans un état cohérent lors de la prise des instantanés. Notez que si vous activez les instantanés cohérents avec l'application, cela affectera les performances des applications exécutées sur les machines virtuelles sources. Assurez-vous que la valeur définie est inférieure au nombre de points de récupération supplémentaires que vous configurez.
6. Dans **Heure de début de la réplication initiale**, indiquez à quel moment démarrer la réplication initiale. La réplication se produit via votre bande passante Internet. Il est donc préférable de prévoir son exécution en dehors des heures de bureau.
7. Dans **Chiffrer les données stockées sur Azure**, indiquez si les données au repos doivent être chiffrées dans Azure Storage. Cliquez ensuite sur **OK**.

    ![Stratégie de réplication](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. Lorsque vous créez une stratégie, elle est automatiquement associée au cloud VMM. Cliquez sur **OK**. Vous pouvez associer des clouds VMM supplémentaires (ainsi que les machines virtuelles correspondantes) à cette stratégie de réplication en cliquant sur **Réplication** > Nom de la stratégie > **Associer un Cloud VMM**.

    ![Stratégie de réplication](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 11 : activer la réplication](vmm-to-azure-walkthrough-enable-replication.md)

