---
title: "Préparation de System Center VMM pour la réplication Hyper-V vers Azure | Microsoft Docs"
description: "Décrit comment préparer le serveur System Center VMM pour la réplication Hyper-V vers Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---

# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Étape 6 : Préparer les serveurs VMM et les hôtes Hyper-V pour la réplication Hyper-V vers Azure

Après avoir configuré les [composants Azure](vmm-to-azure-walkthrough-prepare-azure.md) pour le déploiement, suivez les instructions dans cet article pour préparer les serveurs VMM et les hôtes Hyper-V locaux pour interagir avec Azure Site Recovery.

Après avoir lu cet article, envoyez vos commentaires en bas ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Préparer les serveurs VMM

- Vous devez avoir au moins un serveur VMM conforme aux exigences de la réplication Site Recovery (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Assurez-vous d’avoir préparé le serveur VMM pour le [mappage réseau](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Vérifier que le serveur VMM peut accéder à ces URL

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.
- Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).
- Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).

Pendant un déploiement de Site Recovery, vous téléchargez le fournisseur Azure Site Recovery et vous l’installez sur chaque serveur VMM. Le serveur VMM est inscrit dans le coffre Recovery Services.




## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 7 : créer un coffre](vmm-to-azure-walkthrough-create-vault.md)


