---
title: "Répliquer des machines virtuelles VMware sur Azure à l’aide de Azure Site Recovery | Microsoft Docs"
description: "Fournit une vue d’ensemble des étapes à suivre pour répliquer des charges de travail exécutées sur des machines virtuelles VMware sur Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>Répliquer des machines virtuelles VMware sur Azure à l’aide de Site Recovery

Cet article fournit une vue d’ensemble des étapes à suivre pour répliquer des machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


![Processus de déploiement](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**Figure 1 : Résumé du processus de déploiement**

## <a name="step-1-review-architecture-and-prerequisites"></a>Étape 1 : vérifier l’architecture et les conditions préalables.

Avant de commencer le déploiement, vérifiez l’architecture du scénario et assurez-vous de bien comprendre le fonctionnement de tous les composants que vous devez déployer

Aller à [Étape 1 : examen de l’architecture](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Étape 2 : vérifier les conditions préalables

Assurez-vous d’avoir réuni les conditions préalables pour chaque composant du déploiement :

- **Conditions préalables Azure** : vous avez besoin d’un compte Microsoft Azure, d’un réseau Azure et de comptes de stockage.
- **Composants Site Recovery locaux** : vous avez besoin d’une machine exécutant les composants Site Recovery locaux.
- **Conditions préalables de VMware locales** : vous devez configurer les comptes afin que Site Recovery puisse accéder aux serveurs VMware et aux machines virtuelles.
- **Machines virtuelles répliquées** : Les machines virtuelles que vous souhaitez répliquer doivent se conformer aux exigences d’Azure, et les composants du service Mobilité doivent être installés.

Aller à [Étape 2 : vérifier les conditions préalables et les limitations](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Étape 3 : planifier la capacité

Si vous effectuez un déploiement complet, vous devez déterminer les ressources de réplication dont vous avez besoin. Vous disposez de plusieurs outils pour vous aider à cela. Allez à l’étape 2. Si vous souhaitez réaliser une configuration rapide pour tester l’environnement, vous pouvez passer cette étape.

Aller à [Étape 3 : planifier la capacité](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Étape 4 : planifier la mise en réseau

Vous devez établir un plan du réseau pour vous assurer que les machines virtuelles Azure sont connectées au réseau après le basculement, et qu’elles disposent des bonnes adresses IP.

Aller à [Étape 4 : planifier la mise en réseau](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Étape 5 : préparer les ressources Azure

Configurez les réseaux Azure et le stockage avant de commencer. Vous pouvez configurer les réseaux pendant le déploiement mais nous vous recommandons de vous en occuper avant de commencer.

Aller à [Étape 5 : Préparer Azure](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>Étape 6 : préparer VMware

Vous devez configurer des comptes que Site Recovery utilisera pour :

- Accéder aux serveurs de virtualisation VMware pour détecter automatiquement les machines virtuelles.
- Accéder aux machines virtuelles pour installer le service Mobilité. Vous devez installer l’agent du service Mobilité sur chaque machine virtuelle que vous souhaitez répliquer pour activer la réplication.

Aller à [Étape 6 : préparer VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>Étape 7 : configurer un coffre

Vous devez configurer un coffre Recovery Services pour orchestrer et gérer la réplication. Lorsque vous configurez le coffre, vous spécifiez ce que vous voulez répliquer, et où vous souhaitez le répliquer.

Aller à [Étape 7 : Configurer un coffre](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Étape 8 : configurer les paramètres source et cible

Configurez la source et la cible utilisées pour la réplication. Pour configurer les paramètres source, vous devez exécuter une installation unifiée afin d’installer les composants Site Recovery locaux.

Aller à [Étape 8 : configurer la source et la cible](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Étape 9 : configurer une stratégie de réplication

Vous devez configurer une stratégie afin de spécifier les paramètres de réplication des machines virtuelles VMware dans le coffre.

Aller à [Étape 9 : configurer une stratégie de réplication](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>Étape 10 : installer le service Mobilité

Le service Mobility doit être installé sur chaque machine que vous souhaitez répliquer. Il existe plusieurs méthodes de configuration du service à l’aide d’une installation push ou pull.

Aller à [Étape 10 : installer le service Mobilité](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>Étape 11 : activer la réplication

Vous pouvez activer la réplication une fois que le service Mobilité est en cours d’exécution sur une machine virtuelle. La réplication initiale de la machine virtuelle se produira après l’activation.

Aller à [Étape 11 : activer la réplication](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>Étape 12 : exécuter un test de basculement

Lorsque la réplication initiale est terminée et la réplication delta est en cours d’exécution, vous pouvez exécuter un test de basculement pour vous assurer que tout fonctionne comme prévu.

Aller à [Étape 12 : exécuter un test de basculement](vmware-walkthrough-test-failover.md)

