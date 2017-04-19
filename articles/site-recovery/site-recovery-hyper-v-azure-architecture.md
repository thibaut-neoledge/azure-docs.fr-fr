---
title: "Fonctionnement de la réplication Hyper-V sur Azure dans Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du fonctionnement de la réplication Hyper-V dans Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/05/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 9adf266c6a2ac00c3aaa34e2a29aefe34abe2871
ms.lasthandoff: 04/18/2017


---
# <a name="how-does-hyper-v-replication-to-azure-work"></a>Fonctionnement de la réplication Hyper-V sur Azure

Lisez cet article pour comprendre l’architecture et les flux de travail de la réplication Hyper-V sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Vous pouvez répliquer les éléments suivants dans Azure :
- **Hyper-V avec VMM** : machines virtuelles situées sur les hôtes Hyper-V locaux gérés dans des clouds System Center Virtual Machine Manager (VMM). Les hôtes peuvent exécuter tout type de [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers). Vous pouvez répliquer des machines virtuelles Hyper-V exécutant un système d’exploitation invité [pris en charge par Hyper-V et Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).
- **Hyper-V sans VMM** : machines virtuelles locales situées sur des hôtes Hyper-V qui ne sont pas gérés dans des clouds VMM. Les hôtes peuvent exécuter n’importe quel [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions). Vous pouvez répliquer des machines virtuelles Hyper-V exécutant un système d’exploitation invité [pris en charge par Hyper-V et Azure](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).


## <a name="architectural-components"></a>Composants architecturaux

**Zone** | **Composant** | **Détails**
--- | --- | ---
**Microsoft Azure** | Dans Azure, vous avez besoin d’un compte Microsoft Azure, d’un compte de stockage Azure et d’un réseau Azure. | Le stockage et le réseau peuvent être des comptes Resource Manager ou Classic.<br/><br/> Les données répliquées sont stockées dans le compte de stockage, et les machines virtuelles Azure sont créées avec les données répliquées lors du basculement à partir de votre site local.<br/><br/> Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Serveur VMM** | Hôtes Hyper-V situés dans des clouds VMM | Si les hôtes Hyper-V sont gérés dans des clouds VMM, vous inscrivez le serveur VMM dans le coffre Recovery Services.<br/><br/> Sur le serveur VMM, vous installez le fournisseur Site Recovery pour orchestrer la réplication avec Azure.<br/><br/> Vous avez besoin d’un réseau logique et d’un réseau de machines virtuelles pour configurer le mappage réseau. Un réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud.
**Hôte Hyper-V** | Les serveurs Hyper-V peuvent être déployés avec ou sans serveur VMM. | S’il n’existe aucun serveur VMM, le fournisseur Site Recovery est installé sur l’hôte pour orchestrer la réplication avec Site Recovery via Internet. S’il existe un serveur VMM, le fournisseur est installé sur ce dernier et non sur l’hôte.<br/><br/> L’agent Recovery Services est installé sur l’hôte pour gérer la réplication des données.<br/><br/> Les communications en provenance du fournisseur et de l’agent sont sécurisées et chiffrées. Les données répliquées dans le stockage Azure sont également chiffrées.
**Machines virtuelles Hyper-V** | Vous devez avoir une ou plusieurs machines virtuelles sur le serveur hôte Hyper-V. | Rien ne doit explicitement être installé sur les machines virtuelles.

## <a name="deployment-steps"></a>Étapes du déploiement

1. **Azure** : vous configurez les composants Azure. Nous vous recommandons de configurer les comptes de stockage et de réseau avant de lancer le déploiement de Site Recovery.
2. **Coffre** : vous créez un coffre Recovery Services pour Site Recovery et vous configurez les paramètres du coffre, y compris les paramètres sources et cibles, la définition d’une stratégie de réplication et l’activation de la réplication.
3. **Source et cible** :
    - **Hôtes Hyper-V dans des clouds VMM** : dans le cadre de la définition des paramètres sources, vous téléchargez et installez le fournisseur Azure Site Recovery sur le serveur VMM et l’agent Azure Recovery Services sur chaque hôte Hyper-V. La source est le serveur VMM. La cible est Azure.
    - Hôtes Hyper-V sans VMM : quand vous définissez des paramètres sources, vous téléchargez et installez le fournisseur et l’agent sur chaque hôte Hyper-V. Au cours du déploiement, vous regroupez les hôtes dans un site Hyper-V et spécifiez ce site comme source. La cible est Azure.

    ![Réplication Hyper-V/VMM sur Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)
    ![Réplication de site Hyper-V sur Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


4. **Stratégie de réplication** : vous créez une stratégie de réplication pour le site Hyper-V ou le cloud VMM. La stratégie est appliquée à toutes les machines virtuelles situées sur les hôtes du site ou du cloud.
5. **Activer la réplication** : vous activez la réplication pour les machines virtuelles Hyper-V. La réplication initiale se déclenche selon les paramètres de la stratégie de réplication. Les modifications des données font l’objet d’un suivi et la réplication des modifications delta dans Azure débute après la réplication initiale. Les modifications qui font l’objet d’un suivi sont conservées dans un fichier .hrl.
6. **Test de basculement** : vous exécutez un test de basculement pour vérifier que tout fonctionne bien.

Pour en savoir plus sur le déploiement :
- [Prise en main de la réplication des machines virtuelles Hyper-V sur Azure (avec VMM)](site-recovery-vmm-to-azure.md)
- [Prise en main de la réplication des machines virtuelles Hyper-V sur Azure (sans VMM)](site-recovery-hyper-v-site-to-azure.md)

## <a name="hyper-v-replication-workflow"></a>Flux de travail de réplication Hyper-V

### <a name="enable-protection"></a>Activer la protection

1. Une fois que vous activez la protection d’une machine virtuelle Hyper-V, dans le portail Azure ou en local, **l’activation de la protection** démarre.
2. Le travail vérifie que la machine est conforme à la configuration requise, puis appelle la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), laquelle configure la réplication avec les paramètres que vous avez configurés.
3. Le travail démarre la réplication initiale en appelant la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète de la machine virtuelle et envoyer les disques virtuels de la machine virtuelle sur Azure.
4. Vous pouvez surveiller le travail dans l'onglet **Travaux**.
        ![Liste des travaux](media/site-recovery-hyper-v-azure-architecture/image1.png)
        ![Activer l’exploration de la protection](media/site-recovery-hyper-v-azure-architecture/image2.png)

