---
title: "Passer en revue les conditions préalables pour la réplication Hyper-V vers Azure (sans System Center VMM) à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit les conditions préalables pour configurer la réplication, le basculement et la récupération des machines virtuelles Hyper-V locales vers Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/20/2017

---

# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>Étape 2 : Passer en revue les conditions préalables pour la réplication Hyper-V vers Azure (sans VMM)

Les conditions préalables sont résumées dans le tableau.


**Configuration requise** | **Détails** 
--- | --- 
**Microsoft Azure** | Découvrez la [configuration requise pour Azure](site-recovery-prereq.md#azure-requirements).
**Serveurs locaux** | [Découvrez](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm) la configuration requise pour les hôtes Hyper-V locaux.
**Machines virtuelles Hyper-V en local** | Les machines virtuelles que vous souhaitez répliquer doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) et être conformes aux [conditions préalables pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URL Azure** | Les hôtes Hyper-V doivent accéder à ces URL :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/></br> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/></br> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).



## <a name="next-steps"></a>Étapes suivantes

- Si vous effectuez un déploiement complet, accédez à [Étape 3 : Planifier la capacité](hyper-v-site-walkthrough-capacity.md)
- Si vous effectuez un déploiement test, accédez à [Étape 4 : Planifier la mise en réseau](hyper-v-site-walkthrough-network.md).

