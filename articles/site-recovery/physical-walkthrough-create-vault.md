---
title: "Configurer un coffre pour la réplication de serveurs physiques vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer un coffre dans le cadre de la réplication de serveurs physiques vers Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017

---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Étape 6 : Configurer un coffre pour la réplication de serveurs physiques vers Azure


Cet article décrit comment configurer un coffre. Vous créez le coffre et spécifiez ce que vous souhaitez répliquer à partir de l’emplacement local, vers Azure, à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans la boîte de dialogue **Objectif de protection**, sélectionnez **Vers Azure** > **Non virtualisé / Autre**.


## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 7 : Configurer la source et la cible](physical-walkthrough-source-target.md)

