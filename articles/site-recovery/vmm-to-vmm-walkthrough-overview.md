---
title: "Répliquer des machines virtuelles Hyper-V sur un site VMM secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Présente la réplication des machines virtuelles Hyper-V sur un site VMM secondaire via le portail Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Répliquer des machines virtuelles Hyper-V dans des clouds VMM vers un site VMM secondaire

> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-vmm.md)
> * [Portail classique](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Cet article fournit une vue d’ensemble des étapes nécessaires pour répliquer vers un emplacement VMM secondaire des machines virtuelles Hyper-V locales gérées dans les clouds System Center Virtual Machine Manager (VMM) à l’aide d’[Azure Site Recovery](site-recovery-overview.md) sur le portail Azure.

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Étape 1 : Examen de l’architecture du scénario

Avant de commencer le déploiement, vérifiez l’architecture du scénario et prenez connaissance de tous les composants que vous devez déployer.

Aller à [Étape 1 : Examen de l’architecture](vmm-to-vmm-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Étape 2 : Vérifier les conditions préalables et les limitations

Vérifiez que vous comprenez les conditions préalables et les limitations du déploiement.

**Conditions préalables Azure** : vous avez besoin d’un abonnement Microsoft Azure et d’un coffre Azure Recovery Services pour orchestrer et gérer la réplication.
**Serveurs VMM et hôtes Hyper-V locaux** : assurez-vous que les serveurs VMM et les hôtes Hyper-V sont conformes et préparés pour le déploiement Site Recovery.

Aller à [Étape 2 : Vérifier les conditions préalables et les limitations](vmm-to-vmm-walkthrough-prerequisites.md)

## <a name="step-3-plan-networking"></a>Étape 3 : Planifier la mise en réseau

Vous devez planifier votre mise en réseau pour vous assurer que vous pouvez configurer le mappage entre les réseaux de machines virtuelles VMM lorsque vous déployez le scénario.

Aller à [Étape 3 : Planifier la mise en réseau](vmm-to-vmm-walkthrough-network.md)


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Étape 4 : Préparer VMM et Hyper-V

Préparez les serveurs VMM et les hôtes Hyper-V pour le déploiement Site Recovery.

Aller à [Étape 4 : Préparer les serveurs locaux](vmm-to-vmm-walkthrough-vmm-hyper-v.md)

## <a name="step-5-set-up-a-vault"></a>Étape 5 : Configurer un coffre

Configurez un coffre Recovery Services. Le coffre comporte les paramètres de configuration et orchestre la réplication.

[Étape 5 : Configurer un coffre](vmm-to-vmm-walkthrough-create-vault.md)

## <a name="step-6-set-up-source-and-target-settings"></a>Étape 6 : Configurer les paramètres de la source et de la cible

Configurez les emplacements VMM de réplication source et cible. Ajoutez les serveurs VMM au coffre et téléchargez les fichiers d’installation pour les composants de Site Recovery. Exécutez le fournisseur Azure Site Recovery sur le serveur VMM. La configuration installe le fournisseur sur le serveur VMM et enregistre ce dernier dans le coffre. Vous installez l’agent Microsoft Recovery Services sur chaque hôte Hyper-V.

Aller à [Étape 6 : Configurer les paramètres de la source et de la cible](vmm-to-vmm-walkthrough-source-target.md)

## <a name="step-7-configure-network-mapping"></a>Étape 7 : Configuration du mappage réseau

Mappez les réseaux de machines virtuelles VMM dans les emplacements source et cible. Après le basculement, les machines virtuelles sont créées dans le réseau cible mappé au réseau de machines virtuelles source dans lequel se trouve la machine virtuelle Hyper-V source.

Aller à [Étape 7 : Configuration du mappage réseau](vmm-to-vmm-walkthrough-network-mapping.md)


## <a name="step-8-set-up-a-replication-policy"></a>Étape 8 : Configurer une stratégie de réplication

Spécifiez comment les machines virtuelles seront répliquées entre les emplacements VMM.

Aller à [Étape 8 : configurer une stratégie de réplication](vmm-to-vmm-walkthrough-replication.md)


## <a name="step-9-enable-replication-for-vms"></a>Étape 9 : Activer la réplication des machines virtuelles

Sélectionnez les machines virtuelles à répliquer. L’activation de la réplication d’une machine virtuelle déclenche la réplication initiale vers le site secondaire, suivie de la réplication delta en cours.

Aller à [Étape 9 : Activer la réplication](vmm-to-vmm-walkthrough-enable-replication.md)


## <a name="step-10-run-a-test-failover"></a>Étape 10 : Exécuter un test de basculement

Exécutez un test de basculement afin de vérifier que tout fonctionne bien.

Aller à [Étape 10 : Exécuter un test de basculement](vmm-to-vmm-walkthrough-test-failover.md)
