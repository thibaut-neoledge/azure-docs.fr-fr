---
title: "Conditions préalables pour la réplication vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Cet article résume les conditions préalables pour la réplication des machines virtuelles et des machines physiques vers Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/01/2017
ms.author: rajanaki
ms.openlocfilehash: fb5b8c9ac96ac44d0112919664a177f33ef392da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replicating-azure-virtual-machines-to-another-region-by-using-azure-site-recovery"></a>Conditions préalables pour la réplication de machines virtuelles Azure vers une autre région à l’aide d’Azure Site Recovery

> [!div class="op_single_selector"]
> * [Répliquer d’Azure vers Azure](site-recovery-azure-to-azure-prereq.md)
> * [Répliquer d’un emplacement local vers Azure](site-recovery-prereq.md)

Le service Azure Site Recovery participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant les aspects suivants :
* Réplication de machines virtuelles Azure vers une autre région Azure.
* Réplication de machines virtuelles et serveurs physiques locaux vers Azure ou un centre de données secondaire. 

Lorsque des pannes se produisent sur votre site principal, vous pouvez effectuer un basculement sur un site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous pouvez restaurer votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour plus d’informations sur Site Recovery, voir [Qu’est-ce que Site Recovery ?](site-recovery-overview.md).

Cet article résume les conditions préalables à remplir pour démarrer une réplication Site Recovery d’un emplacement local vers Azure.

Envoyez vos commentaires au bas de cet article ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Conditions requises pour Azure

**Prérequis** | **Détails**
--- | ---
**Compte Azure** | Un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
**Service Site Recovery** | Pour plus d’informations sur la tarification de Site Recovery, voir [Tarification Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). Nous vous recommandons de créer un coffre Recovery Services dans la région Azure cible que vous souhaitez utiliser comme emplacement de récupération d’urgence. Par exemple, si vos machines virtuelles sources sont activez dans l’Est des États-Unis, et que vous souhaitez répliquer vers le Centre des États-Unis, nous recommandons de créer le coffre dans la région Centre des États-Unis.|
**Capacité Azure** | Dans la région Azure cible que vous souhaitez utiliser comme emplacement de récupération d’urgence, vous devez disposer d’un abonnement avec une capacité suffisante pour les machines virtuelles, les comptes de stockage et les composants réseau. Pour augmenter la capacité, vous pouvez contacter le support technique.
**Conseils de stockage** | Veillez à suivre les [conseils de stockage](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) pour vos machines virtuelles Azure sources afin d’éviter tout problème de performances. Si vous conservez les paramètres par défaut, Site Recovery crée les comptes de stockage requis en fonction de la configuration de la source. Si vous personnalisez et sélectionnez vos propres paramètres, gardez à l’esprit les [Objectifs d’évolutivité pour les disques de machines virtuelles](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Conseils de mise en réseau** | Vous devez valider la connectivité sortante à partir de votre machine virtuelle Azure pour des URL ou des plages d’adresses IP spécifiques. Pour plus d’informations, voir [Aide à la mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
**Microsoft Azure** | Assurez-vous que tous les certificats racines les plus récents sont présents sur la machine virtuelle Windows ou Linux. Si les certificats racines les plus récents ne sont pas présents, la machine virtuelle ne peut pas s’inscrire auprès du service Site Recovery en raison de contraintes de sécurité.

>[!NOTE]
>Pour plus d’informations sur la prise en charge des configurations spécifiques, consultez la [Matrice de prise en charge](site-recovery-support-matrix-azure-to-azure.md).

## <a name="next-steps"></a>Étapes suivantes
- Découvrir l’[Aide à la mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
- Commencer à protéger vos charges de travail en [répliquant des machines virtuelles Azure](site-recovery-azure-to-azure.md).
