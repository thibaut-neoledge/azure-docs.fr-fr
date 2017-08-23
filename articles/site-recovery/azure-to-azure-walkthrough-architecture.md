---
title: "Examen de l’architecture de réplication des machines virtuelles Azure entre les régions Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles Azure entre régions Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---

# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>Étape 1 : Examen de l’architecture de réplication des machines virtuelles Azure entre les régions Azure


Après avoir examiné les [étapes de présentation](azure-to-azure-walkthrough-overview.md) de ce déploiement, lisez cet article pour comprendre les composants et les processus utilisés lors de la réplication et de la récupération des machines virtuelles Azure d’une région Azure à l’autre, à l’aide d’[Azure Site Recovery](site-recovery-overview.md).

- À la fin de cet article, vous devriez avoir compris le fonctionnement de la réplication de machines virtuelles Azure d’une région à l’autre.
- Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

>[!NOTE]
>La réplication de machine virtuelle Azure avec le service Site Recovery est actuellement en préversion.



## <a name="architectural-components"></a>Composants architecturaux

Le diagramme suivant fournit une vue d’ensemble d’un environnement de machine virtuelle Azure dans une région spécifique (en l’occurrence, Est des États-Unis). Dans un environnement de machine virtuelle Azure :
- Les applications peuvent s’exécuter sur des machines équipées de disques réparties sur plusieurs comptes de stockage.
- Les machines virtuelles peuvent être incluses dans un ou plusieurs sous-réseaux d’un réseau virtuel.

![environnement client](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>Processus de réplication

### <a name="step-1"></a>Étape 1

Lorsque vous activez la réplication de machine virtuelle Azure dans le portail Azure, les ressources affichées dans le diagramme et le tableau suivants sont automatiquement créées dans la région cible. Par défaut, les ressources sont créées en fonction des paramètres de la région source. Vous pouvez personnaliser les paramètres de la région cible en fonction des besoins. [En savoir plus](site-recovery-replicate-azure-to-azure.md).

![Activer le processus de réplication, étape 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

**Ressource** | **Détails**
--- | ---
**Groupe de ressources cible** | Groupe de ressources auquel appartiennent les machines virtuelles répliquées après le basculement.
**Réseau virtuel cible** | Réseau virtuel dans lequel les machines virtuelles répliquées sont situées après le basculement. Un mappage réseau est créé entre les réseaux virtuels source et cible, et inversement.
**Comptes Stockage de cache** | Avant que les modifications apportées aux machines virtuelles sources soient répliquées vers le compte de stockage cible, elles sont suivies et envoyées au compte de stockage de cache dans l’emplacement cible. Cela garantit un impact minimal sur les applications de production s’exécutant sur la machine virtuelle.
**Comptes de stockage cibles**  | Comptes de stockage dans l’emplacement cible vers lesquels les données sont répliquées.
**Groupes à haute disponibilité cibles**  | Groupes à haute disponibilité dans lesquels se trouvent les machines virtuelles répliquées après basculement.

### <a name="step-2"></a>Étape 2

À mesure que la réplication est activée, le service Mobilité de l’extension de Site Recovery est automatiquement installé sur la machine virtuelle. Voici ce qui se produit :

1. La machine virtuelle est inscrite auprès de Site Recovery.

2. Une réplication continue est configurée pour la machine virtuelle. Les écritures de données sur les disques de machine virtuelle sont transférées en continu vers le compte de stockage de cache dans ’emplacement source.

   ![Activer le processus de réplication, étape 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  Notez que Site Recovery n’a jamais besoin de connectivité entrante à la machine virtuelle. Seule une connectivité sortante aux URL/adresses IP du service Site Recovery, aux URL/adresses IP d’authentification d’Office 365, et aux adresses IP de compte de stockage de cache est requise. 

## <a name="continuous-replication-process"></a>Processus de réplication continue

Une fois que la réplication continue opère, les écritures sur disque sont immédiatement transférées vers le compte de stockage de cache. Site Recovery traite les données et les envoie au compte de stockage cible. Une fois les données traitées, des points de récupération sont générés dans le compte de stockage cible à intervalles de quelques minutes.

## <a name="failover-process"></a>Processus de basculement

Lorsque vous démarrez un basculement, les machines virtuelles sont créées dans le groupe de ressources cible, le réseau virtuel cible, le sous-réseau cible et le groupe à haute disponibilité cible. Lors d’un basculement, vous pouvez utiliser n’importe quel point de récupération.

![Processus de basculement](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 2 : vérifier les conditions préalables et les limitations](azure-to-azure-walkthrough-prerequisites.md)

