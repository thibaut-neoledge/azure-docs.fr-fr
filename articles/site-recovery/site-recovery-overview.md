<properties
	pageTitle="Qu’est ce que Site Recovery ? | Microsoft Azure" 
	description="Présente le service Azure Site Recovery et explique comment il peut être déployé." 
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
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  Qu’est-ce que Site Recovery ?

Bienvenue dans Azure Site Recovery ! Commencez par cet article pour obtenir une présentation rapide du service Site Recovery et voir de quelle façon il peut contribuer à votre stratégie de récupération d’urgence et de continuité des activités.

Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article s’applique aux deux modèles. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

## Vue d'ensemble

Une partie importante de la stratégie de continuité des activités et de récupération d’urgence de votre organisation consiste à savoir comment maintenir les charges de travail et les applications d’entreprise opérationnelles lorsque des interruptions planifiées et non planifiées se produisent.

Site Recovery aide à atteindre cet objectif en coordonnant la réplication, le basculement et la récupération des charges de travail et des applications afin qu’elles soient disponibles à partir d’un site secondaire si votre site principal tombe en panne.

## Pourquoi utiliser Azure Site Recovery ? 

Voici ce que Site Recovery peut faire pour votre entreprise :

- **Simplifier votre stratégie de continuité des activités et de récupération d’urgence **: Site Recovery facilite la gestion de la réplication, du basculement et de la récupération de plusieurs charges de travail et applications métier à partir d’un emplacement unique. Site Recovery orchestre la réplication et le basculement, sans intercepter les données de vos applications ni posséder des informations à leur sujet.
- **Fournir une réplication flexible **: à l’aide de Site Recovery, vous pouvez répliquer des charges de travail s’exécutant sur des machines virtuelles Hyper-V et VMware, et des serveurs physiques Windows/Linux. 
- **Faciliter le basculement et la récupération** : Site Recovery fournit des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence sans affecter les environnements de production. Vous pouvez également exécuter des basculements planifiés avec une perte de données zéro pour les interruptions attendues, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Après le basculement, vous pouvez procéder à une restauration automatique sur vos sites principaux. Site Recovery fournit des plans de récupération qui peuvent inclure des scripts et des classeurs Azure Automation afin que vous puissiez personnaliser le basculement et la récupération d’applications multiniveaux. 
- **Éliminer le centre de données secondaire **: vous pouvez procéder à une réplication vers un site secondaire local ou vers Azure. L’utilisation d’Azure comme destination de la récupération d’urgence permet d’éliminer le coût et la complexité de la maintenance d’un site secondaire. Les données répliquées sont stockées dans Azure Storage, avec toute la résilience que cela implique.
- **S’intégrer aux technologies existantes de continuité des activités et de récupération d’urgence **: Site Recovery collabore avec les fonctionnalités de continuité des activités et de récupération d’urgence d’autres applications. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, notamment la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité. 

## Que puis-je répliquer ?

Voici une synthèse des éléments pouvant être répliqués par Site Recovery.

![Local vers local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **RÉPLICATION DEPUIS** | **RÉPLICATION VERS** | **ARTICLE**
---|---|---|---
Charges de travail s’exécutant sur des machines virtuelles VMware | Serveur VMware local | Stockage Azure | [Déployer](site-recovery-vmware-to-azure-classic.md)
Charges de travail s’exécutant sur des machines virtuelles VMware | Serveur VMware local | Site VMware secondaire | [Déployer](site-recovery-vmware-to-vmware.md) 
Charges de travail s’exécutant sur des machines virtuelles Hyper-V | Serveur hôte Hyper-V local dans le cloud VMM | Stockage Azure | [Déployer](site-recovery-vmm-to-azure.md)
Charges de travail s’exécutant sur des machines virtuelles Hyper-V | Serveur hôte Hyper-V local dans le cloud VMM | Site VMM secondaire | [Déployer](site-recovery-vmm-to-vmm.md)
Charges de travail s’exécutant sur des machines virtuelles Hyper-V | Serveur hôte Hyper-V local dans le cloud VMM avec le stockage SAN| Site VMM secondaire avec stockage SAN | [Déployer](site-recovery-vmm-san.md)
Charges de travail s’exécutant sur des machines virtuelles Hyper-V | Site Hyper-V local (sans VMM) | Stockage Azure | [Déployer](site-recovery-hyper-v-site-to-azure.md)
Charges de travail s’exécutant sur des serveurs physiques Windows/Linux | Serveur physique local | Stockage Azure | [Déployer](site-recovery-vmware-to-azure-classic.md)
Charges de travail s’exécutant sur des serveurs physiques Windows/Linux | Serveur physique local | Centre de données secondaire | [Déployer](site-recovery-vmware-to-vmware.md) 


## Quelles charges de travail puis-je protéger ?

Site Recovery peut contribuer à la continuité des activités et à la récupération d’urgence compatibles avec les applications afin que les charges de travail et les applications continuent de s’exécuter de manière cohérente en cas de pannes. Site Recovery présente les caractéristiques suivantes :

- **Instantanés de cohérence de l’application** : réplication utilisant des instantanés de cohérence de l’application pour les applications à niveau unique ou multiniveaux. **Réplication quasi synchrone **: fréquence de réplication de 30 secondes pour Hyper-V, et réplication continue pour VMware. **Intégration à SQL Server AlwaysOn** : vous pouvez gérer le basculement des groupes de disponibilité dans les plans de récupération de Site Recovery. 
- **Plans de récupération flexibles** : vous pouvez créer et personnaliser des plans de récupération avec des scripts externes, des actions manuelles et des Runbooks Azure Automation qui vous permettent de récupérer une pile d’application entière en un seul clic.
- **Bibliothèque Automation** : une bibliothèque Azure Automation complète fournit des scripts prêts pour la production, propres aux applications qui peuvent être téléchargés et intégrés à Site Recovery.-** Gestion de réseau simple **: la gestion de réseau avancée de Site Recovery et d’Azure simplifie les conditions requises du réseau d’application, notamment la réservation des adresses IP, la configuration des équilibreurs de charge et l’intégration d’Azure Traffic Manager pour garantir des basculement de réseau efficaces.


## Étapes suivantes

- Pour en savoir plus, consultez la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md).
- Pour en savoir plus sur l’architecture de Site Recovery, consultez l’article [Comment Azure Site Recovery fonctionne-t-il ?](site-recovery-components.md).
 

<!---HONumber=AcomDC_0608_2016-->