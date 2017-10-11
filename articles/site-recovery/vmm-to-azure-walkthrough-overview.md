---
title: "Réplication de machines virtuelles Hyper-V dans les clouds VMM vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Fournit une vue d’ensemble de la réplication de machines virtuelles Hyper-V dans les clouds VMM vers Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Répliquer vers Azure des machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de Site Recovery sur le Portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmm-to-azure.md)
> * [Portail Azure Classic](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell Classic](site-recovery-deploy-with-powershell.md)


Cet article fournit une vue d’ensemble des étapes nécessaires pour répliquer vers Azure des machines virtuelles Hyper-V locales gérées dans les clouds System Center Virtual Machine Manager (VMM) à l’aide du service [Azure Site Recovery](site-recovery-overview.md) sur le portail Azure.

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Étape 1 : Examen de l’architecture du scénario

Avant de commencer le déploiement, vérifiez l’architecture du scénario et prenez connaissance de tous les composants que vous devez déployer.

Aller à [Étape 1 : Examen de l’architecture](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Étape 2 : Examen des prérequis et des limitations

Vérifiez que vous comprenez les prérequis et les limitations du déploiement.

**Conditions préalables Azure** : vous avez besoin d’un compte Microsoft Azure, d’un réseau Azure et de comptes de stockage.
**Serveurs VMM et hôtes Hyper-V locaux** : assurez-vous que les serveurs VMM et les hôtes Hyper-V sont conformes et préparés pour le déploiement Site Recovery.
**Machines virtuelles répliquées** : vérifiez que les machines virtuelles à répliquer sont conformes aux conditions requises pour Azure.

Aller à [Étape 2 : vérifier les conditions préalables et les limitations](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Étape 3 : planifier la capacité

Si vous effectuez un déploiement complet, vous devez déterminer les ressources de réplication dont vous avez besoin. Pour ce faire, vous disposez de plusieurs outils. Si vous souhaitez réaliser une configuration rapide pour tester l’environnement, vous pouvez ignorer cette étape.

Aller à [Étape 3 : Planifier la capacité](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Étape 4 : Planifier la mise en réseau

Vous devez effectuer une planification réseau pour vous assurer que vous pouvez configurer le mappage réseau lorsque vous déployez le scénario, que les machines virtuelles Azure seront connectées aux réseaux virtuels Azure après le basculement et que des adresses IP appropriées leur sont attribuées.

Aller à [Étape 4 : Planifier la mise en réseau](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>Étape 5 : Préparer les ressources Azure

Configurez un compte Azure, des réseaux et du stockage. Vous pouvez le faire pendant le déploiement, mais nous vous recommandons de vous en occuper avant de commencer.

Aller à [Étape 5 : Préparer Azure](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Étape 6 : Préparer VMM et Hyper-V

Préparez les serveurs VMM et les hôtes Hyper-V locaux pour le déploiement Site Recovery.

Aller à [Étape 6 : Préparer les serveurs locaux](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Étape 7 : configurer un coffre

Configurez un coffre Recovery Services. Le coffre comporte les paramètres de configuration et orchestre la réplication.

[Étape 7 : Configurer un coffre](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Étape 8 : configurer les paramètres de source et de cible

Configurez les emplacements de réplication source et cible. Ajoutez le serveur VMM au coffre et téléchargez les fichiers d’installation pour les composants de Site Recovery. Exécutez le fournisseur Azure Site Recovery sur le serveur VMM. La configuration installe le fournisseur sur le serveur VMM et enregistre ce dernier dans le coffre. Vous installez l’agent Microsoft Recovery Services sur chaque hôte Hyper-V.

Aller à [Étape 8 : Configurer les paramètres de source et de cible](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>Étape 9 : Configurer le mappage réseau

Mappez des réseaux de machines virtuels VMM locaux à des réseaux virtuels Azure. Après le basculement, les machines virtuelles Azure sont créées dans le réseau Azure mappé au réseau de machines virtuelles local dans lequel se trouve la source Hyper-V.

Aller à [Étape 9 : Configurer le mappage réseau](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>Étape 10 : Configurer une stratégie de réplication

Spécifiez le mode de réplication des machines virtuelles locales vers Azure.

Aller à [Étape 10 : Configurer une stratégie de réplication](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>Étape 11 : Activer la réplication des machines virtuelles

Sélectionnez les machines virtuelles à répliquer. L’activation de la réplication d’une machine virtuelle déclenche la réplication initiale vers Azure, suivie de la réplication delta en cours.

Aller à [Étape 11 : activer la réplication](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>Étape 12 : exécuter un test de basculement

Exécutez un test de basculement afin de vérifier que tout fonctionne bien.

Aller à [Étape 12 : exécuter un test de basculement](vmm-to-azure-walkthrough-test-failover.md)


