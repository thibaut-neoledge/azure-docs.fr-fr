---
title: "Configurer un coffre pour la réplication de machines virtuelles Azure entre des régions avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes à suivre pour configurer un coffre pour la réplication Azure entre des régions Azure à l’aide d’Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---

# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>Étape 4 : Configurer un coffre pour la réplication entre régions Azure

Après avoir [planifié les réseaux](azure-to-azure-walkthrough-network.md), utilisez cet article afin de configurer un coffre pour la réplication de machines virtuelles vers une autre région Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

- Une fois l’article terminé, vous devez avoir un coffre Recovery Services configuré.
- Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



>[!NOTE]
>
> La réplication de machines virtuelles Azure est disponible en préversion.




## <a name="create-a-vault"></a>création d'un coffre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Nous vous recommandons de créer le coffre Recovery Services à l’emplacement où vous souhaitez répliquer vos machines virtuelles. Par exemple, si votre emplacement cible est la région États-Unis du Centre, créez le coffre dans **États-Unis du Centre**.


## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 5 : Activer la réplication](azure-to-azure-walkthrough-enable-replication.md)

