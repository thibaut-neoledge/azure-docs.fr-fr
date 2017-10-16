---
title: "Passer en revue l’architecture pour la réplication Hyper-V (sans System Center VMM) sur Azure avec Azure Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles Hyper-V locales dans des clouds VMM vers Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: df4e227d02901153d3cfcfd4dfd4f11de180763a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture"></a>Étape 1 : examen de l’architecture


Cet article décrit les composants et les processus impliqués dans la réplication des machines virtuelles Hyper-V locales dans des clouds System Center Virtual machine Manager (VMM) vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Composants architecturaux

Un certain nombre de composants sont impliqués dans la réplication des machines virtuelles Hyper-V dans des clouds VMM vers Azure.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Microsoft Azure** | Dans Azure, vous avez besoin d’un compte Microsoft Azure, d’un compte de stockage Azure et d’un réseau Azure. | Les données répliquées sont stockées dans le compte de stockage, et les machines virtuelles Azure sont créées avec les données répliquées lors du basculement à partir de votre site local.<br/><br/> Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Serveur VMM** | Le serveur VMM a un ou plusieurs clouds contenant des hôtes Hyper-V. | Sur le serveur VMM, installez Site Recovery Provider pour organiser la réplication avec Site Recovery, et enregistrez le serveur dans le coffre Recovery Services.
**Hôte Hyper-V** | Un ou plusieurs clusters ou hôtes Hyper-V gérés par VMM. |  Installez l’agent Recovery Services sur chaque hôte ou membre de cluster.
**Machines virtuelles Hyper-V** | Une ou plusieurs machines virtuelles s’exécutant sur le serveur hôte Hyper-V. | Rien ne doit explicitement être installé sur les machines virtuelles.
**Mise en réseau** |Installation des réseaux logiques et des réseaux de machines virtuelles sur le serveur VMM. Un réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud. | Les réseaux de machines virtuelles sont mappés à des réseaux virtuels Azure, pour que les machines virtuelles Azure soient situées dans un réseau si elles sont créées après basculement.

En savoir plus sur les conditions préalables au déploiement et la configuration requise pour chacun de ces composants dans la [matrice de prise en charge](site-recovery-support-matrix-to-azure.md).


**Figure 1 : répliquer les machines virtuelles sur des hôtes Hyper-V dans des clouds VMM vers Azure**

![Composants](./media/vmm-to-azure-walkthrough-architecture/arch-onprem-onprem-azure-vmm.png)


## <a name="replication-process"></a>Processus de réplication

**Figure 2 : processus de réplication et de récupération pour la réplication de Hyper-V vers Azure**

![flux de travail](./media/vmm-to-azure-walkthrough-architecture/arch-hyperv-azure-workflow.png)

### <a name="enable-protection"></a>Activer la protection

1. Une fois que vous activez la protection d’une machine virtuelle Hyper-V, dans le portail Azure ou en local, **l’activation de la protection** démarre.
2. Le travail vérifie que la machine est conforme à la configuration requise, puis appelle la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), laquelle configure la réplication avec les paramètres que vous avez configurés.
3. Le travail démarre la réplication initiale en appelant la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète de la machine virtuelle et envoyer les disques virtuels de la machine virtuelle sur Azure.
4. Vous pouvez surveiller le travail dans l'onglet **Travaux**.      ![Liste des travaux](media/vmm-to-azure-walkthrough-architecture/image1.png)![Activer l’exploration de la protection](media/vmm-to-azure-walkthrough-architecture/image2.png)

### <a name="replicate-the-initial-data"></a>Répliquer les données initiales

1. Un [instantané des machines virtuelles Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) a lieu au moment où la réplication initiale est déclenchée.
2. Les disques durs virtuels sont répliqués un par un jusqu'à ce qu’ils soient copiés sur Azure. Cela peut prendre un certain temps selon la taille de la machine virtuelle et la bande passante réseau. Pour savoir comment optimiser l’utilisation du réseau, consultez la page [Gestion de l’utilisation de la bande passante réseau de protection d’un serveur local vers Azure](https://support.microsoft.com/kb/3056159).
3. Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl) qui se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire.
4. L’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale.
5. Lorsque la réplication initiale s’achève, l’instantané de machine virtuelle est supprimé. Les modifications d’ordre différentiel dans le fichier journal sont synchronisées et fusionnées sur le disque parent.


### <a name="finalize-protection"></a>Finalisation de la protection

