---
title: "Migrer des machines virtuelles sur site et des serveurs physiques vers Azure à l’aide de Site Recovery | Microsoft Docs"
description: "Cet article décrit la migration de machines virtuelles sur site et de serveurs physiques vers Azure à l’aide de Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Effectuer une migration vers Azure avec Site Recovery

Lisez cet article pour en savoir plus sur l’utilisation du service [Azure Site Recovery](site-recovery-overview.md) pour migrer des machines virtuelles (VM) locales et des serveurs physiques vers des machines virtuelles Azure.

## <a name="before-you-start"></a>Avant de commencer

Regardez cette vidéo pour un aperçu rapide des étapes requises pour migrer vers Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>Quelle est notre définition de la migration ?

Vous pouvez déployer Site Recovery pour la réplication de machines virtuelles locales et de serveurs physiques et la migration de ces derniers.

- Lorsque vous répliquez, vous configurez les machines locales pour se répliquer sur une base régulière vers Azure. En cas de panne, vous basculez les machines à partir du site local vers Azure, puis y accédez à partir de ce dernier. Lorsque le site local principal est à nouveau disponible, vous pouvez lancer la restauration automatique à partir d’Azure.
- Lorsque vous utilisez Site Recovery pour la migration, vous répliquez des machines locales vers Azure. Ensuite, vous les basculez à partir de votre site local vers Azure et terminez le processus de migration. Aucune restauration automatique n’est mise en jeu.  

## <a name="what-can-site-recovery-migrate"></a>Que peut migrer Site Recovery ?

Vous pouvez :

- **Migrer à partir du site local** : migrer des machines virtuelles Hyper-V, des machines virtuelles VMware et des serveurs physiques locaux vers Azure. Après la migration, les charges de travail en cours d’exécution sur les machines locales seront exécutées sur les machines virtuelles Azure. 
- **Migrer au sein d’Azure** : migrer des machines virtuelles Azure entre des régions Azure. 
- **Migrer AWS** : migrer des instances Windows AWS vers des machines virtuelles IaaS Azure. 

## <a name="migrate-from-on-premises-to-azure"></a>Migrer de l’environnement local vers Azure

Pour migrer des machines virtuelles VMware, des machines virtuelles Hyper-V et des serveurs physiques locaux, vous suivez presque les mêmes étapes que celles utilisées pour la réplication complète. 


## <a name="migrate-between-azure-regions"></a>Migrer entre des régions Azure

Pour migrer des machines virtuelles Azure entre les régions, vous suivez presque les mêmes étapes que celles utilisées pour la migration complète.

1. Vous [activez la réplication](azure-to-azure-tutorial-enable-replication.md) pour les machines que vous souhaitez migrer.
2. Vous [effectuez un test rapide de basculement](azure-to-azure-tutorial-dr-drill.md) pour vérifier que tout fonctionne bien.
3. Vous [exécutez ensuite un basculement non planifié](azure-to-azure-tutorial-failover-failback.md) avec l’option **Effectuer la migration**.
4. Une fois que vous avez effectué la migration, vous pouvez [configurer la réplication pour la récupération d’urgence](site-recovery-azure-to-azure-after-migration.md), à partir de la région Azure à laquelle vous avez migré, vers une région secondaire.



## <a name="migrate-aws-to-azure"></a>Migrer AWS vers Azure

Vous pouvez migrer des instances AWS vers des machines virtuelles Azure.
- Dans ce scénario, seule la migration est prise en charge. En d’autres termes, vous pouvez répliquer les instances AWS et les basculer vers Azure, mais vous ne pouvez pas effectuer de restauration automatique.
- Les instances AWS sont gérées de la même façon que les serveurs physiques lors de la migration. Vous configurez un coffre Recovery Services, puis déployez un serveur de configuration locale pour gérer la réplication. Vous ajoutez ce serveur dans le coffre et vous indiquez les paramètres de réplication.
- Vous activez la réplication pour les machines que vous souhaitez migrer et vous exécutez un test rapide de basculement. Vous exécutez ensuite un basculement non planifié avec l’option **Effectuer la migration**.






## <a name="next-steps"></a>Étapes suivantes

- [Migrer des machines locales vers Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migrer des machines virtuelles d’une région Azure vers une autre](site-recovery-migrate-azure-to-azure.md)
- [Migrer AWS vers Azure](tutorial-migrate-aws-to-azure.md)
