---
title: "Présentation de l’architecture utilisée pour la réplication Hyper-V sur un site secondaire avec Azure Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble de l’architecture utilisée pour la réplication de machines virtuelles Hyper-V locales sur un site System Center VMM secondaire avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 07099161-4cc7-4f32-8eb9-2a71bbf0750b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: b78cd0d5a5395873afaddc8856004775f447e8ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-hyper-v-replication-to-a-secondary-site"></a>Étape 1 : présentation de l’architecture utilisée pour la réplication Hyper-V sur un site secondaire

Cet article décrit les composants et les processus impliqués dans la réplication des machines virtuelles Hyper-V locales dans des clouds System Center Virtual Machine Manager (VMM) sur un site VMM secondaire à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="architectural-components"></a>Composants architecturaux

Voici ce dont vous avez besoin pour la réplication des machines virtuelles Hyper-V sur un site VMM secondaire.

**Composant** | **Emplacement** | **Détails**
--- | --- | ---
**Microsoft Azure** | Abonnement dans Azure. | Vous créez un coffre Recovery Services dans l’abonnement Azure pour orchestrer et gérer la réplication entre les emplacements VMM.
**Serveur VMM** | Vous avez besoin d’un emplacement VMM principal et secondaire. | Nous recommandons l’utilisation d’un serveur VMM dans le site principal et un autre dans le site secondaire 
**Serveur Hyper-V** |  Un ou plusieurs serveurs hôtes Hyper-V dans les clouds VMM principaux et secondaires. | Les données sont répliquées entre les serveurs hôtes Hyper-V principal et secondaire via une liaison LAN ou VPN, en utilisant Kerberos ou une authentification par certificat.  
**Machines virtuelles Hyper-V** | Sur le serveur hôte Hyper-V. | Le serveur hôte source doit avoir au moins une machine virtuelle que vous souhaitez répliquer.

## <a name="replication-process"></a>Processus de réplication

1. Vous configurez le compte Azure, créez un coffre Recovery Services et spécifiez ce que vous souhaitez répliquer.
2. Vous configurez les paramètres de réplication source et cible, notamment l’installation du fournisseur Azure Site Recovery sur les serveurs VMM et l’agent Microsoft Azure Recovery Services sur chaque hôte Hyper-V.
3. Vous créez une stratégie de réplication pour le cloud VMM source. La stratégie est appliquée à toutes les machines virtuelles situées sur des hôtes dans le cloud.
4. Vous activez la réplication pour chaque VMM, et la réplication initiale d’une machine virtuelle a lieu conformément aux paramètres que vous choisissez.
5. À l’issue de la réplication initiale, la réplication des modifications delta commence. Les modifications qui font l’objet d’un suivi sont conservées dans un fichier .hrl.


![Local vers local](./media/vmm-to-vmm-walkthrough-architecture/arch-onprem-onprem.png)

## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

1. Vous pouvez effectuer un [basculement](site-recovery-failover.md) planifié ou non planifié entre des sites locaux. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données.
2. Vous pouvez basculer vers une seule machine ou créer des [plans de récupération](site-recovery-create-recovery-plans.md) pour orchestrer le basculement de plusieurs machines.
4. Si vous effectuez un basculement non planifié vers un site secondaire, une fois l’opération terminée, les machines de l’emplacement secondaire ne sont pas protégées ou réplicables. Si vous avez lancé un basculement planifié, une fois l’opération terminée, les machines de l’emplacement secondaire sont protégées.
5. Ensuite, validez le basculement pour accéder à la charge de travail à partir de la machine virtuelle répliquée.
6. Lorsque votre site principal est à nouveau disponible, vous déclenchez la réplication inverse depuis le site secondaire vers le site principal. La réplication inverse affecte aux machines virtuelles l’état protégé, mais l’emplacement actif est toujours le centre de données secondaire.
7. Pour placer le site principal à l’emplacement actif, lancez un basculement planifié depuis le site secondaire vers le site principal, puis une autre réplication inverse.



## <a name="next-steps"></a>Étapes suivantes

Allez à [Step 2: Review the prerequisites and limitations for Hyper-V VM replication to a secondary VMM site](vmm-to-vmm-walkthrough-prerequisites.md) (Étape 2 : vérifier les conditions préalables et les limitations pour la réplication de machine virtuelle Hyper-V sur un site VMM secondaire).
