---
title: "Avant de commencer la réplication de machines virtuelles Azure vers une autre région | Microsoft Docs"
description: "Résume les étapes à suivre avant la réplication de machines virtuelles Azure entre des régions Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: d38fc766d5226be7161433555da9622e006c80e9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/31/2017

---

# <a name="step-2-before-you-start"></a>Étape 2 : avant de commencer

Après avoir examiné l’[architecture](azure-to-azure-walkthrough-architecture.md) de réplication de machines virtuelles Azure entre des régions Azure à l’aide d’[Azure Site Recovery](site-recovery-overview.md), utilisez cet article pour vérifier les conditions préalables à remplir.

- À la fin de cet article, vous devriez avoir compris les conditions préalables à un bon déploiement, et suivi les étapes pour les remplir.
- Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>
> La réplication de machines virtuelles Azure est actuellement disponible en préversion.



## <a name="support-recommendations"></a>Recommandations de prise en charge

Consultez le tableau ci-dessous. Obtenez la liste complète des exigences de prise en charge dans la [matrice de prise en charge.](site-recovery-support-matrix-azure-to-azure.md)

**Composant** | **Prérequis**
--- | ---
**Coffre Recovery Services** | Nous vous recommandons de créer un coffre Recovery Services dans la région Azure cible que vous souhaitez utiliser pour la récupération d’urgence. Par exemple, si vous souhaitez répliquer des machines virtuelles sources de l’Est des États-Unis vers le Centre des États-Unis, créez le coffre à l’emplacement Centre des États-Unis.
**Abonnement Azure** | Votre abonnement Azure doit être activé pour créer des machines virtuelles à l’emplacement cible que vous souhaitez utiliser en tant que région de récupération d’urgence. Contactez le support pour activer le quota requis.
**Capacité de la région cible** | Dans la région Azure cible, l’abonnement doit disposer d’une capacité suffisante pour les machines virtuelles, les comptes de stockage et les composants réseau.
**Stockage** | Utilisez les [conseils de stockage](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) pour vos machines virtuelles Azure sources afin d’éviter les problèmes de performances.<br/><br/> Les comptes de stockage doivent se trouver dans la même région que le coffre.<br/><br/> Vous ne pouvez pas effectuer de réplications dans des comptes premium dans le centre et le sud de l’Inde.<br/><br/> Si vous déployez la réplication avec les paramètres par défaut, Site Recovery crée les comptes de stockage requis en fonction de la configuration de la source. Si vous personnalisez des paramètres, suivez les [objectifs d’extensibilité des disques de machines virtuelles](../storage/common/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks).
**Mise en réseau** | Vous devez autoriser la connectivité sortante à partir des machines virtuelles Azure pour des plages d’adresse IP/URL spécifiques.<br/><br/> Les comptes de réseau doivent se trouver dans la même région que le coffre.
**Microsoft Azure** | Assurez-vous que l’ensemble des certificats racines les plus récents se trouvent sur la machine virtuelle Azure Windows/Linux. Dans le cas contraire, vous ne pourrez pas inscrire la machine virtuelle dans Site recovery en raison de contraintes de sécurité.
**Compte d’utilisateur Azure** | Pour pouvoir activer la réplication d’une machine virtuelle Azure, votre compte d’utilisateur Azure doit disposer de certaines [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).


## <a name="set-permissions-on-the-account"></a>Définir les autorisations sur le compte

1. En savoir plus sur les [autorisations](site-recovery-role-based-linked-access-control.md) nécessaires pour la réplication.
2. Suivez ces [instructions](../active-directory/role-based-access-control-configure.md#add-access) pour ajouter des autorisations.


## <a name="verify-target-resources"></a>Vérifier les ressources cibles

1. Activez votre abonnement Azure pour créer des machines virtuelles dans la région cible que vous souhaitez utiliser comme région de récupération d’urgence. Contactez le support pour activer le quota requis.
2. Assurez-vous que votre abonnement dispose de suffisamment de ressources pour activer des machines virtuelles de tailles correspondant à vos machines virtuelles source. Par défaut, lorsque vous configurez la réplication, Site Recovery choisit la même taille de machine virtuelle cible, ou la taille la plus proche possible. En savoir plus sur la [résolution des problèmes](site-recovery-azure-to-azure-troubleshoot-errors.md#azure-resource-quota-issues-error-code-150097) liés aux ressources cibles.

## <a name="verify-azure-vm-certificates"></a>Vérifier les certificats des machines virtuelles Azure

1. Assurez-vous que tous les certificats racines les plus récents sont présents sur les machines virtuelles Windows ou Linux à répliquer. Si les certificats racines les plus récents ne sont pas présents, la machine virtuelle ne peut pas s’inscrire auprès du service Site Recovery en raison de contraintes de sécurité.
2. Pour les machines virtuelles Windows, procédez comme suit :

    - Installez toutes les mises à jour de Windows les plus récentes sur la machine virtuelle pour que tous les certificats racines approuvés s’y trouvent.
    - Dans un environnement déconnecté, suivez le processus de mise à jour de Windows/des certificats standard en vigueur dans votre organisation pour avoir les certificats racines les plus récents et la liste de révocation de certificats mise à jour sur les machines virtuelles.
3. Pour les machines virtuelles Linux, suivez les instructions fournies par votre distributeur Linux pour obtenir les certificats racines approuvés les plus récents et la dernière liste de révocation de certificats sur la machine virtuelle. En savoir plus sur la [résolution des problèmes](site-recovery-azure-to-azure-troubleshoot-errors.md#trusted-root-certificates-error-code-151066) liés aux certificats racines approuvés.


## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 3 : planifier la mise en réseau](azure-to-azure-walkthrough-network.md) pour configurer la connectivité sortante.

