---
title: "Comment fonctionne une réplication de machine virtuelle Azure entre régions Azure dans Azure Site Recovery ?  | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles Azure entre régions Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 7a3e1eb129db530295cbf28bbce1dbe48575d50d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="how-does-azure-vm-replication-work-in-site-recovery"></a>Comment la réplication de machine virtuelle Azure fonctionne-t-elle dans Site Recovery ?


Cet article décrit les composants et processus impliqués dans la réplication et la récupération de machines virtuelles d’une région à une autre à l’aide du service [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>La réplication de machine virtuelle Azure avec le service Site Recovery est actuellement en préversion.

Publiez des commentaires au bas de cet article ou posez des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architectural-components"></a>Composants architecturaux

Le diagramme suivant fournit une vue d’ensemble d’un environnement de machine virtuelle Azure dans une région spécifique (en l’occurrence, Est des États-Unis). Dans un environnement de machine virtuelle Azure :
- Les applications peuvent s’exécuter sur des machines équipées de disques réparties sur plusieurs comptes de stockage.
- Les machines virtuelles peuvent être incluses dans un ou plusieurs sous-réseaux d’un réseau virtuel.

![environnement client](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

Pour en savoir plus sur les conditions préalables au déploiement et la configuration requise, voir la [matrice de prise en charge](site-recovery-support-matrix-azure-to-azure.md).

## <a name="replication-process"></a>Processus de réplication

### <a name="step-1"></a>Étape 1

Lorsque vous activez la réplication de machine virtuelle Azure dans le portail Azure, les ressources affichées dans le diagramme et le tableau suivants sont automatiquement créées dans la région cible. Par défaut, les ressources sont créées en fonction des paramètres de la région source. Vous pouvez personnaliser les paramètres de la région cible en fonction des besoins. [En savoir plus](site-recovery-replicate-azure-to-azure.md).

![Activer le processus de réplication, étape 1](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-1.png)

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

   ![Activer le processus de réplication, étape 2](./media/site-recovery-azure-to-azure-architecture/enable-replication-step-2.png)

   >[!IMPORTANT]
   > Site Recovery n’a jamais besoin de connectivité entrante à la machine virtuelle. La machine virtuelle a besoin uniquement d’une connectivité sortante aux URL/adresses IP du service Site Recovery, aux URL/adresses IP d’authentification d’Office 365, et aux adresses IP de compte de stockage de cache. Pour plus d’informations, voir [Aide à la mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).

## <a name="continuous-replication-process"></a>Processus de réplication continue

Une fois que la réplication continue opère, les écritures sur disque sont immédiatement transférées vers le compte de stockage de cache. Site Recovery traite les données et les envoie au compte de stockage cible. Une fois les données traitées, des points de récupération sont générés dans le compte de stockage cible à intervalles de quelques minutes.

## <a name="failover-process"></a>Processus de basculement

Lorsque vous démarrez un basculement, les machines virtuelles sont créées dans le groupe de ressources cible, le réseau virtuel cible, le sous-réseau cible et le groupe à haute disponibilité cible. Lors d’un basculement, vous pouvez utiliser n’importe quel point de récupération.

![Processus de basculement](./media/site-recovery-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [mise en réseau](site-recovery-azure-to-azure-networking-guidance.md) pour la réplication de machine virtuelle Azure.
- Suivre une procédure pas à pas pour [répliquer des machines virtuelles Azure](site-recovery-azure-to-azure.md).
