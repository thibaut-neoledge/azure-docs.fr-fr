<properties 
    pageTitle="Restaurer une base de données SQL Azure à un point antérieur dans le temps (PowerShell) | Microsoft Azure" 
    description="Restaurer une base de données SQL Azure à un point antérieur dans le temps" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) à l’aide de PowerShell.

[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Restaurer votre base de données à un point dans le temps en tant base de données autonome

1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurez une version antérieure donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Restaurer votre base de données à un point dans le temps dans un pool de base de données élastique
   
1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurez une version antérieure donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Étapes suivantes

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Restauration dans le temps](sql-database-point-in-time-restore.md)
- [Limite de restauration dans le temps à l’aide du portail Azure](sql-database-point-in-time-restore-portal.md)
- [Limite de restauration dans le temps à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)

## Ressources supplémentaires

- [restauration d’une base de données supprimée.](sql-database-restore-deleted-database.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->