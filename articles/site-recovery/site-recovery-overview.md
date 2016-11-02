<properties
    pageTitle="Qu’est-ce que Site Recovery ? | Microsoft Azure"
    description="Présente le service Azure Site Recovery et récapitule les différents scénarios de déploiement associés."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>


#  <a name="what-is-site-recovery?"></a>Qu’est-ce que Site Recovery ?

Bienvenue dans Azure Site Recovery ! Cet article fournit une vue d’ensemble rapide du service Site Recovery, et détaille la contribution qu’il apporte à votre entreprise.

Votre organisation a besoin d’une stratégie de continuité des activités et de récupération d’urgence qui assure la disponibilité et la sécurité des applications, charges de travail et données pendant les temps d’arrêt prévus et imprévus et qui puisse rétablir au plus vite les conditions de travail normales. Site Recovery est un service Azure qui participe à cette stratégie.

Ce service assure l’orchestration de la réplication des charges de travail en cours d’exécution sur des serveurs physiques en local, ainsi que sur des machines virtuelles. Vous pouvez répliquer des machines virtuelles et des serveurs à partir d’un centre de données principal vers le cloud (Azure), ou vers un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous basculez sur le site secondaire pour préserver l’accès et la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery dans le portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle de gestion des services classique. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://portal.azure.com), qui gère les deux modèles de déploiement.

- Site Recovery est disponible dans le portail Azure Classic et dans le portail Azure.
- Dans le portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle de gestion des services classique.
- Dans le portail Azure, vous pouvez prendre en charge les déploiements classiques et Ressource Manager. 

Les informations contenues dans cet article s’appliquent aux déploiements du portail Azure Classic et du portail Azure. Les différences sont indiquées, le cas échéant.


## <a name="why-deploy-site-recovery?"></a>Pour déployer Azure Site Recovery ?

Voici ce que Site Recovery peut faire pour votre entreprise :

- **Simplifier la récupération BCDR** : vous pouvez gérer la réplication, le basculement et la récupération de plusieurs charges de travail dans un emplacement unique dans le portail Azure. Site Recovery orchestre la réplication et le basculement, sans intercepter les données de vos applications ni posséder des informations à leur sujet.
- **Fournir une réplication flexible** : à l’aide de Site Recovery, vous pouvez répliquer des charges de travail s’exécutant sur des machines virtuelles Hyper-V et VMware prises en charge, et sur des serveurs physiques Windows/Linux.
- **Exécuter des tests de récupération simples** : Site Recovery fournit des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence, sans affecter les environnements de production.
- **Basculement et récupération** : vous pouvez également exécuter des basculements planifiés pour les interruptions attendues, sans perte de données, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Après le basculement, vous pouvez procéder à une restauration automatique sur vos sites principaux. Site Recovery fournit des plans de récupération qui peuvent inclure des scripts et des classeurs Azure Automation afin que vous puissiez personnaliser le basculement et la récupération d’applications multiniveaux.
- **Éliminer les besoins de centres de données secondaires** : vous pouvez répliquer des charges de travail vers Azure plutôt que vers un site secondaire. Ceci réduit les coûts et la complexité associés à la gestion d’un centre de données secondaire. Les données répliquées sont stockées dans Azure Storage, avec toute la résilience que cela implique. Les machines virtuelles sont créées avec les données répliquées lors du basculement.
- **S’intégrer aux technologies existantes de continuité des activités et de récupération d’urgence** : Site Recovery s’intègre avec d’autres fonctions BCDR. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, notamment la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.

## <a name="what-can-i-replicate?"></a>Que puis-je répliquer ?

Voici une synthèse des éléments pouvant être répliqués par Site Recovery.

![Local vers local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **RÉPLICATION VERS** 
---|---
Charges de travail s’exécutant sur des machines virtuelles VMware | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)
Charges de travail exécutées sur des machines virtuelles Hyper-V locales, gérées dans des clouds VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secondaire](site-recovery-vmm-to-vmm.md) 
Charges de travail exécutées sur des machines virtuelles Hyper-V locales, gérées dans des clouds VMM, avec stockage SAN|  [Site secondaire](site-recovery-vmm-san.md)
Charges de travail exécutées sur des machines virtuelles Hyper-V locales, sans VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Charges de travail s’exécutant sur des serveurs physiques Windows/Linux locaux | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect?"></a>Quelles charges de travail puis-je protéger ?

Site Recovery permet une continuité des activités et à une récupération d’urgence compatibles avec les applications, afin que les charges de travail et les applications continuent de s’exécuter de manière cohérente en cas de pannes. Site Recovery présente les caractéristiques suivantes :

- **Instantanés de cohérence de l’application** : réplication des machines utilisant des instantanés de cohérence de l’application pour les applications à niveau unique ou multiniveaux. En plus de capturer des données de disque, ces instantanés capturent toutes les données en mémoire, et toutes les transactions en cours.
- **Réplication quasi synchrone** : Site Recovery propose une fréquence de réplication de 30 secondes pour Hyper-V, et une réplication continue pour VMware.
- **Plans de récupération flexibles** : vous pouvez créer et personnaliser des plans de récupération avec des scripts externes et des actions manuelles. L’intégration avec les Runbooks Azure Automation permet de récupérer tout une pile d’applications en un seul clic.
- **Intégration à SQL Server AlwaysOn**: vous pouvez gérer le basculement des groupes de disponibilité dans les plans de récupération de Site Recovery.
- **Bibliothèque d’automatisation**: une bibliothèque Azure Automation avancée qui fournit des scripts spécifiques d’application prêts pour la production, qui peuvent être téléchargés et intégrés avec Site Recovery.
- **Gestion réseau simple** : la gestion réseau avancée dans Site Recovery et Azure simplifie les conditions requises du réseau d’application, notamment la réservation d’adresses IP, la configuration d’équilibreurs de charge et l’intégration d’Azure Traffic Manager pour les commutations réseau efficaces.


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus, consultez la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md)
- Pour en savoir plus sur l’architecture de Site Recovery, consultez l’article [Comment Azure Site Recovery fonctionne-t-il ?](site-recovery-components.md)
 



<!--HONumber=Oct16_HO2-->


