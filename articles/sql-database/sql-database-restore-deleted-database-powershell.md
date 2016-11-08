---
title: Restaurer une base de données SQL supprimée (PowerShell) | Microsoft Docs
description: Restaurez une base de données SQL Azure supprimée (PowerShell).
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Restaurer une base de données SQL Azure supprimée avec PowerShell
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-recovery-using-backups.md)
> * [Restauration d’une base de données supprimée : portail Azure](sql-database-restore-deleted-database-portal.md)
> 
> 

[!INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

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
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md)
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md)
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md)
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md)

<!---HONumber=AcomDC_0803_2016-->