### <a name="initial-replication"></a>Réplication initiale

1. Un [instantané des machines virtuelles Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) a lieu au moment où la réplication initiale est déclenchée.
2. Les disques durs virtuels sont répliqués un par un jusqu'à ce qu’ils soient copiés sur Azure. Cela peut prendre un certain temps selon la taille de la machine virtuelle et la bande passante réseau. Pour savoir comment optimiser l’utilisation du réseau, consultez la page [Gestion de l’utilisation de la bande passante réseau de protection d’un serveur local vers Azure](https://support.microsoft.com/kb/3056159).
3. Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl) qui se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire.
4. L’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale.
5. Lorsque la réplication initiale s’achève, l’instantané de machine virtuelle est supprimé. Les modifications d’ordre différentiel dans le fichier journal sont synchronisées et fusionnées sur le disque parent.


### <a name="finalize-protection"></a>Finalisation de la protection

1. Une fois la réplication initiale terminée, le travail **Finaliser la protection sur l’ordinateur virtuel** configure les paramètres réseau et d’autres paramètres de post-réplication pour protéger la machine virtuelle.
    ![Finaliser le travail de protection](media/site-recovery-hyper-v-azure-architecture/image3.png)
2. Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement. À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.

### <a name="delta-replication"></a>Réplication différentielle

1. La synchronisation delta commence à l’issue de la réplication initiale, selon les paramètres de réplication.
2. Le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications sur un disque dur virtuel, dans des fichiers .hrl. Chaque disque configuré pour la réplication est associé à un fichier .hrl. Ce journal est envoyé au compte de stockage du client à la fin de la réplication initiale. Lorsqu’un journal est en transit vers Azure, les modifications apportées au disque principal font l’objet d’un suivi dans un autre fichier journal du même répertoire.
3. Lors de la réplication différentielle et initiale, vous pouvez surveiller la machine virtuelle dans la vue correspondante. [En savoir plus](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="replication-synchronization"></a>Synchronisation de réplication

1. Si la réplication différentielle échoue et si une réplication complète est exclue (car elle monopoliserait trop de bande passante ou prendrait trop de temps), une resynchronisation se produit au niveau d’une machine virtuelle. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation.
2.  La resynchronisation limite la quantité de données envoyées en calculant les sommes de contrôle des machines virtuelles sources et cibles et en envoyant les données différentielles uniquement. La resynchronisation utilise un algorithme de segmentation de bloc fixe, dans lequel les fichiers source et cible sont divisés en segments fixes. Le système génère les sommes de contrôle de chaque segment, puis les compare, afin de savoir quels blocs de la source doivent être appliqués à la cible.
3. Une fois la resynchronisation terminée, la réplication différentielle normale doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement. Par exemple, la resynchronisation peut reprendre en cas d’interruption du réseau ou autre panne. Pour ce faire, sélectionnez la machine virtuelle dans le portail > **Resynchroniser**.

    ![Resynchronisation manuelle](media/site-recovery-hyper-v-azure-architecture/image4.png)


### <a name="retries"></a>Nouvelle tentatives

Si une erreur de réplication se produit, une nouvelle tentative intégrée est effectuée. Elle s’articule autour de deux catégories :

**Catégorie** | **Détails**
--- | ---
**Erreurs non récupérables** | Aucune nouvelle tentative n’est effectuée. L’état de la machine virtuelle est affiché comme **Critique** et l’intervention de l’administrateur est requise. Exemples d’erreurs : chaîne de disques durs virtuels rompue, état non valide pour les machines virtuelles réplicas, erreurs d’authentification du réseau : erreurs d’autorisations, erreurs de machines virtuelles non trouvées (pour les serveurs Hyper-V autonomes)
**Erreurs récupérables** | De nouvelles tentatives se produisent à chaque intervalle de réplication, à l’aide d’une interruption exponentielle laquelle augmente l’intervalle de récupération de 1, 2, 4, 8 et 10 minutes, dès le début de la première tentative. Si une erreur persiste, effectuez une nouvelle tentative toutes les 30 minutes. Voici quelques exemples : erreur réseau, erreur espace disque faible, conditions de mémoire insuffisante |

## <a name="protection-and-recovery-lifecycle"></a>Cycle de vie de protection et de récupération

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Étapes suivantes

- [Vérification des conditions préalables au déploiement](site-recovery-prereq.md)
- Résolution des problèmes avec :
    - [Surveiller et dépanner la protection](site-recovery-monitoring-and-troubleshooting.md)
    - [Aide du support Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
    - [Problèmes courants et leur résolution](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)

