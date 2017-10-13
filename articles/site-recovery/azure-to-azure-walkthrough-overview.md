---
title: "Répliquer des machines virtuelles Azure entre des régions Azure | Microsoft Docs"
description: "Résume les étapes nécessaires à la réplication de machines virtuelles Azure entre des régions Azure à l’aide du service Site Recovery dans le portail Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Répliquer des machines virtuelles Azure entre des régions avec Azure Site Recovery

>Cet article fournit une vue d’ensemble des étapes requises pour répliquer des machines virtuelles Azure d’une région Azure vers des machines virtuelles Azure d’une autre région. 

>[!NOTE]
>
> La réplication de machines virtuelles Azure est actuellement disponible en préversion.

Publiez des commentaires et des questions au bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Étape 1 : examen de l’architecture

Avant de commencer le déploiement, vérifiez l’architecture du scénario et les composants que vous devez déployer.

Aller à [Étape 1 : Examen de l’architecture](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Étape 2 : Vérifier les conditions préalables

Vérifiez que les conditions préalables Azure sont remplies : abonnement, réseaux virtuels, comptes de stockage et conditions requises au niveau des machines virtuelles.

Aller à [Étape 2 : vérifier les conditions préalables et les limitations](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>Étape 3 : planifier la mise en réseau

Vérifiez que la connectivité sortante est configurée sur les machines virtuelles Azure à répliquer, et que les connexions à partir du site local sont configurées.

Aller à [Étape 4 : Planifier la mise en réseau](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>Étape 4 : créer un coffre 

Vous devez configurer un coffre Recovery Services pour orchestrer et gérer la réplication, et spécifier la région source.

Aller à l’[Étape 4 : créer un coffre](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>Étape 5 : activer la réplication


Pour activer la réplication, configurez les paramètres de l’emplacement cible, définissez une stratégie de réplication et sélectionnez les machines virtuelles Azure que vous souhaitez répliquer. La réplication initiale de la machine virtuelle se produira après l’activation.

Aller à [Étape 5 : activer la réplication](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>Étape 6 : exécuter un test de basculement

Lorsque la réplication initiale est terminée et la réplication delta est en cours d’exécution, vous pouvez exécuter un test de basculement pour vous assurer que tout fonctionne comme prévu.

Aller à [Étape 6 : exécuter un test de basculement](azure-to-azure-walkthrough-test-failover.md)


