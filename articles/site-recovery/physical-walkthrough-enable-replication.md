---
title: "Activer la réplication des serveurs physiques vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour activer la réplication des serveurs virtuels vers Azure à l’aide du service Azure Site Recovery"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 519c5559-7032-4954-b8b5-f24f5242a954
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 42f35c53eec06a346281fd90c97aecfd2269307d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="step-10-enable-replication-for-physical-servers-to-azure"></a>Étape 10 : activer la réplication des serveurs physiques vers Azure


Cet article explique comment activer la réplication des serveurs physiques Windows/Linux locaux vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

- [L’installation du composant Service mobilité](physical-walkthrough-install-mobility.md) sur les serveurs est requise.
- Si une machine virtuelle est prête pour l’installation Push, le serveur de processus installe automatiquement le service Mobilité lorsque vous activez la réplication.
- Lorsque vous activez une machine pour la réplication, votre compte d’utilisateur Azure doit disposer [d’autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) spécifiques pour que vous puissiez utiliser le stockage Azure et créer des machines virtuelles Azure.
- Lorsque vous ajoutez ou modifiez des adresses IP de serveurs, il peut être nécessaire d’attendre 15 minutes ou plus avant que les modifications n’entrent en vigueur et qu’elles apparaissent dans le portail.


## <a name="exclude-disks-from-replication"></a>Exclure les disques de la réplication

Par défaut, tous les disques d’une machine sont répliqués. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez ne pas répliquer les disques comportant des données temporaires, ou des données actualisées à chaque redémarrage d’une machine ou d’une application (par exemple, pagefile.sys ou tempdb dans SQL Server). [En savoir plus](site-recovery-exclude-disk.md)

## <a name="replicate-servers"></a>Réplication des serveurs

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**.
2. Dans **Source**, sélectionnez le serveur de configuration.
3. Dans **Type de machine**, sélectionnez **Machines physiques**.
4. Sélectionnez le serveur de processus. Si vous n’avez pas créé de serveur de processus supplémentaire, il s’agit du serveur de configuration. Cliquez ensuite sur **OK**.
5. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles basculées.
6. Sélectionnez le compte de stockage Azure que vous souhaitez utiliser pour les données de réplication. Si vous ne souhaitez pas utiliser un compte que vous avez déjà configuré, vous pouvez en créer un nouveau.
7. Sélectionnez le **Réseau Azure** et le **Sous-réseau** auxquels les machines virtuelles Azure se connectent après le basculement. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine. Si vous ne souhaitez pas utiliser un réseau existant, vous pouvez en créer un.

    ![Activer la réplication](./media/physical-walkthrough-enable-replication/targetsettings.png)

8. Dans **Machines physiques**, cliquez sur **+Machine physique**, puis entrez le **Nom** et l’**Adresse IP**. Choisissez le système d’exploitation de la machine que vous souhaitez répliquer. Quelques minutes sont nécessaires pour la découverte des machines et leur affichage dans la liste.
9. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte utilisé par le serveur de processus pour installer automatiquement le service Mobilité sur la machine.
10. Par défaut, tous les disques sont répliqués. Cliquez sur **Tous les disques** et désélectionnez ceux qui ne doivent pas être répliqués. Cliquez ensuite sur **OK**. Vous pouvez configurer des propriétés de machine virtuelle supplémentaires ultérieurement.

    ![Activer la réplication](./media/physical-walkthrough-enable-replication/enable-replication6.png)
11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée. Si vous modifiez une stratégie, les modifications seront appliquées à la machine en cours de réplication et aux nouvelles machines.
12. Cochez la case **Cohérence multimachine virtuelle** pour regrouper les machines dans un groupe de réplication, et nommez le groupe. Cliquez ensuite sur **OK**. Notez les points suivants :

    * Les machines des groupes de réplication sont répliquées ensemble et ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent.
    * Nous vous recommandons de rassembler les serveurs physiques afin qu’ils reflètent vos charges de travail. L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail. Elle ne doit être utilisée que si les machines exécutent la même charge de travail et si vous avez besoin de cohérence.

13. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

## <a name="next-steps"></a>Étapes suivantes

Accédez à [Étape 11 : exécuter un test de basculement](physical-walkthrough-test-failover.md).
