---
title: "Préparation du déploiement d’Azure Site Recovery | Microsoft Docs"
description: "Cet article décrit comment préparer le déploiement de la réplication avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/05/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5614c39d914d5ae6fde2de9c0d9941e7b93fc10f
ms.openlocfilehash: 8a4d265694e5eef438b0560a42ea5a95c04f9b02


---
# <a name="prepare-for-azure-site-recovery-deployment"></a>Préparer le déploiement d’Azure Site Recovery
Lisez cet article pour avoir une vue d’ensemble de haut niveau de la configuration requise pour le déploiement en rapport avec chaque scénario de réplication pris en charge par le service Azure Site Recovery. Après avoir pris connaissance de la configuration générale requise pour chaque scénario, vous pouvez suivre le lien vers les détails de déploiement spécifiques à chaque déploiement.

Après lecture de cet article, publiez vos commentaires ou questions au bas de celui-ci, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d'ensemble
Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Votre stratégie BCDR doit assurer la sécurisation et la récupération des données d’entreprise, ainsi que la disponibilité continue des charges de travail suite à un sinistre.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

## <a name="select-your-deployment-model"></a>Sélectionner votre modèle de déploiement
Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle classique de gestion des services. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://ms.portal.azure.com/), qui gère les deux modèles de déploiement.

