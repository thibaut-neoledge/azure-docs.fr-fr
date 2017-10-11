---
title: "Passer en revue les conditions préalables pour la réplication Hyper-V sur un site secondaire VMM avec Azure Site Recovery | Documents Microsoft"
description: "Décrit les conditions préalables de la réplication de machines virtuelles Hyper-V sur un site secondaire VMM avec Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Étape 2 : passer en revue les conditions préalables et les limitations pour la réplication de machine virtuelle Hyper-V vers un site VMM secondaire


Après avoir passé en revue les conditions préalables de [l’architecture de scénarios](vmm-to-vmm-walkthrough-architecture.md), lisez cet article pour comprendre les conditions requises du déploiement lors de la réplication de machines virtuelles Hyper-V locales gérées dans les clouds du System Center Virtual Machines Manager (VMM) sur un site secondaire à l’aide [d’Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Après avoir lu cet article, publiez des commentaires au bas de ce dernier ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Conditions préalables et limitations

**Prérequis** | **Détails**
--- | ---
**Microsoft Azure** | Un abonnement [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery.<br/><br/> Vérifiez les régions prises en charge pour Site Recovery dans la section Disponibilité géographique de la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
**Serveurs VMM** | Nous vous recommandons de déployer 2 serveurs VMM, un sur le site principal et un sur le site secondaire.<br/><br/> La réplication entre des clouds sur un seul serveur VMM est prise en charge.<br/><br/> Les serveurs VMM doivent exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Les serveurs VMM doivent avoir accès à Internet.
**Clouds VMM** | Chaque serveur VMM doit disposer d’un ou plusieurs clouds et tous les clouds doivent avoir le profil de capacité Hyper-V. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM.<br/><br/> Si vous ne disposez que d’un seul serveur VMM, vous devez lui associer au moins 2 clouds, agissant en tant qu’instances primaire et secondaire.
**Hyper-V** | Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster sur Windows Server 2012 R2, installez la [mise à jour 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Configurez manuellement le répartiteur de cluster. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Les serveurs Hyper-V doivent disposer d’un accès Internet.




## <a name="next-steps"></a>Étapes suivantes

Aller à [l’étape 3 : planifier la mise en réseau](vmm-to-vmm-walkthrough-network.md).
