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
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 5ed46a6bd8931d3b8829a053ace12fd219f108c4
ms.openlocfilehash: 6430ae9a97888ef993cd3dd35eea765fe95bb43f

---

# <a name="prepare-for-azure-site-recovery-deployment"></a>Préparer le déploiement d’Azure Site Recovery

Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Cet article résume les exigences pour chaque scénario de réplication Site Recovery et fournit des liens vers des procédures pas à pas de déploiement détaillées.  

Envoyez vos commentaires au bas de cet article ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="azure-deployment-models"></a>Modèles de déploiement Azure

Azure dispose de deux [modèles de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle classique. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://ms.portal.azure.com/), qui gère les deux modèles de déploiement.

Des nouveaux scénarios Site Recovery doivent être déployés dans le [portail Azure](https://portal.azure.com). Ce portail fournit de nouvelles fonctionnalités et une expérience de déploiement optimisée. Des coffres peuvent être gérés dans le portail classique, mais vous ne pouvez pas en créer de nouveaux.


## <a name="deployment-scenarios"></a>Scénarios de déploiement

Voici ce que vous pouvez répliquer avec Site Recovery :

| **Déploiement** | **Détails** | **Portail Azure** | **Portail classique** | **Déployer PowerShell** |
| --- | --- | --- | --- | --- |
| [VMware vers Azure](site-recovery-vmware-to-azure.md) | Réplication de machines virtuelles VMware locales dans Azure Storage | Utilisez Resource Manager ou le stockage et le réseaux classiques. Basculez vers des machines virtuelles Resource Manager ou classiques. | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Non pris en charge pour le moment. |
| [Serveurs physiques vers Azure](site-recovery-vmware-to-vmware.md) | Réplication de serveurs Windows ou Linux physiques dans Azure Storage | Utilisez Resource Manager ou le stockage et le réseaux classiques. Basculez vers des machines virtuelles Resource Manager ou classiques. | Mode Maintenance uniquement. Il est impossible de créer des coffres.  |
| [Machines virtuelles Hyper-V dans des clouds VMM vers Azure](site-recovery-vmm-to-azure.md) | Répliquez des de machines virtuelles Hyper-V locales dans des clouds VMM dans le stockage Azure.<br/><br/> | Utilisez Resource Manager ou le stockage et le réseaux classiques. Basculez vers des machines virtuelles Resource Manager ou classiques.   | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Pris en charge |
| [Machines virtuelles Hyper-V dans Azure (sans VMM)](site-recovery-hyper-v-site-to-azure.md) | Répliquez des machines virtuelles Hyper-V locales dans Azure Storage. | Utilisez Resource Manager ou le stockage et le réseaux classiques. Basculez vers des machines virtuelles Resource Manager ou classiques. | Mode Maintenance uniquement. Il est impossible de créer des coffres. | Pris en charge |
| Serveurs de machines virtuelles VMware/physiques pour site secondaire (site-recovery-vmware-to-vmware.md) | Réplication de machines virtuelles VMware ou de serveurs physiques Windows/Linux dans un site secondaire.<br/><br/> [Téléchargez](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide de l’utilisateur InMage Scout. | Non disponible dans le portail Azure | Téléchargez InMage Scout à partir d’un coffre Site Recovery. | Non pris en charge |
| [Machines virtuelles Hyper-V vers un site secondaire](site-recovery-vmm-to-vmm.md) | Réplication de machines virtuelles Hyper-V résidant dans des clouds VMM vers un cloud secondaire  | La réplication utilise des réplicas Hyper-V standard. Le SAN n’est pas pris en charge. | Réplication à l’aide de réplicas Hyper-V ou de la [réplication SAN](site-recovery-vmm-san.md) | Pris en charge |

## <a name="requirements-for-replication-to-azure"></a>Configuration requise pour la réplication vers Azure

| **Prérequis** | **Détails** |
| --- | --- |
| **Compte Azure** | Un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |
| **Stockage Azure** | Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement. Vous avez besoin d’un [compte de stockage Azure](../storage/storage-introduction.md).<br/><br/>Dans le portail Azure, utilisez le stockage [GRS](../storage/storage-redundancy.md#geo-redundant-storage) ou LRS. Le portail classique ne prenait en charge que GRS.<br/><br/> Si vous répliquez des machines virtuelles VMware ou des serveurs physiques dans le portail Azure, le stockage Premium est pris en charge.<br/><br/>  |
| **Réseau Azure** | Vous devez disposer d’un réseau Azure auquel les machines virtuelles Azure se connecteront. <br/><br/> Dans le portail Azure, vous pouvez utiliser le réseau classique ou le réseau Resource Manager.<br/><br/> Ce réseau doit se trouver dans la même région que le coffre.<br/><br/> Si vous répliquez à partir de VMM vers Azure, vous allez configurer [le mappage réseau](site-recovery-network-mapping.md) entre les réseaux VMM VM et Azure pour vous assurer que les machines virtuelles Azure se connectent aux réseaux appropriés après le basculement. |
| **Local** |**Machines virtuelles VMware** : une machine locale exécutant des composants de Site Recovery. En outre, vous avez besoin d’hôtes VMware vSphere/d’un serveur vCenter et de machines virtuelles que vous souhaitez répliquer. [En savoir plus](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).<br/><br/> **Serveurs physiques** : machine locale exécutant des composants Site Recovery et des serveurs physiques que vous souhaitez répliquer. [En savoir plus](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites). Si vous souhaitez [restaurer](site-recovery-failback-azure-to-vmware.md) des serveurs physiques depuis Azure, vous avez besoin d’une infrastructure VMware.<br/><br/> **Machines virtuelles Hyper-V** : serveur VMM et des hôtes Hyper-V contenant des machines virtuelles que vous souhaitez répliquer. [En savoir plus](site-recovery-vmm-to-azure.md#on-premises-prerequisites).<br/><br/> Pour répliquer des machines virtuelles Hyper-V sans VMM, vous avez besoin uniquement d’hôtes Hyper-V. [En savoir plus](site-recovery-hyper-v-site-to-azure.md#on-premises-prerequisites). |
| **Machines protégées** | Les ordinateurs protégés qui seront répliqués vers Azure doivent respecter les [conditions préalables pour Azure](#azure-virtual-machine-requirements) décrites ci-dessous. |

## <a name="requirements-for-replication-to-a-secondary-site"></a>Configuration requise pour la réplication vers un site secondaire

| **Prérequis** | **Détails** |
| --- | --- |
| **Microsoft Azure** | Un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |
| **Local** |**Machines virtuelles VMware**: sur le site primaire, vous avez besoin d’un serveur de traitement pour la mise en cache, la compression et le chiffrement des données de réplication. Sur le site secondaire, vous devez installer un serveur de configuration pour gérer et surveiller le déploiement, ainsi qu’un serveur cible maître. Pour simplifier la gestion, nous vous recommandons également d’utiliser un serveur vContinuum. Vous avez également besoin d’un ou de plusieurs hôtes VMware vSphere et éventuellement d’un serveur vCenter. [En savoir plus](site-recovery-vmware-to-vmware.md)<br/><br/> **Machines virtuelles Hyper-V dans des clouds VMM** : serveurs VMM et hôtes Hyper-V contenant les machines virtuelles que vous souhaitez répliquer. [En savoir plus](site-recovery-vmm-to-vmm.md#on-premises-prerequisites). |
| **Réseau (VMM à VMM)** | Si vous répliquez de VMM vers VMM, vous définissez un [mappage réseau](site-recovery-network-mapping.md) pour garantir que les réplicas de machines virtuelles sont connectés aux bons réseaux après le basculement et qu’ils sont placés de manière optimale sur les hôtes Hyper-V. |

## <a name="url-access"></a>Accès URL

Ces URL doivent être disponibles depuis VMware, VMM et les serveurs hôtes Hyper-V.

**URL** | **VMM vers VMM** | **VMM vers Azure** | **Hyper-V vers Azure** | **VMware vers Azure** |
|--- | --- | --- | --- | ---
``*.accesscontrol.windows.net`` | AUTORISER | Autoriser | Autoriser | Autoriser
``*.backup.windowsazure.com`` | Non requis | Autoriser | Autoriser | Autoriser
``*.hypervrecoverymanager.windowsazure.com`` | Autoriser | Autoriser | Autoriser | Autoriser
``*.store.core.windows.net`` | Autoriser | Autoriser | Autoriser | Autoriser
``*.blob.core.windows.net`` | Non requis | Autoriser | Autoriser | Autoriser
``https://www.msftncsi.com/ncsi.txt`` | Autoriser | Autoriser | Autoriser | Autoriser
``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Non requis | Non requis | Non requis | Autoriser le téléchargement SQL
``time.windows.com`` | AUTORISER | Autoriser | Autoriser | Autoriser
``time.nist.gov`` | Autoriser | Autoriser | Autoriser | AUTORISER



## <a name="azure-virtual-machine-requirements"></a>Configuration requise des machines virtuelles Azure

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Les machines virtuelles locales à répliquer doivent répondent à la configuration requise d’Azure.

**Fonctionnalité** | **Configuration requise** | **Détails**
--- | --- | ---
**Hôte Hyper-V** | Doit exécuter Windows Server 2012 R2 ou une version ultérieure | La vérification des conditions préalables échoue si le système d’exploitation n’est pas pris en charge
**Hyperviseur VMware** | Système d’exploitation pris en charge | [Vérifier la configuration requise](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**Système d’exploitation invité** | Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Architecture du système d’exploitation invité** | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque du système d’exploitation** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques du système d’exploitation** | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Nombre de disques de données** | 16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Taille du disque dur virtuel de données** | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Adaptateurs réseau** | Prise en charge de plusieurs adaptateurs réseau. |
**Adresse IP statique** | Pris en charge | Si la machine virtuelle principale utilise une adresse IP statique, vous pouvez spécifier l’adresse associée à la machine qui sera créée dans Azure.<br/><br/> Une adresse IP statique pour une **machine virtuelle Linux s’exécutant sur Hyper-V** n’est pas prise en charge.
**Disque iSCSI** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque dur virtuel partagé** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Disque FC** | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
**Format de disque dur** | Disque dur virtuel (VHD)  <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
**BitLocker** | Non pris en charge | Bitlocker doit être désactivé préalablement à la protection d’une machine virtuelle.
**Nom de la machine virtuelle** | Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery
**Type de machine virtuelle** | Génération 1<br/><br/> Génération 2 - Windows | Les machines virtuelles de 2e génération avec un type de disque de système d’exploitation de base, qui inclut un ou deux volumes de données au format VHDX et d’une taille inférieure à 300 Go sont prises en charge.<br/><br/>. Les machines virtuelles Linux de 2e génération ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |

## <a name="deployment-optimization"></a>Optimisation du déploiement

Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

- **Taille du volume du système d’exploitation**: quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données** : si vous répliquez vers Azure, vous pouvez avoir jusqu’à 64 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 64 To.
- **Limites de plan de récupération**: Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de limites par défaut liées aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité**: en savoir plus sur la [planification de la capacité](site-recovery-capacity-planner.md) pour Site Recovery.
- **Bande passante de réplication**: si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
- **ExpressRoute**: Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
- **Trafic de réplication**: Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
- **Trafic réseau**: pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination.  De plus, si vous répliquez vers Azure Site Recovery à l’aide de l’agent Azure Backup, vous pouvez lui appliquer une limitation. [En savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO**: pour mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO**: Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Azure.



## <a name="next-steps"></a>Étapes suivantes
Après avoir examiné les exigences de déploiement générales, lisez les conditions requises détaillées et déployez un scénario.

* [Répliquez des machines virtuelles VMware sur Microsoft Azure.](site-recovery-vmware-to-azure.md)
* [Répliquer des serveurs physiques dans Azure](site-recovery-vmware-to-azure.md)
* [Répliquer un serveur Hyper-V dans des clouds VMM vers Azure](site-recovery-vmm-to-azure.md)
* [Réplication de machines virtuelles Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
* [Réplication de machines virtuelles Hyper-V vers un site secondaire](site-recovery-vmm-to-vmm.md)
* [Réplication de machines virtuelles Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)



<!--HONumber=Dec16_HO1-->


