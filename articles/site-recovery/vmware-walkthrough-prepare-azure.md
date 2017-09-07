---
title: "Préparer les ressources Azure pour répliquer des machines virtuelles VMware sur Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit les éléments à mettre en place dans Azure avant de commencer la réplication de machines virtuelles VMware sur Azure à l’aide d’Azure Site Recovery."
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
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 40abff72278c9f8d9f701023fd473fe52c17b421
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="step-5-prepare-azure-resources-for-vmware-replication-to-azure"></a>Étape 5 : préparer les ressources Azure pour la réplication VMware sur Azure


Utilisez les instructions fournies dans cet article pour préparer les ressources Azure de manière à pouvoir répliquer des machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Prenez connaissance des [conditions préalables](vmware-walkthrough-prerequisites.md).

## <a name="set-up-an-azure-account"></a>Configurer un compte Azure

- Procurez-vous un [compte Microsoft Azure](http://azure.microsoft.com/).
- Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
- Vérifiez les régions prises en charge pour Site Recovery dans la section Disponibilité géographique de la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
- Lisez les informations relatives à la [tarification de Site Recovery](site-recovery-faq.md#pricing) et prenez connaissance de la [tarification appliquée](https://azure.microsoft.com/pricing/details/site-recovery/).



## <a name="set-up-an-azure-network"></a>Configurer un réseau Azure

- Configurez un réseau Azure Les machines virtuelles Azure sont placées dans ce réseau une fois créées après le basculement.
- Site Recovery dans le portail Azure peut utiliser les réseaux configurés dans [Resource Manager](../resource-manager-deployment-model.md) ou en mode classique.
- Ce réseau doit se trouver dans la même région que le coffre Recovery Services.
- Prenez connaissance de la [tarification des réseaux virtuels](https://azure.microsoft.com/pricing/details/virtual-network/).
- Découvrez la [connectivité de la machine virtuelle Azure](site-recovery-network-design.md) après le basculement.


## <a name="set-up-an-azure-storage-account"></a>Configurer un compte de stockage Azure

- Site Recovery réplique les machines virtuelles locales sur le stockage Azure. Des machines virtuelles Azure sont créées à partir du stockage après le basculement.
- Configurer un [compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour les données répliquées.
- Site Recovery dans le portail Azure peut utiliser les comptes de stockage configurés dans le Gestionnaire des ressources ou en mode classique.
- Le compte de stockage peut être standard ou [premium](../storage/common/storage-premium-storage.md).
- Si vous configurez un compte premium, vous aurez également besoin d’un compte standard supplémentaire pour les données de journal.


## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 6 : préparer les ressources VMware](vmware-walkthrough-prepare-vmware.md)

