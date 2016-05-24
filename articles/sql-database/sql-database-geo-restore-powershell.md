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
    ms.workload="data-management" 
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration en utilisant PowerShell.

La [géo-restauration](sql-database-geo-restore.md) permet de restaurer une base de données à partir d’une sauvegarde géo-redondante utilisée pour créer une nouvelle base de données. La base de données peut être créée sur n’importe quel serveur dans n’importe quelle région Azure. Comme elle utilise une sauvegarde géo-redondante en tant que source, vous pouvez la mettre à profit pour récupérer une base de données même si celle-ci est inaccessible en raison d’une défaillance. La géo-restauration est automatiquement activée pour tous les niveaux de service, sans coûts supplémentaires.

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

- [Finaliser la base de données SQL Microsoft Azure restaurée](sql-database-recovered-finalize.md)
- [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md)


## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->