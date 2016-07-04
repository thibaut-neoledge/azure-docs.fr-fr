<properties 
    pageTitle="Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante (PowerShell) | Microsoft Azure" 
    description="Restaurer une base de données SQL Azure sur un nouveau serveur à partir d’une sauvegarde géo-redondante" 
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

# Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell


> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-geo-restore.md)
- [Portail Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration en utilisant PowerShell.

[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

## Géo-restaurer votre base de données dans une base de données autonome

1. Récupérez la sauvegarde géo-redondante de la base de données à restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrez la restauration à partir de la sauvegarde géo-redondante à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## Géo-restaurer votre base de données dans un pool de base de données élastique

1. Récupérez la sauvegarde géo-redondante de la base de données à restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrez la restauration à partir de la sauvegarde géo-redondante à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx). Spécifiez le nom du pool dans lequel vous voulez restaurer votre base de données.
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données SQL Azure à l’aide du portail Azure à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide du portail Azure](sql-database-geo-restore-portal.md)
- Pour obtenir des informations détaillées concernant la restauration d’une base de données SQL Azure à partir d’une sauvegarde géo-redondante, consultez [Géo-restauration à l’aide de PowerShell](sql-database-geo-restore.md)
- Pour une discussion complète sur la récupération après une panne, consultez [Récupération après une panne](sql-database-disaster-recovery.md)


## Ressources supplémentaires

- [Scénarios de continuité des activités](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->