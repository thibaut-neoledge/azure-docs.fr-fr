---
title: "Configurer un coffre pour la réplication Hyper-V (avec System Center VMM) vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour configurer un coffre pour la réplication Hyper-V (avec VMM) vers Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Étape 7 : configurer un coffre pour la réplication Hyper-V

Cet article explique comment configurer un coffre, et spécifier ce que vous souhaitez répliquer à partir de l’emplacement local, sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Sélectionner un objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Préparer l’infrastructure** > **Objectif de protection**.
3. Dans **Objectif de protection**, sélectionnez **To Azure (Vers Azure)** > **, puis Oui, avec Hyper-V**. Sélectionnez **Oui** pour confirmer que vous utilisez VMM. 

     ![Sélectionner des objectifs](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 8 : configurer la source et la cible](vmm-to-azure-walkthrough-source-target.md)
