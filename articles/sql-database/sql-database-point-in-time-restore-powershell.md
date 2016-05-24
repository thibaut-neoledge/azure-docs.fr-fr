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
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Restaurer une base de données SQL Azure à un point dans le temps avec PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Cet article explique comment restaurer votre base de données à un point dans le temps à l’aide de PowerShell.

[**La limite de restauration dans le temps**](sql-database-point-in-time-restore.md) est une fonctionnalité en libre-service qui vous permet de restaurer une base de données à partir des sauvegardes automatiques que nous effectuons pour toutes les bases de données à n’importe quel point de la période de rétention de votre base de données. Pour en savoir plus sur les périodes de rétention des bases de données et des sauvegardes automatiques, consultez la [vue d’ensemble de la continuité d’activité](sql-database-business-continuity.md).

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

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)


## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->