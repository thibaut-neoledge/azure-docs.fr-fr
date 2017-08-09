---
title: "Activer la réplication vers Azure pour les machines virtuelles Hyper-V dans les clouds VMM avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment activer la réplication vers Azure pour les machines virtuelles Hyper-V dans les clouds VMM avec le service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: b8a8bacd73ae9f6c7b7c982a18d55b8bd5d42c76
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>Étape 11 : Activer la réplication vers Azure de machines virtuelles Hyper-V dans les clouds VMM

Après avoir configuré la stratégie de réplication, utilisez cet article pour activer la réplication vers Azure de machines virtuelles Hyper-V locales gérées dans les clouds System Center Virtual Machine Manager (VMM) à l’aide du service [Azure Site Recovery](site-recovery-overview.md) sur le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

Assurez-vous que votre compte Azure dispose des [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) nécessaires pour créer des machines virtuelles Azure. [En savoir plus](../active-directory/role-based-access-built-in-roles.md) sur le contrôle d’accès en fonction du rôle dans Azure.

## <a name="exclude-disks-from-replication"></a>Exclure les disques de la réplication

Par défaut, tous les disques d’une machine sont répliqués. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez ne pas répliquer les disques comportant des données temporaires, ou des données actualisées à chaque redémarrage d’une machine ou d’une application (par exemple, pagefile.sys ou tempdb dans SQL Server). [En savoir plus](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>Répliquer des machines virtuelles

Activez la réplication des machines virtuelles comme suit :  

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**. Après avoir activé la réplication pour la première fois, cliquez sur l’option **+Répliquer** dans le coffre pour activer la réplication des autres machines.

    ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. Dans le panneau **Source**, sélectionnez le serveur VMM et le cloud hébergeant les hôtes Hyper-V. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. Dans **Cible**, sélectionnez l’abonnement, le modèle de déploiement suite au basculement et le compte de stockage utilisé pour les données répliquées.

    ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. Sélectionnez le compte de stockage que vous souhaitez utiliser. Si vous souhaitez utiliser un compte de stockage différent de ceux dont vous disposez, vous pouvez en [créer un](#set-up-an-azure-storage-account). Si vous utilisez un compte de stockage Premium pour les données répliquées, vous devez sélectionner un compte de stockage Standard supplémentaire, afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales. Pour créer un compte de stockage à l’aide du modèle Resource Manager, cliquez sur **Créer**. Si vous souhaitez créer un compte de stockage en mode Classic, vous pouvez le faire [dans le Portail Azure](../storage/storage-create-storage-account-classic-portal.md). Cliquez ensuite sur **OK**.
5. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine. Si vous souhaitez utiliser un réseau différent de ceux dont vous disposez, [créez-le](#set-up-an-azure-network). Pour créer un réseau en mode Resource Manager, cliquez sur **Créer**. Si vous souhaitez créer un réseau en suivant le modèle classique, utilisez le [Portail Azure](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). Sélectionnez un sous-réseau, le cas échéant. Cliquez ensuite sur **OK**.
6. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. Dans **Propriétés** > **Configurer les propriétés**, choisissez le système d’exploitation des machines virtuelles sélectionnées, ainsi que le disque du système d’exploitation.

    - Vérifiez que le nom de la machine virtuelle Azure (nom de la cible) est conforme à la [configuration requise pour les machines virtuelles Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).   
    - Par défaut, tous les disques de la machine virtuelle sont sélectionnés pour la réplication, mais vous pouvez décocher des disques afin de les exclure.

        - Vous pouvez exclure des disques pour réduire la bande passante de réplication. Par exemple, vous pouvez ne pas répliquer les disques contenant des données temporaires ou des données actualisées à chaque redémarrage d’une machine ou d’une application (telles que pagefile.sys ou tempdb dans Microsoft SQL Server). Vous pouvez exclure un disque de la réplication en le désélectionnant.
        - Vous ne pouvez exclure que des disques de base. Vous ne pouvez pas exclure de disques de système d’exploitation.
        - Nous vous recommandons de ne pas exclure de disques dynamiques. Site Recovery ne peut pas déterminer si un disque dur virtuel à l’intérieur d’une machine virtuelle invitée est un disque de base ou dynamique. Si tous les disques de volume dynamique dépendants ne sont pas exclus, le disque dynamique protégé s’affichera comme un disque défectueux lors du basculement de la machine virtuelle, et les données de ce disque ne seront pas accessibles.
        - Une fois la réplication activée, vous ne pouvez pas ajouter ni supprimer de disques pour la réplication. Si vous voulez ajouter ou exclure un disque, vous devez désactiver la protection de la machine virtuelle, puis la réactiver.
        - Les disques que vous créez manuellement dans Azure ne sont pas restaurés automatiquement. Par exemple, si vous basculez trois disques et que vous en créez deux directement dans une machine virtuelle Azure, seuls les trois disques qui ont été basculés seront restaurés automatiquement à partir d’Azure sur Hyper-V. Vous ne pouvez pas inclure de disques créés manuellement dans le processus de restauration automatique ou de réplication inverse d’Hyper-V vers Azure.
        - Si vous excluez un disque requis pour le bon fonctionnement d’une application, après le basculement vers Azure, vous devez le créer manuellement dans Azure afin que l’application répliquée puisse s’exécuter. Vous pouvez également intégrer Azure Automation dans un plan de récupération afin de créer le disque pendant le basculement de la machine.

    Cliquez sur **OK** pour enregistrer les modifications. Vous pouvez opter pour une définition ultérieure des propriétés.

    ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, sélectionnez la stratégie de réplication que vous souhaitez appliquer aux machines virtuelles protégées. Cliquez ensuite sur **OK**. Vous pouvez modifier la stratégie de réplication dans **Stratégies de réplication** > Nom de la stratégie > **Modifier les paramètres**. Les modifications appliquées sont utilisées pour les nouvelles machines et celles dont la réplication est en cours.

   ![Activer la réplication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

Vous pouvez suivre la progression du travail **Activer la protection** dans **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 12 : exécution d’un test de basculement](vmm-to-azure-walkthrough-test-failover.md)

