---
title: "Réplication Hyper-V avec Azure Site Recovery | Microsoft Docs"
description: "Cet article permet de comprendre les concepts techniques qui vous permettent d’installer, de configurer et de gérer Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: mkjain
editor: 
ms.assetid: 97916915-1379-47df-8369-12ddf022c4da
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/12/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: b49771ff1e29aeb6ec582c21061085504705991b


---
# <a name="hyper-v-replication-with-azure-site-recovery"></a>Réplication Hyper-V avec Azure Site Recovery
Cet article décrit les concepts techniques qui vous permettent de configurer et de gérer la protection entre un site Hyper-V ou un site System Center Virtual Machine Manager et Azure à l’aide d’Azure Site Recovery.

## <a name="setting-up-the-source-environment-for-replication-between-on-premises-and-azure"></a>Configuration de l’environnement source pour la réplication entre des sites locaux et Azure
Dans le cadre de la configuration de la récupération d’urgence entre des sites locaux et Azure, veillez à télécharger et installer le fournisseur Azure Site Recovery sur le serveur Virtual Machine Manager (VMM). Installez l’agent Azure Recovery Services sur chaque hôte Hyper-V.

![Déploiement du site VMM pour la réplication entre des sites locaux et Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

La procédure de configuration de l’environnement source dans une infrastructure Hyper-V gérée est similaire à celle de l’environnement source dans une infrastructure VMM gérée. La seule différence est que le fournisseur et l’agent sont installés sur l’hôte Hyper-V lui-même. Dans l’environnement VMM, le fournisseur est installé sur le serveur VMM et l’agent est installé sur les hôtes Hyper-V (en cas de réplication vers Azure).

## <a name="workflows"></a>Flux de travail
### <a name="enable-protection"></a>Activer la protection
Lorsque vous protégez une machine virtuelle à partir du portail Azure ou sur site, une tâche Site Recovery nommée **Activer la protection** démarre. Vous pouvez la surveiller grâce à l’onglet **Tâches** .

![Tâche Activer la protection dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image001.PNG)

La tâche **Activer la protection** vérifie les conditions préalables avant d’appeler la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) . Cette méthode permet de créer la réplication vers Azure à l’aide des entrées qui sont configurées au cours de la protection.

La tâche **Activer la protection** démarre la réplication initiale depuis le site local en appelant la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) . Cette méthode envoie les disques virtuels de la machine virtuelle à Azure.

![Informations relatives à la tâche Activer la protection](media/site-recovery-understanding-site-to-azure-protection/IMAGE002.PNG)

