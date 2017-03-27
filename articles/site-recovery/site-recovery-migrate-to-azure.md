---
title: Migrer vers Azure avec Site Recovery | Microsoft Docs
description: "Cet article présente la migration de machines virtuelles et de serveurs physiques vers Azure avec Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 981155c38bdc8cb54639d2271be1f3bd3036125c
ms.lasthandoff: 03/09/2017


---
# <a name="migrate-to-azure-with-site-recovery"></a>Effectuer une migration vers Azure avec Site Recovery

Lisez cet article pour apprendre à utiliser le service Azure Site Recovery pour la migration des machines virtuelles et des serveurs physiques.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md) Vous pouvez également utiliser Site Recovery pour migrer vos charges de travail locales existantes vers Azure afin d’accélérer votre transition vers le cloud et de bénéficier de l’ensemble de fonctionnalités offertes par Azure.

Pour obtenir un aperçu rapide de la procédure permettant d’effectuer une migration, reportez-vous à cette vidéo.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]

Cet article décrit le déploiement dans le [portail Azure](https://portal.azure.com). Le [portail Azure Classic](https://manage.windowsazure.com/) permet de gérer les coffres Site Recovery existants, mais pas de créer des coffres.

Postez des commentaires à la fin de cet article. Publiez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>Quelle est notre définition de la migration ?

Vous pouvez déployer Site Recovery pour la réplication de machines virtuelles locales et de serveurs physiques vers Azure ou un site secondaire. Répliquez des machines virtuelles, basculez-les à partir du site principal en cas de panne et restaurez-les vers le site principal après la récupération. Par ailleurs, vous pouvez utiliser Site Recovery pour migrer des machines virtuelles et des serveurs physiques vers Azure, afin que les utilisateurs y accèdent comme à des machines virtuelles Azure. La migration entraîne la réplication et le basculement du site principal vers Azure, ainsi qu’un mouvement de migration complet.

## <a name="what-can-site-recovery-migrate"></a>Que peut migrer Site Recovery ?

Vous pouvez :

- Migrer des charges de travail s’exécutant sur des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques locaux, pour les exécuter sur des machines virtuelles Azure. Vous pouvez également effectuer une réplication complète et une restauration automatique dans ce scénario.
- Migrer des [machines virtuelles IaaS Azure](site-recovery-migrate-azure-to-azure.md) entre des régions Azure. Actuellement, seule la migration est prise en charge dans ce scénario, pas la restauration automatique.
- Migrer des [instances Windows AWS](site-recovery-migrate-aws-to-azure.md) vers des machines virtuelles IaaS Azure. Actuellement, seule la migration est prise en charge dans ce scénario, pas la restauration automatique.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migrer des machines virtuelles et des serveurs physiques locaux

Pour migrer des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques locaux, vous suivez presque les mêmes étapes que celles utilisées pour la réplication normale. Vous configurez un coffre Recovery Services et les serveurs d’administration nécessaires (en fonction de ce que vous voulez migrer), puis vous les ajoutez au coffre et spécifiez les paramètres de réplication. Vous activez la réplication pour les machines que vous souhaitez migrer, puis vous exécutez un test rapide de basculement pour vous assurer que tout fonctionne comme prévu.

Après avoir vérifié que votre environnement de réplication fonctionne, vous utilisez un basculement planifié ou non en fonction de [ce qui est pris en charge](site-recovery-failover.md) pour votre scénario. Pour la migration, vous n’avez pas besoin de valider un basculement. Au lieu de cela, vous sélectionnez l’option **Effectuer la migration** pour chacune des machines que vous souhaitez migrer. L’action **Effectuer la migration** termine le processus de migration, supprime la réplication de la machine et arrête la facturation de Site Recovery pour la machine.

![completemigration](./media/site-recovery-hyper-v-site-to-azure/migrate.png)

## <a name="migrate-between-azure-regions"></a>Migrer entre des régions Azure

Vous pouvez migrer des machines virtuelles Azure entre des régions à l’aide de Site Recovery. Dans ce scénario, seule la migration est prise en charge. En d’autres termes, vous pouvez répliquer les machines virtuelles Azure et les basculer vers une autre région, mais vous ne pouvez pas effectuer de restauration automatique. Dans ce scénario, vous configurez un coffre Recovery Services, puis déployez un serveur de configuration locale pour gérer la réplication. Vous ajoutez ce serveur dans le coffre et vous indiquez les paramètres de réplication. Vous activez la réplication pour les machines que vous souhaitez migrer et vous exécutez un test rapide de basculement. Vous exécutez ensuite un basculement non planifié avec l’option **Effectuer la migration**.

## <a name="migrate-aws-to-azure"></a>Migrer AWS vers Azure

Vous pouvez migrer des instances AWS vers des machines virtuelles Azure. Dans ce scénario, seule la migration est prise en charge. En d’autres termes, vous pouvez répliquer les instances AWS et les basculer vers Azure, mais vous ne pouvez pas effectuer de restauration automatique. Les instances AWS sont gérées de la même façon que les serveurs physiques lors de la migration. Vous configurez un coffre Recovery Services, puis déployez un serveur de configuration locale pour gérer la réplication. Vous ajoutez ce serveur dans le coffre et vous indiquez les paramètres de réplication. Vous activez la réplication pour les machines que vous souhaitez migrer et vous exécutez un test rapide de basculement. Vous exécutez ensuite un basculement non planifié avec l’option **Effectuer la migration**.




## <a name="next-steps"></a>Étapes suivantes

- [Migrate VMware VMs to Azure (Migrer des machines virtuelles VMware vers Azure)](site-recovery-vmware-to-azure.md)
- [Migrate Hyper-V VMs in VMM clouds to Azure (Migrer des machines virtuelles Hyper-V dans des clouds VMM vers Azure)](site-recovery-vmm-to-azure.md)
- [Migrate Hyper-V VMs without VMM to Azure (Migrer des machines virtuelles Hyper-V sans VMM vers Azure)](site-recovery-hyper-v-site-to-azure.md)
- [Migrate Azure VMs between Azure regions (Migrer des machines virtuelles Azure entre des régions Azure)](site-recovery-migrate-azure-to-azure.md)
- [Migrate AWS instances to Azure (Migrer des instances AWS vers Azure)](site-recovery-migrate-aws-to-azure.md)

