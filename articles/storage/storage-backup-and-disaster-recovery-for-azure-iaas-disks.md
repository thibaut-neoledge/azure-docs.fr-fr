---
title: "Sauvegarde et récupération d’urgence pour les disques IaaS Azure | Microsoft Docs"
description: "Cet article montre comment planifier la sauvegarde et la récupération d’urgence de disques et de machines virtuelles IaaS dans Azure. Ce document couvre les disques managés et non managés."
services: storage
cloud: Azure
documentationcenter: na
author: luywang
manager: kavithag
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: luywang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0de675e46dbd527148ac5fedcd35d81962adfb7e
ms.contentlocale: fr-fr
ms.lasthandoff: 07/21/2017

---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Sauvegarde et récupération d’urgence pour les disques IaaS Azure

Cet article montre comment planifier la sauvegarde et la récupération d’urgence de disques et de machines virtuelles IaaS dans Azure. Ce document couvre les disques managés et non managés.

Nous abordons tout d’abord les fonctionnalités de tolérance de panne intégrées dans la plateforme Azure qui vous protègent contre les défaillances locales. Nous présentons ensuite les scénarios d’urgence qui ne sont pas entièrement couverts par les fonctionnalités intégrées. Il s’agit du thème principal traité par ce document. Nous montrons également plusieurs exemples de charges de travail où différentes considérations en matière de sauvegarde et de récupération d’urgence peuvent s’appliquer. Nous voyons ensuite les solutions possibles pour la récupération d’urgence des disques IaaS. 

## <a name="introduction"></a>Introduction

La plateforme Azure utilise différentes méthodes pour la redondance et la tolérance de panne afin de protéger les clients contre les défaillances matérielles localisées qui peuvent se produire. Les défaillances locales peuvent inclure des problèmes avec un serveur de stockage Azure qui stocke une partie des données d’un disque virtuel ou les défaillances d’un disque SSD ou d’un disque dur sur ce serveur. Ces défaillances de composant matériel isolées peuvent se produire pendant les opérations normales et la plateforme est conçue pour être résiliente à ces défaillances. Des sinistres majeurs peuvent entraîner des défaillances ou l’inaccessibilité d’un grand nombre de serveurs de stockage ou d’un centre de données entier. Alors que vos machines virtuelles et disques sont normalement protégés contre les défaillances localisées, des étapes supplémentaires sont nécessaires pour protéger votre charge de travail contre les défaillances irrécupérables à l’échelle de la région (par exemple, une catastrophe majeure) qui peuvent affecter vos machines virtuelles et vos disques.

Outre la possibilité de défaillances de la plateforme, des problèmes avec l’application ou les données du client peuvent se produire. Par exemple, une nouvelle version de votre application peut par inadvertance apporter des modifications importantes aux données. Dans ce cas, vous pouvez restaurer l’application et les données à une version antérieure qui contient le dernier état correct connu. Cela nécessite la gestion de sauvegardes régulières.

Pour la récupération d’urgence régionale, vous devez sauvegarder vos disques de machines virtuelles IaaS dans une autre région. 

Avant d’examiner les options de sauvegarde et de récupération d’urgence, récapitulons quelques méthodes disponibles pour gérer les défaillances localisées.

### <a name="azure-iaas-resiliency"></a>Résilience Azure IaaS

La *résilience* fait référence à la tolérance aux défaillances normales qui se produisent dans les composants matériels. La résilience est la capacité à récupérer après des défaillances et à continuer de fonctionner. Il ne s’agit pas d’éviter les défaillances, mais d’y répondre en évitant les temps d’arrêt ou la perte de données. L’objectif de la résilience est que l’application retrouve un état entièrement fonctionnel suite à une défaillance. Les machines virtuelles et les disques Azure sont conçus pour assurer la résilience aux défaillances matérielles courantes. Examinons comment la plateforme Azure IaaS fournit cette résilience.

Une machine virtuelle se compose principalement de deux parties : (1) un serveur de calcul et (2) les disques persistants. Ces deux composants assurent la tolérance de panne d’une machine virtuelle.

Si le serveur hôte de calcul Azure qui héberge votre machine virtuelle subit une défaillance matérielle (ce qui est rare), Azure est conçu pour restaurer automatiquement la machine virtuelle sur un autre serveur. Dans ce cas, un redémarrage a lieu et la machine virtuelle est sauvegardée au bout d’un certain temps. Azure détecte automatiquement de telles défaillances matérielles et exécute des récupérations pour garantir la disponibilité rapide de la machine virtuelle du client.

