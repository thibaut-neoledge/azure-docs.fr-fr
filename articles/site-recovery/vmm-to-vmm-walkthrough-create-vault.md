---
title: "Création d’un coffre pour la réplication Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Décrit comment créer un coffre lors de la réplication de machines virtuelles Hyper-V sur un site secondaire System Center VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/01/2017

---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Étape 5 : Créer un coffre pour la réplication Hyper-V sur un site secondaire

Après avoir préparé [les serveurs System Center Virtual Machine Manager (VMM) et les hôtes/clusters Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md) locaux pour la réplication Hyper-V sur un site secondaire à l’aide [d’Azure Site Recovery](site-recovery-overview.md), vous pouvez créer un coffre Recovery Services et sélectionner le scénario de réplication.

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Choisir un objectif en matière de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Cliquez sur **Site Recovery** > **Étape 1 : Préparez l’infrastructure** > **Objectif de protection**.
2. Sélectionnez **Vers le site de récupération**, puis **Oui, avec Hyper-V**.
3. Sélectionnez **Oui** pour indiquer que vous utilisez VMM pour gérer les hôtes Hyper-V.
4. Sélectionnez **Oui** si vous avez un serveur VMM secondaire. Si vous déployez la réplication entre des clouds sur un seul serveur VMM, cliquez sur **Non**. Cliquez ensuite sur **OK**.

    ![Sélectionner des objectifs](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 6 : Configurer la source et la cible de réplication](vmm-to-vmm-walkthrough-source-target.md).
