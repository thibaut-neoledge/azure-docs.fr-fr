---
title: "Restaurer une base de données SQL Azure à un point antérieur dans le temps | Microsoft Docs"
description: "Restaurer une base de données SQL Azure à un point antérieur dans le temps"
services: sql-database
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
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restaurer une base de données SQL Azure à un point antérieur dans le temps 

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md). 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Restaurer à un point antérieur dans le temps à l’aide du portail Azure

> [!TIP]
> Pour obtenir un didacticiel, consultez [Prise en main des fonctionnalités de sauvegarde et de restauration pour la protection et la récupération des données](sql-database-get-started-backup-recovery.md)
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

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Restaurer à un point antérieur dans le temps à l’aide de PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Restaurer votre base de données à un point dans le temps en tant base de données unique
1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaurez une version donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Restaurer votre base de données à un point dans le temps dans un pool élastique
1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaurez une version donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour visualiser le point de restauration le plus ancien à partir des sauvegardes d’une base de données générées par le service, consultez [Afficher le point de restauration le plus ancien](sql-database-view-oldest-restore-point.md)
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


