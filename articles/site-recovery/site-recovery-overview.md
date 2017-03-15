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
ms.date: 02/26/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e4bb13a73f6338d2d844a0561edc65063c685d59
ms.openlocfilehash: e554a0ba87efb0272e092a121ba96edc9d9eb011
ms.lasthandoff: 03/02/2017


---
# <a name="what-is-site-recovery"></a>Qu’est-ce que Site Recovery ?

Bienvenue dans le service Azure Site Recovery. Cet article offre une vue d’ensemble du service et des liens vers des informations complémentaires.

Les interruptions d’activité sont dues à des événements naturels et à des défaillances opérationnelles. Votre organisation a besoin d’une stratégie de continuité des activités et de récupération d’urgence qui assure la disponibilité des applications et la sécurité des données pendant les temps d’arrêt prévus et imprévus et qui puisse rétablir au plus vite des conditions de travail normales.

Azure Recovery Services contribue à votre stratégie de continuité des activités et de récupération d’urgence. [Azure Backup](https://docs.microsoft.com/en-us/azure/backup/) préserve la sécurité et la capacité de récupération de vos données. Site Recovery réplique, bascule et récupère les charges de travail, afin qu’elles restent disponibles en cas de panne.

## <a name="what-does-site-recovery-provide"></a>À quoi sert Site Recovery ?

- **Récupération d’urgence dans le cloud** : vous pouvez répliquer des charges de travail qui s’exécutent sur des machines virtuelles et des serveurs physiques vers Azure, plutôt que vers un site secondaire. Ceci réduit le coût et la complexité de gestion d’un centre de données secondaire.
- **Réplication flexible pour les environnements hybrides** : vous pouvez répliquer des charges de travail s’exécutant sur des machines virtuelles locales Hyper-V et VMware prises en charge, et sur des serveurs physiques Windows/Linux.
- **Migration** : vous pouvez utiliser Site Recovery pour migrer des instances AWS locales vers des machines virtuelles Azure, ou pour migrer des machines virtuelles Azure entre différentes régions Azure.
- **Continuité d’activité et récupération d’urgence simplifiées** : vous pouvez déployer la réplication à partir d’un emplacement unique sur le Portail Azure.  Vous pouvez exécuter des basculements simples et des restaurations d’une seule ou de plusieurs machines.
- **Résilience** : Site Recovery prend en charge la réplication et le basculement, sans intercepter les données d’application.
Les données répliquées sont stockées dans le Stockage Azure, avec la résilience que cela implique. Lors du basculement, les machines virtuelles Azure sont créées à partir des données répliquées.
- **Performances de réplication** : Site Recovery propose une fréquence de réplication de 30 secondes seulement pour Hyper-V, et une réplication continue pour VMware. Vous pouvez définir des seuils d’objectifs de point de récupération (RPO) pour contrôler la fréquence de création des points de récupération de données, et réduire l’objectif de temps de récupération (RTO) grâce au processus de récupération automatique de Site Recovery et à l’intégration avec [Azure Traffic Manager](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)
- **Cohérence au niveau de l’application** : réplication des machines utilisant des captures instantanées cohérentes au niveau de l’application. En plus de capturer des données de disque, ces instantanés capturent toutes les données en mémoire, et toutes les transactions en cours.
- **Tests sans perturbations** : vous pouvez facilement exécuter des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence, sans affecter les environnements de production.
- **Basculement et récupération flexibles** : vous pouvez exécuter des basculements planifiés pour les interruptions attendues, sans perte de données, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Vous pouvez effectuer en toute simplicité une restauration automatique vers votre site principal dès qu’il est à nouveau disponible.
- **Plans de récupération personnalisés** : les plans de récupération vous permettent de modéliser et de personnaliser le basculement et la récupération des applications multicouches réparties sur plusieurs machines virtuelles. Vous pouvez trier les groupes parmi les plans et ajouter des scripts et des actions manuelles. Les plans de récupération peuvent être intégrés à des runbooks Azure Automation.
- **Applications multiniveaux** : vous pouvez créer des plans de récupération pour le basculement séquencé et la récupération d’applications multiniveaux. Vous pouvez regrouper des machines de différents niveaux (par exemple les niveaux base de données, web, application) au sein d’un plan de récupération, et personnaliser le basculement et le démarrage de chaque groupe.
* **Intégration aux technologies existantes de continuité d’activité et de récupération d’urgence** : Site Recovery s’intègre avec d’autres technologies de continuité d’activité et de récupération d’urgence. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, notamment la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.
* **Intégration à la bibliothèque d’automatisation** : une bibliothèque Azure Automation avancée qui fournit des scripts propres à chaque application et prêts pour la production, qui peuvent être téléchargés et intégrés avec Site Recovery.
* **Gestion réseau simple** : la gestion réseau avancée dans Site Recovery et Azure simplifie les conditions requises du réseau d’application, notamment la réservation d’adresses IP, la configuration d’équilibreurs de charge et l’intégration d’Azure Traffic Manager pour les commutations réseau efficaces.

## <a name="which-azure-portal"></a>Quel portail Azure utiliser ?

* Site Recovery peut être déployé dans le nouveau [Portail Azure](https://portal.azure.com) et sur le [Portail Azure Classic](https://manage.windowsazure.com/).
* Dans le portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle de gestion des services classique.
* Sur le Portail Azure, vous pouvez prendre en charge le modèle Classic, ou le nouveau [modèle de déploiement Ressource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
- Le Portail Classic doit uniquement être utilisé pour maintenir les déploiements Site Recovery existants. Il est impossible de créer de nouveaux coffres à l’aide du Portail Classic.

## <a name="whats-supported"></a>Prise en charge

**Pris en charge** | **Détails**
--- | ---
**Quelles sont les régions prises en charge pour Site Recovery ?** | [Régions prises en charge](https://azure.microsoft.com/en-us/regions/services/) |
**Que puis-je répliquer ?** | Machines virtuelles VMware locales, machines virtuelles Hyper-V et serveurs physiques Windows et Linux.
**De quels systèmes d’exploitation ont besoin les machines répliquées ?** | [Systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) pour les machines virtuelles VMware<br/><br/> Pour les machines virtuelles Hyper-V, tous les [SE invités](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) pris en charge par Azure et Hyper-V sont pris en charge.<br/><br/> [Systèmes d’exploitation](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) pour les serveurs physiques
**Vers où puis-je effectuer la réplication ?** | Vers le stockage Azure ou à un centre de données secondaire<br/><br/> Pour Hyper-V, seules les machines virtuelles sur des hôtes Hyper-V gérés dans des clouds System Center VMM peuvent être répliquées vers un centre de données secondaire.
**De quels serveurs/hôtes VMware ai-je besoin ?** | Les machines virtuelles VMware que vous souhaitez répliquer peuvent être gérées par les [hôtes vSphere / serveurs vCenter pris en charge](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
**Quelles charges de travail puis-je répliquer ?** | Vous pouvez répliquer toutes les charges de travail qui s’exécutent sur une machine de réplication prise en charge. En outre, l’équipe de Site Recovery a effectué des tests pour un [certain nombre d’applications](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [prise en charge de la charge de travail](site-recovery-workload.md)
* En savoir plus sur [l’architecture et les composants de Site Recovery](site-recovery-components.md).

