---
title: "Qu’est-ce que la solution Azure Site Recovery ? | Microsoft Docs"
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
ms.date: 06/25/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: a442d398cf5c54b00dc92ebcbe62597fb3ece30c
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


---
<a id="what-is-site-recovery" class="xliff"></a>

# Qu’est-ce que Site Recovery ?

Bienvenue dans le service Azure Site Recovery. Cet article propose une vue d’ensemble du service.

<a id="business-continuity-and-disaster-recovery-bdcr-with-azure-recovery-services" class="xliff"></a>

## Continuité des activités et récupération d’urgence (BDCR) avec Azure Recovery Services

En tant qu’organisation, vous devez déterminer comment vous allez protéger vos données ainsi que les applications/charges de travail en cours d’exécution lors d’interruptions planifiées ou non.

Azure Recovery Services contribue à votre stratégie BCDR :

- **Service Site Recovery** : ce service aide à assurer une continuité des activités en maintenant les applications en cours d’exécution sur des machines virtuelles et des serveurs physiques disponibles, en cas de panne du site. Site Recovery réplique les charges de travail en cours d’exécution sur des machines virtuelles et des serveurs physiques, afin que ces charges restent disponibles à un second emplacement si jamais l’emplacement principal n’est pas disponible. Ce service récupère les charges de travail sur le site principal lorsqu’il fonctionne à nouveau.
- **Service de sauvegarde** : le service de [sauvegarde Azure](https://docs.microsoft.com/azure/backup/) assure également la sécurité de vos données. En sauvegardant ces données sur Azure, elles sont récupérables.

Site Recovery peut gérer la réplication pour :

- Les machines virtuelles Azure qui répliquent des données entre des régions Azure.
- Les machines virtuelles et les serveurs physiques locaux qui répliquent des données sur Azure ou sur un site secondaire.


<a id="what-does-site-recovery-provide" class="xliff"></a>

## À quoi sert Site Recovery ?

**Fonctionnalité** | **Détails**
--- | ---
**Déployer une solution BCDR simple** | Grâce à Site Recovery, vous pouvez configurer et gérer la réplication, le basculement et la restauration automatique à partir d’un seul emplacement dans le portail Azure.
**Répliquer des machines virtuelles Azure** | Vous pouvez configurer votre stratégie BCDR afin que les machines virtuelles Azure soient répliquées entre les régions Azure.
**Répliquer hors site des machines virtuelles locales** | Vous pouvez répliquer des machines virtuelles et des serveurs physiques locaux sur Azure ou à un emplacement local secondaire. La réplication sur Azure réduit le coût et la complexité qu’implique la maintenance d’un centre de données secondaire.
**Répliquer n’importe quelle charge de travail** | Répliquez n’importe quelle charge de travail en cours d’exécution sur des machines virtuelles Azure, des machines virtuelles Hyper-V locales, des machines virtuelles VMware et des serveurs physiques Windows/Linux pris en charge.
**Maintenir les données durables et sécurisées** | Site Recovery orchestre la réplication sans intercepter les données d’une application. Les données répliquées sont stockées dans le Stockage Azure, avec la résilience que cela implique. Lors du basculement, les machines virtuelles Azure sont créées à partir des données répliquées.
**Respecter les RTO et les RPO** | Maintenez les objectifs de délai de récupération (RTO) et les objectifs de point de récupération (RPO) au sein des limites fixées par l’organisation. Site Recovery fournit une réplication continue pour les machines virtuelles Azure et VMware. Les machines virtuelles Hyper-V bénéficient d’une fréquence de réplication de 30 secondes seulement. Vous pouvez réduire davantage les RTO en les ajoutant à l’intégration [Azure Traffic Manager](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Maintenir la cohérence des applications en cas de basculement** | Vous pouvez configurer des points de récupération avec des captures instantanées cohérentes au niveau des applications. Ces captures récupèrent les données des disques, l’ensemble des données en mémoire et toutes les transactions en cours de traitement.
**Réaliser des tests sans interruption** | Vous pouvez facilement réaliser des tests de basculement pour prendre en charge la marche à suivre en cas de récupération d’urgence et ce, sans affecter une réplication en cours.
**Exécuter des basculements flexibles** | Vous pouvez exécuter des basculements planifiés pour les interruptions attendues, sans perte de données, ou des basculements non planifiés avec une perte de données minimale (en fonction de la fréquence de réplication) pour les incidents inattendus. Vous pouvez effectuer en toute simplicité une restauration automatique vers votre site principal dès qu’il est à nouveau disponible.
**Créer des plans de récupération** | Vous pouvez personnaliser et séquencer le basculement et la récupération d’applications multiniveau sur plusieurs machines virtuelles avec des plans de récupération. Vous regroupez des machines dans des plans et ajoutez des scripts ainsi que des actions manuelles. Les plans de récupération peuvent être intégrés à des runbooks Azure Automation.
**Intégration aux technologies BCDR existantes** | Site Recovery s’intègre à d’autres technologies BCDR. Par exemple, vous pouvez utiliser Site Recovery pour protéger le serveur principal SQL Server de charges de travail d’entreprise, notamment la prise en charge native de SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.
**Intégration à la bibliothèque d’automatisation** | La bibliothèque Azure Automation diversifiée fournit des scripts spécifiques à l’application prêts pour la production, qui peuvent être téléchargés et intégrés au service Site Recovery.
**Gérer les paramètres réseau** | Site Recovery s’intègre à Azure pour permettre une gestion simple du réseau d’application, y compris la réservation d’adresses IP, la configuration des équilibrages de charges et l’intégration d’Azure Traffic Manager pour des commutations réseau efficaces.


<a id="what-can-i-replicate" class="xliff"></a>

## Que puis-je répliquer ?

**Pris en charge** | **Détails**
--- | ---
**Que puis-je répliquer ?** | Machines virtuelles Azure entre régions Azure (préversion)<br/><br/>  Machines virtuelles VMware locales, machines virtuelles Hyper-V et serveurs physiques (Windows et Linux) sur Azure<br/> Machines virtuelles VMware locales, machines virtuelles Hyper-V et serveurs physiques sur un site secondaire. Concernant les machines virtuelles Hyper-V, la réplication sur un site secondaire n’est prise en charge qui si les hôtes Hyper-V sont gérés par System Center VMM.
**Quelles sont les régions prises en charge pour Site Recovery ?** | [Régions prises en charge](https://azure.microsoft.com/regions/services/) |
**De quels systèmes d’exploitation ont besoin les machines répliquées ?** | [Exigences des machines virtuelles Azure](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)<br></br>[Exigences des machines virtuelles VMware](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Pour les machines virtuelles Hyper-V, tous les [SE invités](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) pris en charge par Azure et Hyper-V sont pris en charge.<br/><br/> [Exigences des serveurs physiques](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**De quels serveurs/hôtes VMware ai-je besoin ?** | Les machines virtuelles VMware peuvent se trouver sur des [hôtes vSphere/serveurs vCenter](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Quelles charges de travail puis-je répliquer ?** | Vous pouvez répliquer toutes les charges de travail qui s’exécutent sur une machine de réplication prise en charge. En outre, l’équipe de Site Recovery a effectué des tests pour un [certain nombre d’applications](site-recovery-workload.md#workload-summary).


<a id="azure-portal-considerations" class="xliff"></a>

## Considérations relatives au portail Azure

* Site Recovery peut être déployé dans le [portail Azure](https://portal.azure.com).
* Dans le portail Azure Classic, vous pouvez gérer Site Recovery avec le modèle de gestion des services classique.
- Le Portail Classic doit uniquement être utilisé pour maintenir les déploiements Site Recovery existants. Il est impossible de créer de nouveaux coffres à l’aide du Portail Classic.

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
* En savoir plus sur la [prise en charge de la charge de travail](site-recovery-workload.md)
* Prise en main de la [ réplication de machines virtuelles Azure entre des régions](site-recovery-azure-to-azure.md), de la [réplication VMware sur Azure](vmware-walkthrough-overview.md), ou de la [réplication Hyper-V sur Azure](hyper-v-site-walkthrough-overview.md).