### <a name="finalize-protection-on-the-virtual-machine"></a>Finaliser la protection sur la machine virtuelle
Un [instantané des machines virtuelles Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) est effectué lorsque la réplication initiale est déclenchée. Les disques durs virtuels sont traités un par un, jusqu’à ce qu’ils soient tous chargés dans Azure. Cette procédure prend généralement un certain temps, selon la taille du disque et la bande passante. Pour savoir comment optimiser l’utilisation du réseau, consultez la page [Gestion de l’utilisation de la bande passante réseau de protection d’un serveur local vers Azure](https://support.microsoft.com/kb/3056159).

Une fois la réplication initiale terminée, la tâche **Finaliser la protection sur la machine virtuelle** configure les paramètres de post-réplication et réseau. Pendant l’exécution de la réplication initiale :

* Toutes les modifications apportées aux disques font l’objet d’un suivi.
* Le stockage sur disque supplémentaire est utilisé pour les fichiers journaux de réplication Hyper-V (HRL) et de capture instantanée.

À la fin de la réplication initiale, l’instantané des machines virtuelles Hyper-V est supprimé. Cette suppression entraîne la fusion des modifications apportées aux données à la fin de la réplication initiale sur le disque parent.

![Tâche Finaliser la protection sur l’ordinateur virtuel dans la liste des tâches](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### <a name="delta-replication"></a>Réplication différentielle
Le dispositif de suivi de réplication des réplicas Hyper-V, qui fait partie du moteur de réplication des réplicas Hyper-V, effectue un suivi des modifications apportées à un disque dur virtuel sous la forme de fichiers journaux de réplication Hyper-V (*.hrl). Les fichiers HRL se trouvent dans le même répertoire que les disques associés.

Chaque disque configuré pour la réplication est associé à un fichier HRL. Ce journal est envoyé au compte de stockage du client à la fin de la réplication initiale. Lorsqu’un journal est en transit vers Azure, les modifications apportées au fichier principal font l’objet d’un suivi dans un autre fichier journal du même répertoire.

Lors de la réplication initiale ou différentielle, vous pouvez surveiller l’intégrité de l’opération dans la vue Machine virtuelle, comme indiqué dans la section [Surveillance de l’intégrité de la réplication de la machine virtuelle](site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machines).  

### <a name="resynchronization"></a>Resynchronisation
Une machine virtuelle est marquée pour une resynchronisation lorsque la réplication différentielle échoue et que la réplication initiale complète est coûteuse en termes de bande passante réseau ou de temps. Par exemple, lorsque la taille du fichier HRL représente jusqu’à 50 % de la taille totale du disque, la machine virtuelle est marquée pour une resynchronisation. Cette opération réduit la quantité de données envoyées sur le réseau par le calcul de sommes de contrôle des disques de machine virtuelle source et cible et l’envoi du différentiel uniquement.

Une fois la resynchronisation terminée, la réplication différentielle normale doit reprendre. La resynchronisation peut reprendre en cas d’interruption du réseau ou autre.

Par défaut, une resynchronisation planifiée automatiquement est configurée pour s’exécuter en dehors des heures de travail. Si la machine virtuelle doit être resynchronisée manuellement, sélectionnez-la dans le portail et cliquez sur **Resynchroniser**.

![Resynchronisation manuelle](media/site-recovery-understanding-site-to-azure-protection/image04.png)

La resynchronisation utilise un algorithme de segmentation de bloc fixe, dans lequel les fichiers source et cible sont divisés en segments fixes. Le système génère les sommes de contrôle de chaque segment, puis les compare, afin de savoir quels blocs de la source doivent être appliqués à la cible.

### <a name="retry-logic"></a>Logique de nouvelle tentative
Il existe une logique de nouvelle tentative intégrée pour les erreurs de réplication. Elle s’articule autour de deux catégories :

| Catégorie | Scénarios |
| --- | --- |
| Erreur non récupérable |Aucune nouvelle tentative n’est effectuée. L’état de réplication de la machine virtuelle est affiché comme **Critique**et une intervention de l’administrateur est requise. Voici quelques exemples :  <ul><li>Chaîne du disque dur virtuel interrompue</li><li>État non valide pour la machine virtuelle de réplication</li><li>Erreur d’authentification réseau</li><li>Erreur d’autorisation</li><li>Machine virtuelle introuvable, dans le cas d’un serveur autonome Hyper-V</li></ul> |
| Erreur récupérable |De nouvelles tentatives se produisent à chaque intervalle de réplication à l’aide d’une interruption exponentielle, qui augmente l’intervalle de nouvelle tentative dès le début de la première tentative (1, 2, 4, 8 ou 10 minutes). Si une erreur persiste, effectuez une nouvelle tentative toutes les 30 minutes. Voici quelques exemples :  <ul><li>Erreur réseau</li><li>Espace disque insuffisant</li><li>Mémoire insuffisante</li></ul> |

## <a name="hyper-v-virtual-machine-protection-and-recovery-life-cycle"></a>Cycle de vie de protection et de récupération de la machine virtuelle Hyper-V
![Cycle de vie de protection et de récupération de la machine virtuelle Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## <a name="other-references"></a>Autres références
* [Surveiller et résoudre les problèmes de protection pour les sites VMware, VMM, Hyper-V et physiques](site-recovery-monitoring-and-troubleshooting.md)
* [Contacter le support Microsoft](site-recovery-monitoring-and-troubleshooting.md#reach-out-for-microsoft-support)
* [Erreurs liées à Azure Site Recovery et résolution](site-recovery-monitoring-and-troubleshooting.md#common-azure-site-recovery-errors-and-their-resolutions)



<!--HONumber=Nov16_HO3-->


