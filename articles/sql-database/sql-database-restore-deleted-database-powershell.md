<properties
	pageTitle="Restaurer une base de données SQL supprimée (PowerShell) | Microsoft Azure"
	description="Restaurez une base de données SQL Azure supprimée (PowerShell)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurer une base de données SQL Azure supprimée avec PowerShell

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-database-restore-deleted-database.md)
- [Portail Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]


## Restaurer votre base de données supprimée vers une base de données autonome

1. Obtenez la sauvegarde de la base de données supprimée que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrez la restauration à partir de la sauvegarde de la base de données supprimée à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## Restaurer une base de données supprimée vers un pool élastique de bases de données

1. Obtenez la sauvegarde de la base de données supprimée que vous voulez restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Démarrez la restauration à partir de la sauvegarde de la base de données supprimée à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## Étapes suivantes

- Pour obtenir des instructions détaillées sur la restauration d’une base de données supprimée à l’aide du portail Azure, consultez [Restaurer une base de données supprimée à l’aide du portail Azure](sql-database-restore-deleted-database-portal.md).
- Pour obtenir des informations sur la restauration d’une base de données supprimée, consultez [Restaurer une base de données supprimée à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).
- Pour obtenir des informations détaillées sur la restauration d’une base de données supprimée, consultez [Restaurer une base de données supprimée](sql-database-restore-deleted-database.md)
- Pour plus d’informations concernant les sauvegardes automatisées de base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).

## Ressources supplémentaires

- [Limite de restauration dans le temps](sql-database-point-in-time-restore.md)
- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Restauration géographique](sql-database-geo-restore.md)
- [Géo-réplication active](sql-database-geo-replication-overview.md)
- [Conception d'applications pour la récupération d'urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->