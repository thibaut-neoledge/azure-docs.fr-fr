---
title: "Préparer les ressources Azure pour répliquer des serveurs physiques locaux vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit les éléments à mettre en place dans Azure avant de commencer la réplication de serveurs locaux vers Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 4e320d9b-8bb8-46bb-ba21-77c5d16748ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: b7411fa6aba04ffd34f3f4bd03e706ca75afc9c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-5-prepare-azure-resources-for-physical-server-replication-to-azure"></a>Étape 5 : Préparer les ressources Azure pour la réplication de serveurs physiques vers Azure


Utilisez les instructions fournies dans cet article pour préparer les ressources Azure de manière à pouvoir répliquer des serveurs locaux vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Prenez connaissance des [conditions préalables](physical-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Configurer un compte Azure

- Procurez-vous un [compte Microsoft Azure](http://azure.microsoft.com/).
- Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Vérifiez les régions prises en charge pour Site Recovery, dans la section **Disponibilité géographique** de la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Lisez les informations relatives à la [tarification de Site Recovery](site-recovery-faq.md#pricing) et prenez connaissance de la [tarification appliquée](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

- Configurez un réseau Azure Les machines virtuelles Azure sont placées dans ce réseau une fois créées après le basculement.
- Site Recovery dans le portail Azure peut utiliser les réseaux configurés dans le [Gestionnaire des ressources](../resource-manager-deployment-model.md) ou en mode classique.
- Ce réseau doit se trouver dans la même région que le coffre Recovery Services.
- Prenez connaissance de la [tarification des réseaux virtuels](https://azure.microsoft.com/pricing/details/virtual-network/).
- Découvrez la [connectivité de la machine virtuelle Azure](physical-walkthrough-network.md) après le basculement.


## <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Site Recovery réplique les serveurs locaux vers le stockage Azure. Des machines virtuelles Azure sont créées à partir du stockage après le basculement.
- Configurer un [compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour les données répliquées.
- Site Recovery dans le portail Azure peut utiliser les comptes de stockage configurés dans le Gestionnaire des ressources ou en mode classique.
- Le compte de stockage peut être standard ou [premium](../storage/common/storage-premium-storage.md).
- Si vous configurez un compte premium, vous aurez également besoin d’un compte standard supplémentaire pour les données de journal.


## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 6 : Configurer un coffre](physical-walkthrough-create-vault.md)
