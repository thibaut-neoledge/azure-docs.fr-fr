---
title: "Portail Azure : restauration d’une base de données SQL Azure à un point dans le temps | Microsoft Docs"
description: "Restauration d’une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure"
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restauration d’une base de données SQL Azure à un point antérieur dans le temps avec le portail Azure

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md). avec PowerShell. Cette tâche peut également être effectuée [avec PowerShell](sql-database-point-in-time-restore-powershell.md).

## <a name="restore-to-a-previous-point-in-time"></a>Restaurer une version antérieure 

> [!TIP]
> Pour obtenir un didacticiel, consultez [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery-portal.md)
>

Sélectionnez une base de données à restaurer dans le portail Azure :

1. Ouvrez le [portail Azure](https://portal.azure.com).
2. Sur le côté gauche de l’écran, sélectionnez **Autres services** > **Bases de données SQL**.
3. Cliquez sur la base de données à restaurer.
4. Dans la partie supérieure de la page de votre base de données, sélectionnez **Restaurer** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Dans la page **Restaurer**, sélectionnez la date et l’heure (au format UTC) auxquelles restaurer la base de données, puis cliquez sur **OK** :
   
   ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Une fois que vous avez cliqué sur **OK** à l’étape précédente, cliquez sur l’icône de notification dans l’angle supérieur droit de la page, puis cliquez sur la notification **Restauration de la base de données SQL** pour plus d’informations.
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. La page Restauration de la base de données SQL s’ouvre avec les informations sur l’état de la restauration. Vous pouvez cliquer sur la ligne pour plus de détails :
   
    ![Restaurer une base de données SQL Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)


