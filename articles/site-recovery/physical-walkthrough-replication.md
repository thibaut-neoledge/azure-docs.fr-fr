---
title: "Configurer une stratégie de réplication pour la réplication de serveurs physiques vers Azure à l’aide d’Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour configurer une stratégie de réplication lors de la réplication de serveurs physiques locaux vers le stockage Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 9ce23382001b54e7b9b7a58b8dd9aa61b400826d
ms.contentlocale: fr-fr
ms.lasthandoff: 06/29/2017


---
# <a name="step-8-set-up-a-replication-policy-for-physical-server-replication-to-azure"></a>Étape 8 : Configurer une stratégie de réplication pour la réplication de serveurs physiques vers Azure


Cet article explique comment configurer une stratégie de réplication pour la réplication de serveurs physiques Windows/Linux vers Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.


Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Configurer une stratégie

1. Cliquez sur **Infrastructure de Site Recovery** > **Stratégies de réplication** > **+Stratégie de réplication**.
2. Dans **Créer une stratégie de réplication**, indiquez le nom de la stratégie.
3. Dans le champ **Seuil d’objectif de point de récupération**, spécifiez la limite de l’objectif de point de récupération. Cette valeur indique la fréquence à laquelle les points de récupération des données sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
4. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre. Les machines virtuelles répliquées vers Premium Storage peuvent prendre en charge jusqu’à 24 heures de rétention et 72 heures en cas de stockage standard.
5. Dans **Fréquence des captures instantanées de cohérence d’application**, spécifiez la fréquence de création des points de récupération contenant des captures instantanées cohérentes avec les applications (en minutes). Cliquez sur **OK** pour créer la stratégie.

    ![Stratégie de réplication](./media/physical-walkthrough-replication/gs-replication2.png)
8. Lorsque vous créez une stratégie, cette dernière est automatiquement associée au serveur de configuration. Par défaut, une stratégie de correspondance est automatiquement créée pour la restauration automatique. Par exemple, si la stratégie de réplication est **rep-policy**, la stratégie de restauration automatique correspond alors à **rep-policy-failback**. Cette stratégie n’est utilisée qu’à partir du moment où vous initiez une restauration automatique à partir d’Azure.

## <a name="next-steps"></a>Étapes suivantes

Aller à l’[Étape 9 : Installer le service Mobilité](physical-walkthrough-install-mobility.md)

