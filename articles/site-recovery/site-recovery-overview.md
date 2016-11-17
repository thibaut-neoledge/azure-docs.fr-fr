---
title: "Qu’est-ce que Site Recovery ? | Microsoft Docs"
description: "Présente le service Azure Site Recovery et récapitule les différents scénarios de déploiement associés."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64d085bff08d9a824204851d32504fac3e79024c


---
# <a name="what-is-site-recovery"></a>Qu’est-ce que Site Recovery ?
Bienvenue dans le service Azure Site Recovery. Cet article propose une vue d’ensemble de Site Recovery.

Votre organisation a besoin d’une stratégie de continuité des activités et de récupération d’urgence qui assure la disponibilité et la sécurité des applications et des données pendant les temps d’arrêt prévus et imprévus et qui puisse rétablir au plus vite les conditions de travail normales.

Site Recovery contribue à votre stratégie de continuité des activités et de récupération d’urgence en prenant en charge la réplication des machines virtuelles et des serveurs physiques locaux. Vous répliquez des machines virtuelles et des serveurs à partir d’un centre de données local vers le cloud (Azure), ou vers un centre de données secondaire.

Lorsque des pannes se produisent sur le site principal, vous basculez vers le site secondaire pour préserver l’accès et la disponibilité des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal.

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery dans le portail Azure
Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle de gestion des services classique. Azure dispose également de deux portails : le [portail Azure Classic](https://manage.windowsazure.com/) et le [portail Azure](https://portal.azure.com).

* Site Recovery peut être déployé dans le portail Azure ClassiC et le portail Azure.
* Dans le portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle de gestion des services classique.
* Dans le portail Azure, vous pouvez prendre en charge les déploiements classiques et Ressource Manager.

Les informations contenues dans cet article s’appliquent aux déploiements du portail Azure Classic et du portail Azure. Nous avons indiqué les différences, le cas échéant.

## <a name="why-deploy-site-recovery"></a>Pour déployer Azure Site Recovery ?
Voici ce que Site Recovery peut faire pour votre entreprise :

* **Simplifier la récupération BCDR** : vous pouvez répliquer, basculer et récupérer plusieurs charges de travail à partir d’un emplacement unique dans le portail Azure. Site Recovery prend en charge la réplication et le basculement, sans intercepter les données d’application.
* **Fournir une réplication flexible** : vous pouvez répliquer des charges de travail s’exécutant sur des machines virtuelles Hyper-V et VMware prises en charge, et sur des serveurs physiques Windows/Linux.
* **Éliminer les besoins de centres de données secondaires** : vous pouvez répliquer des charges de travail vers Azure plutôt que vers un site secondaire. Ceci réduit le coût et la complexité de gestion d’un centre de données secondaire. Les données répliquées sont stockées dans Azure Storage, avec la résilience que cela implique. Lors du basculement, les machines virtuelles Azure sont créées avec les données répliquées.
* **Exécuter des tests de récupération simples** : vous pouvez facilement exécuter des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence, sans affecter les environnements de production.
* **Basculement et récupération** : vous pouvez exécuter des basculements planifiés pour les interruptions attendues, sans perte de données, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Vous pouvez effectuer une restauration automatique vers votre site principal dès qu’il est à nouveau disponible.
* **Multiple VM failover** :vous pouvez configurer des plans de récupération qui incluent des scripts et des runbooks Azure Automation. Les plans de récupération vous permettent de modéliser et de personnaliser le basculement et la récupération des applications multicouches réparties sur plusieurs machines virtuelles.
* **S’intégrer aux technologies existantes de continuité des activités et de récupération d’urgence** : Site Recovery s’intègre avec d’autres technologies BCDR. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, notamment la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.

## <a name="what-can-i-replicate"></a>Que puis-je répliquer ?
Voici une synthèse des éléments pouvant être répliqués par Site Recovery.

![Local vers local](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **RÉPLICATION VERS** |
| --- | --- |
| Machines virtuelles VMware locales |[Microsoft Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md) |
| Machines virtuelles Hyper-V locales, gérées dans des clouds VMM |[Microsoft Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secondaire](site-recovery-vmm-to-vmm.md) |
| Machines virtuelles Hyper-V locales, gérées dans des clouds VMM, avec stockage SAN |[Site secondaire](site-recovery-vmm-san.md) |
| Machines virtuelles Hyper-V locales, sans VMM |[Microsoft Azure](site-recovery-hyper-v-site-to-azure.md) |
| Serveurs Windows/Linux physiques locaux |[Microsoft Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Comment Site Recovery protège les charges de travail ?
Site Recovery assure une réplication compatible avec les applications, afin que les charges de travail et les applications continuent de s’exécuter de manière cohérente en cas de pannes.

* **Instantanés de cohérence de l’application** : réplication des machines utilisant des instantanés de cohérence de l’application pour les applications à niveau unique ou multiniveaux. En plus de capturer des données de disque, ces instantanés capturent toutes les données en mémoire, et toutes les transactions en cours.
* **Réplication quasi synchrone** : Site Recovery propose une fréquence de réplication de 30 secondes seulement pour Hyper-V, et une réplication continue pour VMware.
* **Plans de récupération flexibles** : vous pouvez créer et personnaliser des plans de récupération avec des scripts externes et des actions manuelles. L’intégration avec les Runbooks Azure Automation permet de récupérer tout une pile d’applications en un seul clic.
* **Intégration à SQL Server AlwaysOn**: vous pouvez gérer le basculement des groupes de disponibilité à l’aide des plans de récupération.
* **Bibliothèque d’automatisation**: une bibliothèque Azure Automation avancée qui fournit des scripts spécifiques d’application prêts pour la production, qui peuvent être téléchargés et intégrés avec Site Recovery.
* **Gestion réseau simple** : la gestion réseau avancée dans Site Recovery et Azure simplifie les conditions requises du réseau d’application, notamment la réservation d’adresses IP, la configuration d’équilibreurs de charge et l’intégration d’Azure Traffic Manager pour les commutations réseau efficaces.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus, consultez la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md)
* Pour en savoir plus sur l’architecture de Site Recovery, consultez l’article [Comment Azure Site Recovery fonctionne-t-il ?](site-recovery-components.md)




<!---HONumber=Nov16_HO2-->


