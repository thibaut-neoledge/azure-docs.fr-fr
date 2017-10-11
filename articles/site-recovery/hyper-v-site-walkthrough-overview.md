---
title: "Répliquer des machines virtuelles Hyper-V vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Explique comment orchestrer la réplication, le basculement et la récupération des machines virtuelles Hyper-V locales dans Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Répliquer des machines virtuelles Hyper-V (sans VMM) vers Azure 

> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Portail Azure Classic](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Cet article offre une vue d’ensemble des étapes à suivre pour répliquer des machines virtuelles Hyper-V locales vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure. Dans ce déploiement, les machines virtuelles Hyper-V ne sont pas gérées par System Center Virtual Machine Manager (VMM).


Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-architecture-and-prerequisites"></a>Étape 1 : vérifier l’architecture et les conditions préalables

Avant de commencer le déploiement, vérifiez l’architecture du scénario et prenez connaissance de tous les composants que vous devez déployer.

Accédez à [Étape 1 : examen de l’architecture](hyper-v-site-walkthrough-architecture.md).


## <a name="step-2-review-prerequisites"></a>Étape 2 : Vérifier les conditions préalables

Assurez-vous que les conditions préalables sont remplies pour chaque composant du déploiement :

- **Conditions préalables Azure** : vous avez besoin d’un compte Microsoft Azure, de réseaux Azure et de comptes de stockage.
- **Conditions préalables pour les machines virtuelles Hyper-V locales** : assurez-vous que les hôtes Hyper-V sont préparés pour le déploiement Site Recovery.
- **Machines virtuelles répliquées** : les machines virtuelles à répliquer doivent se conformer aux conditions requises pour Azure.

Accédez à [Étape 2 : vérifier les conditions préalables et les limitations](hyper-v-site-walkthrough-prerequisites.md).

## <a name="step-3-plan-capacity"></a>Étape 3 : planifier la capacité

Si vous effectuez un déploiement complet, vous devez déterminer les ressources de réplication dont vous avez besoin. Pour ce faire, vous disposez de plusieurs outils. Accédez à l’étape 2. Si vous souhaitez réaliser une configuration rapide pour tester l’environnement, vous pouvez passer cette étape.

Accédez à [Étape 3 : planifier la capacité](hyper-v-site-walkthrough-capacity.md).

## <a name="step-4-plan-networking"></a>Étape 4 : Planifier la mise en réseau

Vous devez établir un plan du réseau pour vous assurer que les machines virtuelles Azure sont connectées aux réseaux après le basculement et qu’elles disposent des bonnes adresses IP.

Aller à [Étape 4 : Planifier la mise en réseau](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Étape 5 : Préparer les ressources Azure

Configurez les réseaux et le stockage Azure avant de commencer. Vous pouvez le faire pendant le déploiement, mais nous vous recommandons de vous en occuper avant de commencer.

Accédez à [Étape 5 : préparer Azure](hyper-v-site-walkthrough-prepare-azure.md).


## <a name="step-6-prepare-hyper-v"></a>Étape 6 : préparer Hyper-V

Assurez-vous que les serveurs Hyper-V remplissent les exigences liées au déploiement Site Recovery.

Accédez à [Étape 6 : préparer Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md).

## <a name="step-7-set-up-a-vault"></a>Étape 7 : configurer un coffre

Vous devez configurer un coffre Recovery Services pour orchestrer et gérer la réplication. Lors de la configuration du coffre, vous spécifiez les éléments à répliquer et leur destination de réplication.

Accédez à [Étape 7 : créer un coffre](hyper-v-site-walkthrough-create-vault.md).

## <a name="step-8-configure-source-and-target-settings"></a>Étape 8 : configurer les paramètres de source et de cible

Configurez la source et la cible utilisées pour la réplication. La configuration des paramètres de source comprend l’ajout d’hôtes Hyper-V à un site Hyper-V, l’installation du fournisseur Site Recovery et de l’agent Recovery Services sur chaque hôte Hyper-V, et l’inscription du site dans le coffre Recovery Services.

Accédez à [Étape 8 : configurer la source et la cible](hyper-v-site-walkthrough-source-target.md).

## <a name="step-9-set-up-a-replication-policy"></a>Étape 9 : configurer une stratégie de réplication

Vous devez configurer une stratégie afin de spécifier les paramètres de réplication des machines virtuelles Hyper-V dans le coffre.

Accédez à [Étape 9 : configurer une stratégie de réplication](hyper-v-site-walkthrough-replication.md).


## <a name="step-10-enable-replication"></a>Étape 10 : activer la réplication

Une fois que vous avez une stratégie de réplication en place, la réplication initiale de la machine virtuelle se produira après l’activation.

Accédez à [Étape 10 : activer la réplication](hyper-v-site-walkthrough-enable-replication.md).

## <a name="step-11-run-a-test-failover"></a>Étape 11 : exécuter un test de basculement

Lorsque la réplication initiale est terminée et la réplication delta est en cours d’exécution, vous pouvez exécuter un test de basculement pour vous assurer que tout fonctionne comme prévu.

Accédez à [Étape 11 : exécuter un test de basculement](hyper-v-site-walkthrough-test-failover.md).
