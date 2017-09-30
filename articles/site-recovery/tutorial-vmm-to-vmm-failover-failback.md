---
title: "Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur un centre de données secondaire avec Site Recovery | Microsoft Docs"
description: "Découvrez comment basculer des machines virtuelles Hyper-V vers votre site local secondaire et restaurer sur le site principal avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 44a662fa-2e7a-4996-86df-fdd6d6f5dedf
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 8f139070de99c4249207d048d445e86dd41e9060
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Basculer et restaurer automatiquement des machines virtuelles Hyper-V répliquées sur votre site local secondaire

Le service [Azure Site Recovery](site-recovery-overview.md) gère et orchestre la réplication, le basculement et la restauration automatique des machines locales et des machines virtuelles Azure.

Ce didacticiel explique comment basculer une machine virtuelle Hyper-V managée dans un cloud System Center Virtual Machine Manager (VMM) vers un site VMM secondaire. Après avoir procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Basculer une machine virtuelle Hyper-V d’un cloud VMM principal vers un cloud VMM secondaire
> * Reprotéger et effectuer une restauration automatique du site secondaire sur le site principal
> * Effectuer de nouveau une réplication du site principal sur le site secondaire (facultatif)

## <a name="overview"></a>Vue d'ensemble

Le basculement et la restauration automatique comportent trois étapes :

1. **Basculement vers le site secondaire** : les machines basculent du site principal vers le site secondaire.
2. **Restauration automatique à partir du site secondaire** : les machines virtuelles sont répliquées depuis le site secondaire sur le site principal, et un basculement planifié est exécuté pour la restauration automatique.
3. Après le basculement planifié, vous pouvez, si vous le souhaitez, effectuer de nouveau une réplication du site primaire sur le site secondaire.


## <a name="fail-over-to-a-secondary-site"></a>Basculer vers un site secondaire

### <a name="failover-prerequisites"></a>Conditions préalables de basculement

Veillez à effectuer un [test de récupération d’urgence](tutorial-dr-drill-secondary.md) pour vérifier que tout fonctionne comme prévu.


### <a name="run-a-failover-from-primary-to-secondary"></a>Effectuer un basculement du site principal vers le site secondaire

Vous pouvez effectuer un basculement régulier ou planifié pour des machines virtuelles Hyper-V.

- Utilisez un basculement régulier pour les interruptions inattendues. Quand vous effectuez ce basculement, Site Recovery crée une machine virtuelle sur le site secondaire et la démarre. Vous effectuez le basculement pour un point de récupération spécifique. Des pertes de données peuvent se produire en fonction du point de récupération que vous utilisez.
- Un basculement planifié peut être utilisé pour la maintenance ou durant une interruption prévue. Cette option permet de ne perdre aucune donnée. Quand un basculement planifié est déclenché, les machines virtuelles sources sont arrêtées. Les données non synchronisées sont synchronisées, et le basculement est déclenché. 
- 
Cette procédure explique comment effectuer un basculement régulier.


1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.
2. Dans **Basculer**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    - **Dernier** (par défaut) : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle de réplica créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
    - **Dernier point traité** : cette option bascule la machine virtuelle vers le dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
    - **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery. 
3. La clé de chiffrement ne s’applique pas à ce scénario.
4. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles sources avant de déclencher le basculement. Site Recovery tente également de synchroniser les données locales qui n’ont pas encore été envoyées au site secondaire avant de déclencher le basculement. Notez que le basculement continue même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.
5. La machine virtuelle devrait à présent apparaître dans le cloud VMM secondaire.
6. Après avoir vérifié la machine virtuelle, **validez** le basculement. Cela supprime tous les points de récupération disponibles.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée. Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.  


## <a name="reprotect-and-fail-back-from-secondary-to-primary"></a>Reprotéger et effectuer une restauration automatique du site secondaire sur le site principal

### <a name="prerequisites-for-failback"></a>Prérequis pour la restauration automatique

Pour effectuer la restauration automatique, vérifiez que les serveurs VMM principal et secondaire sont connectés à Site Recovery.


### <a name="reprotect-and-fail-back"></a>Reprotéger et effectuer une restauration automatique

Démarrez la réplication du site secondaire sur le site principal et effectuez la restauration automatique sur le site principal. Quand les machines virtuelles fonctionnent de nouveau sur le site principal, vous pouvez les répliquer de nouveau sur le site secondaire.  

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle et activez l’option **Réplication inverse**. La machine virtuelle démarre la réplication sur le site principal.
2. Cliquez sur la machine virtuelle > **Basculement planifié**.
3. Dans **Confirmer le basculement planifié**, vérifiez le sens du basculement (à partir du cloud VMM secondaire) et sélectionnez les emplacements source et cible. 
4. Dans **Synchronisation des données**, spécifiez comment vous voulez synchroniser :
    - **Synchroniser les données avant le basculement (synchroniser seulement les modifications d’ordre différentiel)** : cette option minimise le temps d’indisponibilité des machines virtuelles, car elle synchronise sans arrêter la machine virtuelle. Voici ce qu’elle fait :
        - Elle capture un instantané de la machine virtuelle de réplica et le copie sur l’hôte Hyper-V principal. La machine virtuelle de réplica continue à fonctionner.
        - Elle arrête la machine virtuelle de réplica : aucune nouvelle modification ne s’y produit donc. L’ensemble final des modifications d’ordre différentiel est transféré vers le site principal, et la machine virtuelle locale sur le site principal est démarrée.
    - **Synchroniser les données uniquement lors du basculement (téléchargement complet)** : utilisez cette option si vous utilisez le site secondaire depuis longtemps. Cette option est plus rapide, car nous attendons plusieurs modifications du disque et nous ne passons pas de temps dans les calculs des sommes de contrôle. Cette option effectue un téléchargement du disque. Elle est également pratique quand la machine virtuelle principale a été supprimée.
5. La clé de chiffrement ne s’applique pas à ce scénario.
6. Lancez le basculement. Vous pouvez suivre la progression du basculement sur l’onglet **Tâches** .
7. Si vous avez sélectionné l’option de synchronisation des données avant le basculement, quand la synchronisation initiale des données est terminée et que vous êtes prêt à arrêter les machines virtuelles de réplica sur le site secondaire, cliquez sur **Tâches** > Nom de la tâche de basculement planifié > **Terminer le basculement**. Cela arrête la machine virtuelle secondaire, transfère les dernières modifications vers le site principal et démarre la machine virtuelle principale.
8. Dans le cloud VMM principal, vérifiez que la machine virtuelle est disponible.
9. L’état de la machine virtuelle principale est maintenant Validation en attente. Cliquez sur **Valider** pour valider le basculement.
10. Si vous souhaitez répliquer de nouveau la machine virtuelle principale sur le site secondaire, activez l’option **Réplication inverse**.


> [!NOTE]
> La réplication inverse réplique seulement les modifications qui se sont produites depuis que la machine virtuelle de réplica a été arrêtée, et seules les modifications d’ordre différentiel sont envoyées.


