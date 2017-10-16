---
title: "Passer en revue les conditions préalables pour la réplication Hyper-V vers Azure (avec System Center VMM) à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Décrit les conditions préalables pour configurer la réplication, le basculement et la récupération des machines virtuelles Hyper-V locales dans les clouds VMM vers Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Étape 2 : Passer en revue les conditions préalables pour la réplication Hyper-V vers Azure (avec VMM)

Une fois l’[architecture du scénario](vmm-to-azure-walkthrough-architecture.md) passé en revue, lisez cet article pour être sûr de comprendre les conditions préalables du déploiement. 

## <a name="prerequisites-and-limitations"></a>Conditions préalables et limitations

**Prérequis** | **Détails**
--- | ---
**Compte Azure** | Vous avez besoin d’un [compte Microsoft Azure](http://azure.microsoft.com/).
**Stockage Azure** | Vous avez besoin d’un compte de stockage Azure pour stocker les données répliquées.<br/><br/> Le compte de stockage doit se trouver dans la même région que le coffre Recovery Services.<br/><br/>Vous pouvez utiliser le [stockage géoredondant](../storage/common/storage-redundancy.md#geo-redundant-storage) ou le stockage localement redondant. Nous vous recommandons d’utiliser le stockage géo-redondant. En utilisant le stockage géo-redondant, les données sont résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée.<br/><br/> Vous pouvez utiliser un compte de stockage Azure standard ou Azure [Premium](../storage/common/storage-premium-storage.md). Le stockage Premium peut héberger des charges de travail nécessitant beaucoup d’E/S et est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence. Si vous utilisez un stockage Premium pour les données répliquées, il vous faut également un compte de stockage standard. Un compte de stockage standard permet de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.
**Réseau Azure** | Vous devez avoir un [réseau Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md), auquel les machines virtuelles Azure se connectent après basculement. Le réseau Azure doit se trouver dans la même région que le coffre Recovery Services.
**Serveurs VMM locaux** | Vous avez besoin d’un ou plusieurs serveurs VMM exécutant System Center 2012 R2 ou une version ultérieure.<br/><br/> Chaque serveur VMM doit disposer d’un ou de plusieurs clouds privés. Chaque cloud nécessite un ou la plupart des groupes hôte.<br/><br/> Le serveur VMM nécessite un accès à Internet.
**Hyper-V local** | Les serveurs hôtes Hyper-V doivent exécuter au moins Windows Server 2012 R2 avec le rôle Hyper-V activé ou Microsoft Hyper-V Server 2012 R2. Les dernières mises à jour doivent être installées.<br/><br/> L’hôte Hyper-V doit se trouver dans un groupe hôte (lui-même situé dans un cloud VMM).<br/><br/> Un hôte doit disposer d’une ou de plusieurs machines virtuelles que vous souhaitez répliquer.<br/><br/> Les hôtes Hyper-V doivent être connectés à internet (directement ou via un proxy) pour que la réplication vers Azure soit possible. Les correctifs détaillés dans l’article [2961977](https://support.microsoft.com/kb/2961977) doivent être installés sur les serveurs Hyper-V.
**Machines virtuelles Hyper-V en local** | Les machines virtuelles que vous souhaitez répliquer doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) et être conformes aux [conditions préalables pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). Le nom de la machine virtuelle peut être modifié après activation de la réplication. 




## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 3 : Planifier la capacité](vmm-to-azure-walkthrough-capacity.md)