1. Une fois la réplication initiale terminée, le travail **Finaliser la protection sur l’ordinateur virtuel** configure les paramètres réseau et d’autres paramètres de post-réplication pour protéger la machine virtuelle.
    ![Finaliser le travail de protection](media/vmm-to-azure-walkthrough-architecture/image3.png)
2. Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement. À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.

### <a name="replicate-the-delta"></a>Répliquer le delta

1. La synchronisation delta commence à l’issue de la réplication initiale, selon les paramètres de réplication.
2. Le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications sur un disque dur virtuel, dans des fichiers .hrl. Chaque disque configuré pour la réplication est associé à un fichier .hrl. Ce journal est envoyé au compte de stockage du client à la fin de la réplication initiale. Lorsqu’un journal est en transit vers Azure, les modifications apportées au disque principal font l’objet d’un suivi dans un autre fichier journal du même répertoire.
3. Lors de la réplication différentielle et initiale, vous pouvez surveiller la machine virtuelle dans la vue correspondante. [En savoir plus](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="synchronize-replication"></a>Synchroniser la réplication

1. Si la réplication différentielle échoue et si une réplication complète est exclue (car elle monopoliserait trop de bande passante ou prendrait trop de temps), une resynchronisation se produit au niveau d’une machine virtuelle. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation.
2.  La resynchronisation limite la quantité de données envoyées en calculant les sommes de contrôle des machines virtuelles sources et cibles et en envoyant les données différentielles uniquement. La resynchronisation utilise un algorithme de segmentation de bloc fixe, dans lequel les fichiers source et cible sont divisés en segments fixes. Le système génère les sommes de contrôle de chaque segment, puis les compare, afin de savoir quels blocs de la source doivent être appliqués à la cible.
3. Une fois la resynchronisation terminée, la réplication différentielle normale doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement. Par exemple, la resynchronisation peut reprendre en cas d’interruption du réseau ou autre panne. Pour ce faire, sélectionnez la machine virtuelle dans le portail > **Resynchroniser**.

    ![Resynchronisation manuelle](media/vmm-to-azure-walkthrough-architecture/image4.png)


### <a name="retry-logic"></a>Logique de nouvelle tentative

Si une erreur de réplication se produit, une nouvelle tentative intégrée est effectuée. Elle s’articule autour de deux catégories :

**Catégorie** | **Détails**
--- | ---
**Erreurs non récupérables** | Aucune nouvelle tentative n’est effectuée. L’état de la machine virtuelle est affiché comme **Critique** et l’intervention de l’administrateur est requise. Exemples d’erreurs : chaîne de disques durs virtuels rompue, état non valide pour les machines virtuelles réplicas, erreurs d’authentification du réseau : erreurs d’autorisations, erreurs de machines virtuelles non trouvées (pour les serveurs Hyper-V autonomes)
**Erreurs récupérables** | De nouvelles tentatives se produisent à chaque intervalle de réplication, à l’aide d’une interruption exponentielle laquelle augmente l’intervalle de récupération de 1, 2, 4, 8 et 10 minutes, dès le début de la première tentative. Si une erreur persiste, effectuez une nouvelle tentative toutes les 30 minutes. Voici quelques exemples : erreur réseau, erreur espace disque faible, conditions de mémoire insuffisante |



## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

1. Vous pouvez effectuer un [basculement](site-recovery-failover.md) planifié ou non planifié vers Azure à partir de machines virtuelles Hyper-V locales. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
2. Vous pouvez basculer sur une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour orchestrer le basculement de plusieurs machines.
4. Une fois le basculement effectué, vous pouvez voir les machines virtuelles répliquées dans Azure. Si nécessaire, vous pouvez affecter une adresse IP publique à la machine virtuelle.
5. Vous validez ensuite le basculement pour accéder à la charge de travail à partir de la machine virtuelle Azure répliquée.
6. Lorsque votre site local principal est à nouveau disponible, vous pouvez lancer la [restauration automatique](site-recovery-failback-from-azure-to-hyper-v.md). Vous effectuez un basculement planifié depuis Azure vers le site principal. Pour un basculement planifié, vous pouvez choisir d’effectuer une restauration automatique vers la même machine virtuelle ou un autre emplacement et synchroniser les modifications entre Azure et un emplacement local pour éviter toute perte de données. Lorsque les machines virtuelles sont créées en local, vous validez le basculement.




## <a name="next-steps"></a>Étapes suivantes

Accédez à l’[Étape 2 : passer en revue les conditions préalables au déploiement](vmm-to-azure-walkthrough-prerequisites.md)
