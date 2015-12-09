<properties
	pageTitle="Qu’est-ce que Site Recovery ?" 
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et serveurs physiques situés en local sur Microsoft Azure, ou sur un site local secondaire." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="11/29/2015" 
	ms.author="raynew"/>

#  Qu’est-ce que Site Recovery ?

Site Recovery est un service Azure qui met en œuvre la stratégie de continuité des activités et de récupération d’urgence (ou BCDR pour Business Continuity and Disaster Recovery) de votre entreprise en coordonnant la réplication de vos serveurs locaux et machines virtuelles sur un centre de données local secondaire ou sur Azure. Site Recovery gère la réplication, et vous pouvez lancer le basculement et la récupération d’un simple clic. Parcourez une liste de questions courantes dans le [FAQ](site-recovery-faq.md)


## Pourquoi utiliser Azure Site Recovery ? 
- **Récit de continuité d’activité et récupération d’urgence simplifiées (Business Continuity Disaster Recovery, BCDR)** : Site Recovery facilite la gestion de la réplication, du basculement et de la récupération pour vos charges de travail et applications locales.
- **Réplication flexible** : vous pouvez répliquer des serveurs locaux, des machines virtuelles Hyper-V et des machines virtuelles VMware. Site Recovery utilise la réplication intelligente : seuls les blocs de données sont répliqués, et non l’ensemble du disque dur virtuel, lors de la réplication initiale. Lors de la réplication en continu, seul le delta des changements est répliqué. Site Recovery prend en charge le transfert de données hors ligne et utilise des optimiseurs WAN. 
- **Éliminer le besoin d’un centre de données secondaire** : Site Recovery peut automatiser la réplication entre les centres de données, mais offre également la possibilité d’ignorer un emplacement local secondaire en effectuant la réplication sur Azure. Les données répliquées sont stockées dans Azure Storage, avec toute la résilience que cela implique.


## Scénarios de déploiement

Ce tableau récapitule les scénarios de réplication pris en charge par Site Recovery.

**REPLICATE** | **RÉPLICATION DEPUIS** | **RÉPLICATION VERS** | **ARTICLE**
---|---|---|---
Machines virtuelles VMware | Serveur VMware local | Stockage Azure | [Déployer](site-recovery-vmware-to-azure.md)
Serveur Windows/Linux physique | Serveur physique local | Stockage Azure | [Déployer](site-recovery-vmware-to-azure.md)
Machines virtuelles Hyper-V | Serveur hôte Hyper-V local dans le cloud VMM | Stockage Azure | [Déployer](site-recovery-vmm-to-azure.md)
Machines virtuelles Hyper-V | Site Hyper-V local (un ou plusieurs serveurs hôte Hyper-V) | Stockage Azure | [Déployer](site-recovery-hyper-v-site-to-azure.md)
Machines virtuelles Hyper-V locales| Serveur hôte Hyper-V local dans le cloud VMM | Serveur hôte Hyper-V local dans le cloud VMM dans le centre de données secondaire | [Déployer](site-recovery-vmm-to-vmm)
Machines virtuelles Hyper-V | Serveur hôte Hyper-V local dans le cloud VMM avec le stockage SAN| Serveur hôte Hyper-V local dans le cloud VMM avec le stockage SAN dans le centre de données secondaire | [Déployer](site-recovery-vmm-san.md)
Machines virtuelles VMware | Serveur VMware local | Centre de données secondaire exécutant VMware | [Déployer](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 
Serveur Windows/Linux physique | Serveur physique local | Centre de données secondaire | [Déployer](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 

Ils sont résumés dans les schémas suivants.

![Local vers local](./media/site-recovery-overview/asr-overview-graphic.png)

## Quelles charges de travail puis-je protéger ?

Site Recovery vous assiste dans la continuité d’activité tenant compte des appliances. Vous pouvez utiliser Site Recovery pour orchestrer la récupération d’urgence pour Windows et les applications tierces. Cette protection tenant compte des applications fournit les éléments suivants :


- Réplication presque synchrone avec des RPO de 30 secondes pour Hyper-V et la réplication continue pour VMware afin de répondre aux besoins des applications les plus critiques.
- Captures instantanées cohérentes de l’application pour les applications uniques ou multiniveau.
- Intégration avec SQL Server AlwaysOn, partenariat avec d’autres technologies de réplication au niveau des applications, y compris la réplication Active Directory, les groupes de disponibilité de base de données (DAG) Exchange et Oracle Data Guard.
- Les plans de récupération flexibles qui vous permettent de récupérer une pile de l’application entière en un seul clic, et incluent les scripts externes ou des actions manuelles. 
- La gestion réseau avancée dans Site Recovery et Azure simplifie la configuration réseau requise pour une application, notamment la réservation d’adresses IP, la configuration d’équilibreurs de charge ou l’intégration d’Azure Traffic Manager pour les commutations réseau à RTO faible.
- Une bibliothèque d’automatisation avancée qui fournit des scripts spécifiques d’application prêts pour la production, qui peuvent être téléchargés et intégrés avec Site Recovery.  


En savoir plus en consultant [Aide relative à la charge de travail Site Recovery](site-recovery-workload.md).


## Étapes suivantes

Une fois que vous avez fini de consulter cette présentation, vous pouvez [en savoir plus](site-recovery-components.md) sur l’architecture Site Recovery.
 

<!---HONumber=AcomDC_1203_2015-->