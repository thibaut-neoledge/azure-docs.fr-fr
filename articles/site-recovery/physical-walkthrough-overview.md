---
title: "Répliquer des serveurs physiques locaux vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Fournit une vue d’ensemble des étapes pour la réplication des charges de travail exécutées sur des serveurs physiques locaux Windows/Linux vers Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Répliquer des serveurs physiques vers Azure avec Site Recovery

Cet article fournit une vue d’ensemble des étapes à suivre pour répliquer des serveurs physiques locaux Windows/Linux vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


## <a name="step-1-review-architecture-and-prerequisites"></a>Étape 1 : Vérifier l’architecture et les conditions préalables

Avant de commencer le déploiement, vérifiez l’architecture du scénario et prenez connaissance de tous les composants nécessaires pour procéder au déploiement.

Aller à [Étape 1 : Examen de l’architecture](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Étape 2 : Vérifier les conditions préalables

Assurez-vous que les conditions préalables sont remplies pour chaque composant du déploiement :

- **Conditions préalables Azure** : vous avez besoin d’un compte Microsoft Azure, de réseaux Azure et de comptes de stockage.
- **Composants Site Recovery locaux** : vous avez besoin d’une machine exécutant les composants Site Recovery locaux.
- **Machines répliquées** : les serveurs à répliquer doivent remplir les conditions requises pour l’environnement local et Azure.

Aller à [Étape 2 : Vérifier les conditions préalables et les limitations](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Étape 3 : Planifier la capacité

Si vous effectuez un déploiement complet, vous devez déterminer les ressources de réplication dont vous avez besoin. Si vous souhaitez réaliser une configuration rapide pour tester l’environnement, vous pouvez ignorer cette étape.

Aller à [Étape 3 : Planifier la capacité](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Étape 4 : Planifier la mise en réseau

Vous devez établir un plan du réseau pour vous assurer que les machines virtuelles Azure sont connectées aux réseaux après le basculement et qu’elles disposent des bonnes adresses IP.

Aller à [Étape 4 : Planifier la mise en réseau](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Étape 5 : Préparer les ressources Azure

Configurez les réseaux et le stockage Azure avant de commencer. 

Aller à [Étape 5 : Préparer Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>Étape 6 : Configurer un coffre

Vous devez configurer un coffre Recovery Services pour orchestrer et gérer la réplication. Lors de la configuration du coffre, vous spécifiez les éléments à répliquer et leur destination de réplication.

Aller à [Étape 6 : Configurer un coffre](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>Étape 7 : Configurer les paramètres de source et de cible

Configurez les paramètres des sites source et cible (Azure). Pour les paramètres source, vous devez exécuter une installation unifiée afin d’installer les composants Site Recovery locaux.

Aller à [Étape 7 : Configurer la source et la cible](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>Étape 8 : Configurer une stratégie de réplication

Vous configurez une stratégie pour spécifier les modalités de réplication des serveurs physiques.

Aller à [Étape 8 : Configurer une stratégie de réplication](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>Étape 9 : Installer le service Mobilité

Le service Mobilité doit être installé sur chaque serveur que vous souhaitez répliquer. Il existe plusieurs méthodes de configuration du service à l’aide d’une installation push ou pull.

Aller à [Étape 9 : Installer le service Mobilité](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>Étape 10 : Activer la réplication

Vous pouvez activer la réplication une fois que le service Mobilité est en cours d’exécution sur un serveur. La réplication initiale de la machine virtuelle se produira après l’activation.

Aller à [Étape 10 : Activer la réplication](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Étape 11 : Exécuter un test de basculement

Lorsque la réplication initiale est terminée et que la réplication delta est en cours d’exécution, vous pouvez exécuter un test de basculement pour vous assurer que tout fonctionne comme prévu.

Aller à [Étape 11 : Exécuter un test de basculement](physical-walkthrough-test-failover.md)


