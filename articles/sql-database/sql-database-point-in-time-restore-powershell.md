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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-point-in-time-restore.md)
- [Portail Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article explique comment restaurer votre base de données à un point antérieur dans le temps à partir de [sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md) à l’aide de PowerShell.

[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Restaurer votre base de données à un point dans le temps en tant base de données autonome

1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurez une version donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Restaurer votre base de données à un point dans le temps dans un pool de base de données élastique
   
1. Obtenez la base de données que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaurez une version donnée de la base de données à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la récupération à un point dans le temps à l’aide du portail Azure, consultez [Limite de restauration dans le temps à l’aide du portail Azure](sql-database-point-in-time-restore-portal.md).
- Pour obtenir des informations sur la récupération à un point dans le temps à l’aide de l’API REST, consultez [Limite de restauration dans le temps à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Pour une vue d’ensemble de la limite de restauration dans le temps, consultez [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- Pour une discussion complète sur la récupération après une erreur utilisateur ou une erreur d’application, consultez [Récupération d’erreur utilisateur](sql-database-user-error-recovery.md).

## Ressources supplémentaires

- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->