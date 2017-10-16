---
title: "Activer la réplication de machines virtuelles Hyper-V vers Azure (sans System Center VMM) avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour activer la réplication de machines virtuelles Hyper-V vers Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>Étape 10 : activer la réplication de machines virtuelles Hyper-V vers Azure


Cet article explique comment activer la réplication de machines virtuelles Hyper-V locales (non gérées par System Center VMM) à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, vérifiez que votre compte d’utilisateur Azure dispose des [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) requises pour activer la réplication d’une nouvelle machine virtuelle dans Azure.

## <a name="exclude-disks-from-replication"></a>Exclure les disques de la réplication

Par défaut, tous les disques d’une machine sont répliqués. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez ne pas répliquer les disques comportant des données temporaires, ou des données actualisées à chaque redémarrage d’une machine ou d’une application (par exemple, pagefile.sys ou tempdb dans SQL Server). [En savoir plus](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>Répliquer des machines virtuelles

Activez la réplication des machines virtuelles comme suit :          

1. Cliquez sur **Répliquer l’application** > **Source**. Après avoir configuré la réplication pour la première fois, vous pouvez cliquer sur l’option **+Répliquer** pour activer la réplication des machines supplémentaires.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. Dans **Source**, sélectionnez le site Hyper-V. Cliquez ensuite sur **OK**.
3. Dans **Cible**, sélectionnez l’abonnement de coffre et le modèle de basculement que vous souhaitez utiliser dans Azure (gestion des ressources ou classique) après le basculement.
4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous ne possédez pas de compte, vous pouvez en [créer un](#set-up-an-azure-storage-account). Cliquez ensuite sur **OK**.
5. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement.

    - Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer les paramètre réseau à l’ensemble des machines que vous activez pour la réplication.
    - Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine.
    - Si vous ne possédez pas de réseau, vous pouvez en [créer un](#set-up-an-azure-network). Sélectionnez un sous-réseau, le cas échéant. Cliquez ensuite sur **OK**.

   ![Activer la réplication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. Dans **Propriétés** > **Configurer les propriétés**, choisissez le système d’exploitation des machines virtuelles sélectionnées, ainsi que le disque du système d’exploitation.
8. Vérifiez que le nom de la machine virtuelle Azure (nom de la cible) est conforme à la [configuration requise pour les machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
9. Par défaut, tous les disques de la machine virtuelle sont sélectionnés pour la réplication. Pour exclure des disques, décochez-les.
10. Cliquez sur **OK** pour enregistrer les modifications. Vous pouvez opter pour une définition ultérieure des propriétés.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, sélectionnez la stratégie de réplication que vous souhaitez appliquer aux machines virtuelles protégées. Cliquez ensuite sur **OK**. Vous pouvez modifier la stratégie de réplication dans **Stratégies de réplication** > Nom de la stratégie > **Modifier les paramètres**. Les modifications que vous appliquez seront utilisées pour les nouvelles machines et les machines dont la réplication est déjà en cours.


   ![Activer la réplication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

Vous pouvez suivre la progression du travail **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.


## <a name="next-steps"></a>Étapes suivantes


Accédez à [Étape 11 : exécuter un test de basculement](hyper-v-site-walkthrough-test-failover.md).