Concernant les disques IaaS, la durabilité des données est essentielle pour la plateforme de stockage persistant. Les clients Azure ont des applications métiers importantes exécutées sur IaaS qui dépendent de la persistance des données. Azure conçoit la protection de ces disques IaaS avec trois copies redondantes des données stockées localement, fournissant ainsi une durabilité élevée contre les défaillances locales. Si un des composants matériels qui contient le disque tombe en panne, votre machine virtuelle n’est pas affectée, car il existe deux copies supplémentaires pour prendre en charge les demandes du disque. Cela fonctionne bien même si deux composants matériels prenant en charge un disque tombent en panne en même temps (ce qui est très rare). Pour garantir le maintien en continu de trois réplicas, le service Stockage Azure génère automatiquement une nouvelle copie des données en arrière-plan si une des trois copies devient indisponible. Par conséquent, il ne doit pas être nécessaire d’utiliser un système RAID avec des disques Azure pour assurer la tolérance de panne. Une simple configuration RAID 0 doit être suffisante pour la répartition des disques le cas échéant afin de créer des volumes de plus grande capacité.

Cette architecture a permis à **Azure de fournir de façon cohérente une durabilité de classe Entreprise pour les disques IaaS, avec un [taux de défaillance annuel](https://en.wikipedia.org/wiki/Annualized_failure_rate) inégalé dans le secteur de zéro %.**

Les défaillances matérielles localisées sur l’ordinateur hôte ou dans la plateforme de stockage peuvent parfois entraîner une indisponibilité temporaire de la machine virtuelle qui est couverte par le [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) qui en garantit la disponibilité. Azure fournit également un contrat SLA de pointe pour les instances de machine virtuelle uniques qui utilisent des disques Stockage Premium.

Pour protéger les charges de travail d’application contre les temps d’arrêt suite à l’indisponibilité temporaire d’un disque ou d’une machine virtuelle, les clients peuvent exploiter les [groupes à haute disponibilité](../virtual-machines/windows/manage-availability.md). Plusieurs machines virtuelles regroupées dans un groupe à haute disponibilité assurent la redondance de votre application. Azure crée ensuite ces machines virtuelles et disques dans des domaines d’erreur distincts avec différents composants d’alimentation, réseau et serveur. Par conséquent, les défaillances matérielles localisées n’affectent généralement pas plusieurs machines virtuelles du groupe en même temps, offrant ainsi une haute disponibilité pour votre application. Il est considéré comme une bonne pratique d’utiliser des groupes à haute disponibilité lorsque la haute disponibilité est requise. Pour plus d’informations, consultez les aspects de la récupération d’urgence détaillés ci-dessous.

### <a name="backup-and-disaster-recovery"></a>Sauvegarde et récupération d’urgence

La récupération d’urgence est la capacité à récupérer après des incidents rares mais majeurs : des défaillances non transitoires et à grande échelle, telles que des interruptions de service qui affectent une région entière. La récupération d’urgence inclut la sauvegarde et l’archivage des données. Elle peut également inclure une intervention manuelle, comme la restauration d’une base de données à partir de la sauvegarde.

Il se peut que la protection intégrée de la plateforme Azure contre les défaillances localisées ne protège pas entièrement les machines virtuelles/disques dans le cas de sinistres majeurs qui peuvent provoquer des interruptions à grande échelle. Cela inclut les événements catastrophiques, par exemple un centre de données atteint par un ouragan, un tremblement de terre, un incendie ou des pannes d’unités matérielles à grande échelle. En outre, vous pouvez rencontrer des défaillances en raison de problèmes rencontrés par l’application ou les données.

Pour protéger vos charges de travail IaaS contre les interruptions, vous devez planifier la redondance et les sauvegardes afin d’activer la récupération. Pour la récupération d’urgence, vous devez planifier la redondance et la sauvegarde dans un emplacement géographique différent situé en dehors du site principal. Cela permet de garantir que la sauvegarde n’est pas affectée par l’événement qui a initialement eu un impact sur la machine virtuelle ou les disques. Pour plus d’informations, consultez [Récupération d’urgence pour les applications développées sur Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

Vos considérations relatives à la récupération d’urgence peuvent inclure les aspects suivants :

1. La haute disponibilité est la capacité de l’application à continuer de s’exécuter dans un état sain, sans temps d’arrêt significatif. « Sain » signifie que l’application est réactive et que les utilisateurs peuvent s’y connecter et interagir avec elle. Certaines applications et bases de données critiques peuvent avoir besoin d’être toujours disponibles, même en cas de défaillances dans la plateforme. Pour ces charges de travail, vous devrez peut-être planifier une redondance pour l’application ainsi que les données.

2. Durabilité des données : dans certains cas, la considération principale est de s’assurer que les données sont préservées en cas de sinistre. Par conséquent, vous aurez peut-être besoin d’une sauvegarde de vos données dans un autre site. Pour ces charges de travail, vous n’aurez peut-être pas besoin d’une redondance complète pour l’application, mais d’une sauvegarde régulière des disques.

## <a name="backup-and-dr-scenarios"></a>Scénarios de sauvegarde et de récupération d’urgence

Examinons quelques exemples de charges de travail d’application et les éléments à prendre en compte pour la planification de la récupération d’urgence.

### <a name="scenario-1-major-database-solutions"></a>Scénario 1 : solutions de base de données majeures

Prenons l’exemple d’un serveur de base de données de production tel que SQL Server ou Oracle prenant en charge la haute disponibilité. Les utilisateurs et les applications de production critiques dépendent de cette base de données. Le plan de récupération d’urgence pour ce système peut être de prendre en charge les exigences suivantes :

1. Les données doivent être protégées et récupérables.
2.  Le serveur doit être disponible pour utilisation.

Cela peut nécessiter de maintenir un réplica de la base de données dans une autre région en tant que sauvegarde. Selon les exigences liées à la récupération des données et à la disponibilité du serveur, la solution peut aller d’un site de réplica actif-actif ou actif-passif à des sauvegardes en mode hors connexion des données. Les bases de données relationnelles telles que SQL Server et Oracle fournissent diverses options pour la réplication. Pour SQL Server, les [groupes de disponibilité SQL Server Always On](https://msdn.microsoft.com/library/hh510230.aspx) peuvent être utilisés pour la haute disponibilité.

Les bases de données NoSQL comme MongoDB prennent également en charge les [réplicas](https://docs.mongodb.com/manual/replication/) pour assurer la redondance. Les réplicas pour la haute disponibilité peuvent être utilisés.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Scénario 2 : un cluster de machines virtuelles redondantes

Prenons l’exemple d’une charge de travail gérée par un cluster de machines virtuelles qui fournit la redondance et l’équilibrage de charge. Il peut s’agir d’un cluster Cassandra déployé dans une région. Ce type d’architecture fournit déjà un niveau élevé de redondance dans cette région. Toutefois, pour protéger la charge de travail contre une défaillance au niveau régional, vous devez envisager de répartir le cluster entre deux régions ou d’effectuer des sauvegardes périodiques vers une autre région.

### <a name="scenario-3-iaas-application-workload"></a>Scénario 3 : charge de travail d’application IaaS

Il peut s’agir d’une charge de travail de production standard exécutée sur une machine virtuelle Azure. Par exemple, il peut s’agir d’un serveur web ou d’un serveur de fichiers contenant le contenu et d’autres ressources d’un site. Il peut également s’agir d’une application métier personnalisée exécutée sur une machine virtuelle qui stocke ses données, ressources et son état sur des disques de machine virtuelle. Dans ce cas, il est important de veiller à effectuer des sauvegardes régulièrement. La fréquence des sauvegardes doit être basée sur la nature de la charge de travail de machine virtuelle. Par exemple, si l’application s’exécute chaque jour et modifie des données, la sauvegarde doit être effectuée toutes les heures.

Voici un autre exemple : un serveur de rapports qui extrait des données provenant d’autres sources et génère des rapports agrégés. La perte de cette machine virtuelle ou des disques entraîne la perte des rapports. Toutefois, il est possible de réexécuter le processus de création de rapports et de regénérer la sortie. Dans ce cas, vous ne rencontrez pas vraiment une perte de données, même si le serveur de rapports est touché par un sinistre. Vous pouvez donc avoir un niveau plus élevé de tolérance concernant la perte d’une partie des données du serveur de rapports. Dans ce cas, des sauvegardes moins fréquentes sont une option permettant de réduire les coûts.

### <a name="scenario-4-iaas-application-data-issues"></a>Scénario 4 : problèmes liés aux données d’une application IaaS

Vous avez une application qui calcule, tient à jour et gère des données commerciales critiques telles que des informations de tarification. Une nouvelle version de votre application a rencontré un bogue logiciel qui a incorrectement calculé la tarification et a endommagé les données commerciales actuelles prises en charge par la plateforme. Ici, le meilleur plan d’action est de commencer par rétablir la version antérieure de l’application et des données. Pour ce faire, effectuez des sauvegardes périodiques de votre système.

## <a name="disaster-recovery-solution-azure-backup-service"></a>Solution de récupération d’urgence : service Sauvegarde Azure

Le [service Sauvegarde Azure](https://azure.microsoft.com/services/backup/) peut être utilisé pour la sauvegarde et la récupération d’urgence et il fonctionne aussi bien avec les [disques managés](storage-managed-disks-overview.md) que les [disques non managés](storage-about-disks-and-vhds-windows.md#unmanaged-disks). Vous pouvez créer un travail de sauvegarde avec des sauvegardes périodiques, une restauration facile des machines virtuelles et des stratégies de rétention de sauvegarde. 

Si vous utilisez des [disques Stockage Premium](storage-premium-storage.md), des [disques managés](storage-managed-disks-overview.md) ou d’autres types de disque avec l’option [Stockage localement redondant (LRS)](storage-redundancy.md#locally-redundant-storage) , il est particulièrement important de tirer parti des sauvegardes de récupération d’urgence périodiques. Sauvegarde Azure stocke les données dans votre coffre Recovery Services pour une rétention à long terme. Choisissez l’option [Stockage géoredondant (GRS)](storage-redundancy.md#geo-redundant-storage) pour le coffre Recovery Services du service Sauvegarde. Cela permet de garantir que les sauvegardes sont répliquées sur une région Azure différente afin d’assurer la protection contre les sinistres au niveau d’une région.

Pour les [disques non managés](storage-about-disks-and-vhds-windows.md#unmanaged-disks), vous pouvez utiliser le type de stockage LRS pour les disques IaaS, mais veillez à ce que Sauvegarde Azure soit activé avec l’option GRS pour le coffre Recovery Services.

**Si vous utilisez l’option [GRS](storage-redundancy.md#geo-redundant-storage)/[RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage) pour vos disques non managés, vous avez besoin de captures instantanées cohérentes pour la sauvegarde et la récupération d’urgence.** Vous devez utiliser le [service Sauvegarde Azure](https://azure.microsoft.com/services/backup/) ou des [captures instantanées cohérentes](#alternative-solution-consistent-snapshots).

Voici un résumé des solutions de récupération d’urgence.

| Scénario | Réplication automatique | Solution de récupération d’urgence |
| --- | --- | --- |
| *Disques stockage Premium* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| *Disques gérés* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| *Disques LRS non managés* | Local ([LRS](storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| *Disques GRS non managés* | Inter-région ([GRS](storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Captures instantanées cohérentes](#alternative-solution-consistent-snapshots) |
| *Disques RA-GRS non managés* | Inter-région ([RA-GRS](storage-redundancy.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Captures instantanées cohérentes](#alternative-solution-consistent-snapshots) |

Pour obtenir une haute disponibilité, il est conseillé d’utiliser des disques managés dans un groupe à haute disponibilité avec Sauvegarde Azure. Si vous utilisez des disques non managés, vous pouvez toujours utiliser Sauvegarde Azure pour la récupération d’urgence. Si vous ne pouvez pas utiliser Sauvegarde Azure, une solution consiste à utiliser des [captures instantanées cohérentes](#alternative-solution-consistent-snapshots), comme décrit dans une section ultérieure.

Vos choix pour la haute disponibilité, la sauvegarde et la récupération d’urgence aux niveaux Application et Infrastructure peuvent être représentés comme suit :

| *Niveau* | Haute disponibilité   | Sauvegarde/récupération d’urgence |
| --- | --- | --- |
| *Application* | SQL Always On | Azure Backup |
| *Infrastructure*  | Groupe à haute disponibilité  | GRS avec captures instantanées cohérentes |

### <a name="using-the-azure-backup-service"></a>Utilisation du service Sauvegarde Azure

[Sauvegarde Azure](../backup/backup-azure-vms-introduction.md) peut sauvegarder vos machines virtuelles exécutant Windows ou Linux dans le coffre Azure Recovery Services. La sauvegarde et la restauration des données critiques sont compliquées par le fait qu’elles doivent être sauvegardées alors que les applications qui génèrent les données sont en cours d’exécution. Pour résoudre ce problème, Sauvegarde Azure fournit des sauvegardes cohérentes entre les applications pour les charges de travail Microsoft en utilisant le service VSS (Volume Shadow Service) pour s’assurer que les données sont correctement écrites dans le stockage. Pour les machines virtuelles Linux, seules les sauvegardes cohérentes au niveau fichier sont possibles, car l’équivalent de la fonctionnalité VSS n’existe pas sous Linux.

![][1]

Lorsque le service Sauvegarde Azure lance le travail de sauvegarde à l’heure planifiée, il déclenche l’extension de sauvegarde installée dans la machine virtuelle pour prendre une capture instantanée à un moment donné. Cette capture instantanée est prise de façon coordonnée avec VSS pour obtenir une capture instantanée cohérente des disques de la machine virtuelle sans avoir à arrêter cette dernière. L’extension de sauvegarde de la machine virtuelle vide toutes les écritures avant de prendre la capture instantanée cohérente de tous les disques. Une fois la capture instantanée réalisée, les données sont transférées par le service Sauvegarde Azure dans le coffre de sauvegarde. Pour rendre le processus de sauvegarde plus efficace, le service identifie et transfère uniquement les blocs de données qui ont été modifiés depuis la sauvegarde précédente.


Pour effectuer la restauration, vous pouvez afficher les sauvegardes disponibles au moyen de Sauvegarde Azure, puis lancer une restauration. Vous pouvez créer et restaurer des sauvegardes Azure au moyen du [portail Azure](https://portal.azure.com/), de [PowerShell](../backup/backup-azure-vms-automation.md ) ou de l’interface [Azure CLI](https://docs.microsoft.com/azure/xplat-cli-install). Pour plus d’informations, consultez la page relative à Sauvegarde Azure.

### <a name="steps-to-enable-backup"></a>Étapes à suivre pour activer la sauvegarde

Les étapes suivantes sont généralement suivies pour activer les sauvegardes de vos machines virtuelles à l’aide du [portail Azure](https://portal.azure.com/). Cela peut varier légèrement en fonction de votre scénario. Pour plus d’informations, consultez la documentation [Sauvegarde Azure](../backup/backup-azure-vms-introduction.md). Sauvegarde Azure [prend également en charge les machines virtuelles avec des disques managés](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Pour créer un coffre Recovery Services pour une machine virtuelle, procédez comme suit :

    a.  Dans le [portail Azure](https://portal.azure.com/), parcourez toutes les ressources et recherchez Coffres Recovery Services.

    b.  Dans le menu Coffres Recovery Services, cliquez sur Ajouter et suivez les étapes pour créer un coffre dans la même région que la machine virtuelle. Par exemple, si votre machine virtuelle est située dans la région Ouest des États-Unis, vous pouvez choisir Ouest des États-Unis pour le coffre.

2.  Vérifiez la réplication du stockage pour le nouveau coffre. Pour ce faire, accédez au coffre à partir du panneau Coffres Recovery Services, puis accédez aux paramètres de configuration de la sauvegarde. Veillez à ce que l’option GRS soit sélectionnée par défaut. Cela garantit que votre coffre est automatiquement répliqué vers un centre de données secondaire. Par exemple, votre coffre de la région Ouest des États-Unis est automatiquement répliqué vers la région Est des États-Unis.

3.  Configurez la stratégie de sauvegarde et sélectionnez la machine virtuelle à partir de la même interface utilisateur.

4.  Vérifiez que l’agent de sauvegarde est installé sur la machine virtuelle. Si votre machine virtuelle est créée à l’aide d’une image de la galerie Azure, l’agent de sauvegarde est déjà installé. Sinon (autrement dit, si vous utilisez une image personnalisée), utilisez les instructions de la page [Installer l’agent de machine virtuelle sur la machine virtuelle](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Vérifiez que la machine virtuelle autorise la connectivité réseau pour que le service de sauvegarde fonctionne. Suivez ces instructions relatives à la [connectivité réseau](../backup/backup-azure-arm-vms-prepare.md#network-connectivity).

6.  Une fois les étapes ci-dessus terminées, la sauvegarde s’exécute à intervalles réguliers, comme spécifié dans la stratégie de sauvegarde. Si nécessaire, vous pouvez déclencher la première sauvegarde manuellement à partir du tableau de bord du coffre sur le portail Azure.

Pour automatiser l’utilisation de scripts par Sauvegarde Azure, reportez-vous à [Applets de commande PowerShell pour la sauvegarde de machines virtuelles](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Étapes de récupération

Si vous devez réparer ou regénérer une machine virtuelle, vous pouvez la restaurer à partir des points de récupération de sauvegarde dans le coffre. Il existe deux options différentes pour effectuer la récupération :

1.  Vous pouvez créer une machine virtuelle en tant que représentation jusqu’à une date et une heure de votre machine virtuelle sauvegardée.

2.  Vous pouvez restaurer les disques, puis utiliser le modèle de la machine virtuelle pour personnaliser et recréer la machine virtuelle restaurée. 

Consultez les instructions à la page [Utiliser le portail Azure pour restaurer des machines virtuelles](../backup/backup-azure-arm-restore-vms.md#restoring-a-vm-during-azure-datacenter-disaster). Le document explique également les étapes spécifiques permettant de restaurer les machines virtuelles sauvegardées dans le centre de données associé à l’aide de votre coffre de sauvegarde géoredondant en cas de sinistre dans le centre de données primaire. Dans ce cas, Sauvegarde Azure utilise le service Compute de la région secondaire pour créer la machine virtuelle restaurée.

Vous pouvez également utiliser PowerShell pour [restaurer une machine virtuelle](../backup/backup-azure-vms-automation.md#restore-an-azure-vm) ou [créer une machine virtuelle à partir de disques restaurés](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solution alternative : captures instantanées cohérentes

Si vous ne pouvez pas utiliser le service Sauvegarde Azure, vous pouvez implémenter votre propre mécanisme de sauvegarde à l’aide de captures instantanées. Il est compliqué de créer des captures instantanées cohérentes pour tous les disques utilisés par une machine virtuelle, puis de répliquer ces captures instantanées vers une autre région. Pour cette raison, Azure considère qu’il est préférable d’utiliser le service Sauvegarde plutôt que de créer une solution personnalisée. Si vous utilisez le stockage RA-GRS/GRS pour les disques, les captures instantanées sont automatiquement répliquées vers un centre de données secondaire. Si vous utilisez le stockage LRS pour les disques, vous devez répliquer les données vous-même. Pour plus d’informations, consultez [Sauvegarder les disques de machines virtuelles Azure non gérés avec des captures instantanées incrémentielles](storage-incremental-snapshots.md).

Une capture instantanée est une représentation sous la forme d’un objet à un point spécifique dans le temps. Une capture instantanée entraîne la facturation de la taille incrémentielle des données qu’elle contient. Pour plus d’informations, consultez [Création d’un instantané d’objet blob](storage-blob-snapshots.md).

### <a name="creating-snapshots-while-the-vm-is-running"></a>Création de captures instantanées pendant l’exécution de la machine virtuelle

Quand vous prenez une capture instantanée à un moment donné, si la machine virtuelle est en cours d’exécution, il y a toujours des données qui sont diffusées vers les disques et les captures instantanées peuvent contenir des opérations partielles qui étaient en cours. En outre, si plusieurs disques sont impliqués, les captures instantanées des différents disques se sont peut-être produites à différents moments, et il est donc possible qu’elles ne soient pas coordonnées. Cela est particulièrement problématique pour les volumes agrégés par bandes dont les fichiers peuvent être endommagés si des modifications sont apportées pendant la sauvegarde.

Pour éviter cette situation, le processus de sauvegarde doit implémenter les étapes suivantes :

1.  Figer tous les disques

2.  Vider toutes les écritures en attente

3.  [Créer une capture instantanée de blob](storage-blob-snapshots.md) pour tous les disques

Certaines applications Windows telles que SQL Server fournissent un mécanisme de sauvegarde coordonné via VSS pour créer des sauvegardes cohérentes entre les applications. Sur Linux, vous pouvez utiliser un outil tel que fsfreeze pour coordonner les disques, ce qui fournit des sauvegardes cohérentes au niveau fichier, mais pas de captures instantanés cohérentes au niveau application. Ce processus étant compliqué, envisagez d’utiliser le service [Sauvegarde Azure](../backup/backup-azure-vms-introduction.md) ou une solution de sauvegarde tierce qui implémente déjà cela.

Le processus ci-dessus produit une collection de captures instantanées coordonnées pour tous les disques de machine virtuelle, ce qui représente une vue dans le temps spécifique de la machine virtuelle. Il s’agit d’un point de restauration de sauvegarde pour la machine virtuelle. Vous pouvez répéter le processus à intervalles réguliers pour créer des sauvegardes périodiques. Consultez ci-dessous les étapes permettant de [copier les sauvegardes vers une autre région](#copy-the-snapshots-to-another-region) à des fins de récupération d’urgence.

### <a name="creating-snapshots-while-the-vm-is-offline"></a>Création de captures instantanées quand la machine virtuelle est en mode hors connexion

Une autre option pour créer des sauvegardes cohérentes est d’arrêter la machine virtuelle et de prendre des captures instantanées de blob de chaque disque. Cette opération est plus facile que de coordonner les captures instantanées d’une machine virtuelle en cours d’exécution, mais elle nécessite un temps d’arrêt de quelques minutes. Pour ce processus, procédez comme suit :

1. Arrêtez la machine virtuelle.

2. Créez une capture instantanée de chaque blob de disque dur virtuel. Cette opération ne prend que quelques secondes.

    Pour créer une capture instantanée, vous pouvez utiliser [PowerShell](storage-powershell-guide-full.md#how-to-manage-azure-blob-snapshots), l’[API REST Stockage Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), [Azure CLI](https://docs.microsoft.com/azure/xplat-cli-install) ou l’une des bibliothèques clientes Stockage Azure comme [la bibliothèque cliente Stockage pour .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Démarrez la machine virtuelle, ce qui met fin au temps d’arrêt. En général, l’ensemble du processus se termine en quelques minutes.

Ce processus génère une collection de captures instantanées cohérentes pour tous les disques, en fournissant un point de restauration de sauvegarde pour la machine virtuelle. Consultez ci-dessous les étapes permettant de copier les captures instantanées vers une autre région à des fins de récupération d’urgence.

### <a name="copy-the-snapshots-to-another-region"></a>Copier les captures instantanées vers une autre région

La création de captures instantanées uniquement n’est peut-être pas suffisante pour la récupération d’urgence. Vous devez également répliquer les sauvegardes de captures instantanées vers une autre région.

Si vous utilisez le stockage GRS ou RA-GRS pour vos disques, les captures instantanées sont automatiquement répliquées dans la région secondaire. Il peut y avoir un délai de quelques minutes avant la réplication et si le centre de données principal tombe en panne avant la fin de la réplication des captures instantanées, vous ne pouvez pas accéder aux captures instantanées à partir du centre de données secondaire. La probabilité que cela se produise est faible.

> [!Note] 
> Avoir des disques dans un compte GRS ou RA-GRS uniquement ne protège pas la machine virtuelle contre les incidents. Vous devez également créer des captures instantanées coordonnées ou utiliser Sauvegarde Azure. Cela est nécessaire pour récupérer une machine virtuelle dans un état cohérent.

Si vous utilisez LRS, vous devez copier les captures instantanées dans un autre compte de stockage immédiatement après leur création. La cible de la copie peut être un compte de stockage LRS dans une région différente, ce qui entraîne la génération d’une copie dans une région éloignée. Vous pouvez également copier la capture instantanée dans un compte de stockage RA-GRS dans la même région. Dans ce cas, la capture instantanée est répliquée tardivement dans la région secondaire distante. Votre sauvegarde est protégée contre les sinistres sur le site principal une fois que la copie et la réplication sont terminées.

Pour copier efficacement vos captures instantanées incrémentielles à des fins de récupération d’urgence, consultez les instructions figurant dans [Sauvegarder des disques de machine virtuelle non managés Azure avec des captures instantanées incrémentielles](storage-incremental-snapshots.md).

![][2]

### <a name="recovery-from-snapshots"></a>Récupération à partir de captures instantanées

Pour récupérer une capture instantanée, copiez-la pour créer un nouveau blob. Si vous copiez la capture instantanée à partir du compte principal, vous pouvez la copier dans le blob de base de la capture instantanée, et ainsi rétablir le disque vers la capture instantanée. Ce processus est également appelé « promotion de la capture instantanée ». Si vous copiez la sauvegarde de capture instantanée à partir d’un compte secondaire (dans le cas d’un stockage RA-GRS), celle-ci doit être copiée dans un compte principal. Vous pouvez copier une capture instantanée [à l’aide de PowerShell](storage-powershell-guide-full.md#how-to-copy-a-snapshot-of-a-blob) ou de l’utilitaire AzCopy. Pour plus d’informations, consultez [Transfert de données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md) .

Dans le cas des machines virtuelles contenant plusieurs disques, vous devez copier toutes les captures instantanées qui font partie du même point de restauration coordonné. Une fois que vous copiez les captures instantanées vers des blobs de disque dur virtuel accessibles en écriture, vous pouvez utiliser les blobs pour recréer votre machine virtuelle à l’aide du modèle correspondant.

## <a name="other-options"></a>Autres options

### <a name="sql-server"></a>SQL Server

SQL Server exécuté dans une machine virtuelle a ses propres fonctionnalités intégrées permettant de sauvegarder votre base de données SQL Server vers Stockage Blob Azure ou un partage de fichiers. Si le compte de stockage est GRS ou RA-GRS, vous pouvez accéder à ces sauvegardes dans le centre de données secondaire du compte de stockage en cas de sinistre, avec les restrictions indiquées précédemment. Pour plus d’informations, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). En plus de la sauvegarde et de la restauration, [les groupes de disponibilité SQL Server AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) peuvent gérer les réplicas secondaires de bases de données pour réduire considérablement le temps de récupération d’urgence.

## <a name="other-considerations"></a>Autres points à considérer

Cet article a décrit la sauvegarde ou la réalisation de captures instantanées de vos machines virtuelles et de leurs disques pour prendre en charge la récupération d’urgence. Il a également présenté leur utilisation dans le cadre d’une récupération. Avec le modèle Azure Resource Manager, de nombreuses personnes utilisent des modèles pour créer leurs machines virtuelles et d’autres infrastructures dans Azure. Vous pouvez utiliser un modèle pour créer une machine virtuelle qui possède la même configuration à chaque fois. Si vous utilisez des images personnalisées pour la création de vos machines virtuelles, vous devez également vérifier que vos images sont protégées en utilisant un compte RA-GRS pour les stocker.

Par conséquent, votre processus de sauvegarde peut être une combinaison de deux éléments :

1. Sauvegarder les données (disques)
2. Sauvegarder la configuration (modèles, images personnalisées)

Selon l’option de sauvegarde que vous choisissez, vous devrez peut-être gérer la sauvegarde des données et de la configuration ou il se peut que le service Sauvegarde gère tout cela pour vous.

## <a name="appendix---understanding-the-impact-of-lrs-grs-and-ra-grs"></a>Annexe - Comprendre l’impact des stockages LRS, GRS et RA-GRS

Pour les comptes de stockage dans Azure, il existe trois types de redondance de données que vous devez prendre en compte lors d’une récupération d’urgence : le stockage localement redondant (LRS), le stockage géoredondant (GRS) ou le stockage géoredondant avec accès en lecture (RA-GRS). 

Le stockage localement redondant (LRS) conserve trois copies des données dans le même centre de données. Lorsque vous écrivez des données, les trois copies sont mises à jour avant qu’un message de réussite ne soit retourné à l’appelant. Vous savez donc qu’elles sont identiques. Votre disque est protégé contre les défaillances locales, car il est très peu probable que les trois copies soient affectées en même temps. Dans le cas du stockage LRS, il n’y a pas de géoredondance. Par conséquent, le disque n’est pas protégé contre les défaillances graves qui peuvent avoir un impact sur une unité de stockage ou un centre de données entier.

Avec les stockages GRS et RA-GRS, trois copies de vos données sont conservées dans la région primaire (que vous avez choisie) et trois autres copies sont conservées dans une région secondaire correspondante (définie par Azure). Par exemple, si vous stockez des données dans la région Ouest des États-Unis, celles-ci sont répliquées dans la région Est des États-Unis. Cette opération est effectuée en mode asynchrone et il existe un léger délai entre les mises à jour vers les régions primaires et secondaires. Les réplicas des disques sur le site secondaire sont cohérents par disque (avec le délai), mais les réplicas de plusieurs disques actifs peuvent ne pas être synchronisés **entre eux**. Pour avoir des réplicas cohérents sur plusieurs disques, les captures instantanées cohérentes sont nécessaires.

La principale différence entre les stockages GRS et RA-GRS est que, avec l’option RA-GRS, vous pouvez lire la copie secondaire à tout moment. S’il existe un problème à cause duquel les données de la région primaire sont inaccessibles, l’équipe Azure s’efforce de rétablir l’accès. Quand la région primaire est en panne, si vous avez activé le stockage RA-GRS, vous pouvez accéder aux données dans le centre de données secondaire. Par conséquent, si vous envisagez de lire à partir du réplica alors que la région primaire n’est pas accessible, le stockage RA-GRS doit être envisagé.

S’il s’agit d’une interruption importante, l’équipe Azure peut déclencher un basculement géographique et modifier les entrées DNS principales pour qu’elles pointent vers le stockage secondaire. À ce stade, si vous avez activé le stockage GRS ou RA-GRS, vous pouvez accéder aux données dans la région utilisée comme région secondaire. En d’autres termes, si votre compte de stockage est GRS et qu’un problème survient, vous pouvez accéder au stockage secondaire uniquement s’il existe un basculement géographique.

Pour plus d’informations, consultez [Que faire en cas de panne du stockage Azure](storage-disaster-recovery-guidance.md). 

Notez que Microsoft contrôle si un basculement se produit. Le basculement n’est pas contrôlé par le compte de stockage. Par conséquent, il n’est pas décidé par les clients individuels. Pour implémenter la récupération d’urgence pour des comptes de stockage ou disques de machine virtuelle spécifiques, vous devez utiliser les techniques décrites précédemment dans cet article.



[1]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/storage-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
