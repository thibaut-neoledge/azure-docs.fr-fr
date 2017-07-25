---
title: "Préparation d’hôtes Hyper-V (sans System Center VMM) pour la réplication vers Azure | Microsoft Docs"
description: "Décrit comment préparer des ordinateurs hôtes Hyper-V pour la réplication vers Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.contentlocale: fr-fr
ms.lasthandoff: 07/20/2017

---

# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Étape 6 : préparer des ordinateurs hôtes Hyper-V pour la réplication vers Azure

Suivez les instructions de cet article pour préparer des hôtes Hyper-V locaux à interagir avec Azure Site Recovery.

Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Préparer des ordinateurs hôtes

- Assurez-vous que les hôtes Hyper-V respectent la [configuration requise](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Assurez-vous que les hôtes peuvent accéder aux URL requises :

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.
- Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
- Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

Pendant le déploiement de Site Recovery, vous ajoutez des hôtes Hyper-V qui contiennent des machines virtuelles que vous souhaitez répliquer sur un site Hyper-V. Le fournisseur et l’agent Site Recovery sont installés sur chaque hôte. Le site Hyper-V est inscrit dans le coffre Recovery Services.

## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 7 : créer un coffre](hyper-v-site-walkthrough-create-vault.md)