Site Recovery est disponible dans le portail classique et le portail Azure. Dans le Portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle classique de gestion des services. Dans le portail Azure, vous pouvez prendre en charge les déploiements classiques et Ressource Manager. [En savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement à l’aide du portail Azure.

Lorsque vous choisissez un modèle de déploiement, tenez compte des remarques suivantes :

* Nous vous recommandons d’utiliser le [portail Azure](https://portal.azure.com) et le modèle Resource Manager dans la mesure du possible.
* Site Recovery propose une expérience de prise en main plus simple et plus intuitive dans le portail Azure.
* Le portail Azure vous permet de répliquer des machines aussi bien sur un stockage classique sur un stockage Resource Manager dans Azure. Dans le portail Azure, vous pouvez également utiliser des comptes de stockage LRS ou GRS.
* Le portail Azure regroupe les services Site Recovery et Backup dans un même coffre Recovery Services afin de vous permettre de définir et gérer les services BCDR à partir d’un emplacement unique.
* Les utilisateurs dotés d’abonnements Azure configurés avec le programme du fournisseur de solutions cloud (CSP) peuvent désormais gérer les opérations Site Recovery dans le Portail Azure.
* La réplication de machines virtuelles VMware ou d’ordinateurs physiques dans Azure via le portail Azure offre plusieurs nouvelles fonctionnalités, notamment la prise en charge de Premium Storage et la possibilité d’exclure certains disques de la réplication.

## <a name="select-your-deployment-scenario"></a>Sélectionner votre scénario de déploiement
| **Déploiement** | **Détails** | **Portail Azure** | **Portail classique** | **PowerShell** |
| --- | --- | --- | --- | --- |
| **Machines virtuelles VMware vers Azure** |Réplication de machines virtuelles VMware dans Azure Storage |Dans le portail Azure, les machines virtuelles peuvent basculer sur un stockage classique ou sur un stockage Resource Manager<br/><br/> Un déploiement dans le [portail Azure](site-recovery-vmware-to-azure.md) vous garantit une expérience de déploiement rationalisée et vous procure un certain nombre d’avantages. |Dans le portail Azure Classic, vous pouvez effectuer un déploiement à l’aide du [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers un stockage Azure classique.<br/><br/>  Il existe également un ancien modèle qu’il est préférable de ne pas utiliser pour les nouveaux déploiements. |PowerShell n’est pas actuellement pris en charge. |
| **Machines virtuelles Hyper-V vers Azure** |Réplication de machines virtuelles Hyper-V dans Azure Storage. Les machines virtuelles peuvent résider sur des hôtes Hyper-V gérés dans des clouds VMM System Center ou sans VMM. |Dans le portail Azure, les machines virtuelles peuvent basculer vers un stockage standard ou vers un stockage Resource Manager.<br/><br/>  Le portail Azure offre une expérience de déploiement rationalisée. [En savoir plus](site-recovery-vmm-to-azure.md) sur la réplication de machines virtuelles Hyper-V dans des clouds VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md) sur la réplication de machines virtuelles Hyper-V (sans VMM). |Dans le portail Azure Classic, vous pouvez basculer des machines virtuelles vers le stockage Azure classique |Les déploiements PowerShell sont pris en charge. |
| **Serveurs physiques vers Azure** |Réplication de serveurs Windows ou Linux physiques dans Azure Storage |Dans le portail Azure, les serveurs peuvent basculer vers un stockage standard ou vers un stockage Resource Manager.<br/><br/> Un déploiement dans le [portail Azure](site-recovery-vmware-to-azure.md) vous garantit une expérience de déploiement rationalisée et vous procure un certain nombre d’avantages. |Dans le portail Azure Classic, vous pouvez effectuer un déploiement à l’aide du [modèle amélioré](site-recovery-vmware-to-azure-classic.md) et basculer vers un stockage Azure classique.<br/><br/>  Il existe également un ancien modèle qu’il est préférable de ne pas utiliser pour les nouveaux déploiements. |Les déploiements PowerShell ne sont pas actuellement pris en charge. |
| **Machines virtuelles VMware/serveurs physiques vers un site secondaire* |Réplication de machines virtuelles VMware ou de serveurs physiques Windows/Linux dans un site secondaire. [En savoir plus et télécharger](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide de l’utilisateur InMage Scout. |Non disponible dans le portail Azure |Dans le portail Classic, vous allez télécharger InMage Scout à partir d’un coffre Site Recovery. |Les déploiements PowerShell ne sont pas actuellement pris en charge |
| **Machines virtuelles Hyper-V vers un site secondaire** |Réplication de machines virtuelles Hyper-V résidant dans des clouds VMM vers un cloud secondaire |Dans le [portail Azure](site-recovery-vmm-to-vmm.md) , vous ne pouvez répliquer des machines virtuelles Hyper-V gérées dans des clouds VMM vers un site secondaire qu’à l’aide d’un réplica Hyper-V. La réplication SAN n’est pas actuellement prise en charge. |Dans le portail Azure Classic, vous pouvez répliquer des machines virtuelles Hyper-V gérées dans des clouds VMM vers un site secondaire à l’aide d’un [réplica Hyper-V](site-recovery-vmm-to-vmm-classic.md) ou d’une [Réplication SAN](site-recovery-vmm-san.md). |Les déploiements PowerShell sont pris en charge |

## <a name="check-what-you-need-for-deployment"></a>Vérifier vos besoins pour le déploiement
### <a name="replicate-to-azure"></a>Réplication vers Microsoft Azure
| **Prérequis** | **Détails** |
| --- | --- |
| **Compte Azure** |Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |
| **Stockage Azure** |Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement. Pour répliquer des données vers Azure, vous aurez besoin d’un [compte de stockage Azure](../storage/storage-introduction.md).<br/><br/>Si vous déployez Site Recovery dans le portail Classic, vous aurez besoin d’un ou de plusieurs [comptes de stockage GRS standard](../storage/storage-redundancy.md#geo-redundant-storage).<br/><br/> Si vous déployez Site Recovery dans le portail Azure, vous pouvez utiliser le compte de stockage GRS ou LRS.<br/><br/>  Si vous répliquez des machines virtuelles VMware ou des serveurs physiques dans le portail Azure, Premium Storage est pris en charge. Notez que si vous utilisez un compte Premium Storage, vous aurez aussi besoin d’un compte de stockage standard afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales. [Premium Storage](../storage/storage-premium-storage.md) est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence pour héberger les charges de travail nécessitant beaucoup d’E/S.<br/><br/>  Si vous souhaitez utiliser un compte Premium pour stocker les données répliquées, vous avez aussi besoin d’un compte Standard Storage afin de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales. |
| **Réseau Azure** |Pour répliquer dans Azure, vous aurez besoin d’un réseau Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles sont créées après le basculement.<br/><br/>  Si vous déployez dans le portail Classic, vous utiliserez un réseau classique. Si vous déployez dans le portail Azure, vous pouvez utiliser le réseau classique ou le réseau Resource Manager.<br/><br/>  Ce réseau doit se trouver dans la même région que le coffre. |
| **Mappage réseau (VMM vers Azure)** |Si vous répliquez de VMM vers Azure, le [mappage réseau](site-recovery-network-mapping.md) garantit que les machines virtuelles Azure sont connectées aux réseaux appropriés après le basculement.<br/><br/>  Pour configurer le mappage réseau, vous devez configurer les réseaux de machines virtuelles dans le portail VMM. |
| **Local** |**Machines virtuelles VMware**: vous aurez besoin d’un ordinateur local exécutant les composants Site Recovery, les hôtes VMware vSphere/le serveur vCenter et les machines virtuelles que vous souhaitez répliquer. [En savoir plus](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Serveurs physiques**: si vous répliquez des serveurs physiques, vous avez besoin d’ordinateurs en local exécutant les composants Site Recovery et les serveurs physiques que vous souhaitez répliquer. [En savoir plus](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Si vous souhaitez [restaurer](site-recovery-failback-azure-to-vmware.md) après un basculement vers Azure, vous aurez besoin d’une infrastructure VMware.<br/><br/> **Machines virtuelles Hyper-V**: If you want to replicate Machines virtuelles Hyper-V in VMM clouds you'll need a VMM server, and Hyper-V hosts on which VMs you want to protect are located. [En savoir plus](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/>  Si vous souhaitez répliquer des machines virtuelles Hyper-V sans VMM, vous aurez besoin des hôtes Hyper-V sur lesquels résident les machines virtuelles. [En savoir plus](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Machines protégées** |Les ordinateurs protégés qui seront répliqués vers Azure doivent respecter les [conditions préalables pour Azure](#azure-virtual-machine-requirements) décrites ci-dessous. |

### <a name="replicate-to-a-secondary-site"></a>Réplication vers un site secondaire
| **Prérequis** | **Détails** |
| --- | --- |
| **Compte Azure** |Vous aurez besoin d’un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |
| **Local** |**Machines virtuelles VMware**: sur le site primaire, vous aurez besoin d’un serveur de traitement pour la mise en cache, la compression et le chiffrement des données de réplication avant leur envoi au site secondaire. Sur le site secondaire, vous devez installer un serveur de configuration pour gérer et surveiller le déploiement, ainsi qu’un serveur cible maître. Pour simplifier la gestion, nous vous recommandons également d’utiliser un serveur vContinuum. Vous avez également besoin d’un ou plusieurs ordinateurs hôtes VMware vSphere et éventuellement d’un serveur vCenter. [En savoir plus](site-recovery-vmware-to-vmware.md)<br/><br/> **sur les machines virtuelles Hyper-V gérées dans des clouds VMM**: vous devez configurer des serveurs VMM et des hôtes Hyper-V contenant les machines virtuelles que vous souhaitez répliquer. [En savoir plus](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Mappage réseau (VMM vers VMM)** |Si vous répliquez de VMM vers VMM, un [mappage réseau](site-recovery-network-mapping.md) garantit que les machines virtuelles de réplica sont connectées aux bons réseaux après le basculement et qu’elles sont placées de manière optimale sur les hôtes Hyper-V. Pour configurer le mappage réseau, vous devez configurer les réseaux de machines virtuelles sur vos serveurs VMM. |
| **Mappage de stockage** |Si vous répliquez de VMM vers VMM, vous pouvez éventuellement configurer un [mappage de stockage](site-recovery-storage-mapping.md) pour spécifier la cible de stockage des machines virtuelles répliquées. Le mappage de stockage peut être configuré pour la réplication SAN et pour la réplication de réplicas Hyper-V.<br/><br/>  Le mappage de stockage n’est actuellement pas pris en charge dans le portail Azure. |

## <a name="verify-url-access"></a>Vérifier l’accès URL
Assurez-vous que ces URL sont accessibles à partir des serveurs.

| **URL** | **VMM vers VMM** | **VMM vers Azure** | **Hyper-V vers Azure** | **VMware vers Azure** |
| --- | --- | --- | --- | --- |
| *.accesscontrol.windows.net |Autoriser |Autoriser |Autoriser |Autoriser |
| *.backup.windowsazure.com |Non requis |Autoriser |Autoriser |Autoriser |
| *.hypervrecoverymanager.windowsazure.com |Autoriser |Autoriser |Autoriser |Autoriser |
| *.store.core.windows.net |Autoriser |Autoriser |Autoriser |Autoriser |
| *.blob.core.windows.net |Non requis |Autoriser |Autoriser |AUTORISER |
| https://www.msftncsi.com/ncsi.txt |AUTORISER |Autoriser |Autoriser |AUTORISER |
| https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi |Non requis |Non requis |Non requis |Autoriser |

## <a name="azure-virtual-machine-requirements"></a>Configuration requise des machines virtuelles Azure
Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Vous devrez vérifier que les machines virtuelles locales à protéger répondent à la configuration requise d’Azure.

| **Fonctionnalité** | **Configuration requise** | **Détails** |
| --- | --- | --- |
| Hôte Hyper-V |Doit exécuter Windows Server 2012 R2 |La vérification des conditions préalables échoue si le système d’exploitation n’est pas pris en charge |
| Hyperviseur VMware |Système d’exploitation pris en charge |[Vérifier la configuration requise](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |
| Système d’exploitation invité |Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Architecture du système d’exploitation invité |64 bits |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Taille du disque du système d’exploitation |Jusqu’à 1 023 Go |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Nombre de disques du système d’exploitation |1 |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Nombre de disques de données |16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Taille du disque dur virtuel de données |Jusqu’à 1 023 Go |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Adaptateurs réseau |Prise en charge de plusieurs adaptateurs réseau. | |
| Adresse IP statique |Pris en charge |Si la machine virtuelle principale utilise une adresse IP statique, vous pouvez spécifier l’adresse associée à la machine qui sera créée dans Azure. Notez qu’une adresse IP statique pour une machine virtuelle Linux s’exécutant sur Hyper-V n’est pas prise en charge. |
| Disque iSCSI |Non pris en charge |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Disque dur virtuel partagé |Non pris en charge |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Disque FC |Non pris en charge |La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. |
| Format de disque dur |Disque dur virtuel (VHD)  <br/><br/>  VHDX |Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX. |
| Bitlocker |Non pris en charge |Bitlocker doit être désactivé préalablement à la protection d’une machine virtuelle. |
| Nom de la machine virtuelle |Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre. |Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery |
| Type de machine virtuelle |<p>Génération 1</p> <p>Génération 2 - Windows</p> |Une machine virtuelle de génération 2 dont le type de disque de système d’exploitation est disque de base et qui inclut 1 ou 2 volumes de données avec un format de disque VHDX inférieur à 300 Go est prise en charge. Les machines virtuelles Linux de génération 2 ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="optimizing-your-deployment"></a>Optimisation de votre déploiement
Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

* **Taille du volume du système d’exploitation**: quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
* **Taille du disque de données**: si vous répliquez vers Microsoft Azure, vous pouvez posséder jusqu’à 32 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 32 To.
* **Limites de plan de récupération**: Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
* **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de limites par défaut liées aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
* **Planification de la capacité**: en savoir plus sur la [planification de la capacité](site-recovery-capacity-planner.md) pour Site Recovery.
* **Bande passante de réplication**: si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
  * **ExpressRoute**: Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
  * **Trafic de réplication**: Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
  * **Trafic réseau**: pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination.  De plus, si vous répliquez vers Azure Site Recovery à l’aide de l’agent Azure Backup, vous pouvez lui appliquer une limitation. [En savoir plus](https://support.microsoft.com/kb/3056159).
* **RTO**: pour mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
* **RPO**: Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Azure.

## <a name="service-urls"></a>URL de service
Assurez-vous que ces URL sont accessibles à partir du serveur.

| **URLs** | **VMM vers VMM** | **VMM vers Azure** | **Site Hyper-V vers Azure** | **VMware vers Azure** |
| --- | --- | --- | --- | --- |
|  \*.accesscontrol.windows.net |Accès requis |Accès requis |Accès requis |Accès requis |
|  \*.backup.windowsazure.com | |Accès requis |Accès requis |Accès requis |
|  \*.hypervrecoverymanager.windowsazure.com |Accès requis |Accès requis |Accès requis |Accès requis |
|  \*.store.core.windows.net |Accès requis |Accès requis |Accès requis |Accès requis |
|  \*.blob.core.windows.net | |Accès requis |Accès requis |Accès requis |
|  https://www.msftncsi.com/ncsi.txt |Accès requis |Accès requis |Accès requis |Accès requis |
|  https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi | | | |Accès requis |

## <a name="next-steps"></a>Étapes suivantes
Après avoir appris et comparé les conditions requises générales du déploiement, vous pouvez lire le détail des conditions requises et démarrer le déploiement de chaque scénario.

* [Répliquez des machines virtuelles VMware sur Microsoft Azure.](site-recovery-vmware-to-azure-classic.md)
* [Répliquer des serveurs physiques dans Azure](site-recovery-vmware-to-azure-classic.md)
* [Répliquer un serveur Hyper-V dans des clouds VMM vers Azure](site-recovery-vmm-to-azure.md)
* [Réplication de machines virtuelles Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
* [Réplication de machines virtuelles Hyper-V vers un site secondaire](site-recovery-vmm-to-vmm.md)
* [Réplication de machines virtuelles Hyper-V vers un site secondaire avec SAN](site-recovery-vmm-san.md)
* [Réplication de machines virtuelles Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)



<!--HONumber=Nov16_HO3-->


