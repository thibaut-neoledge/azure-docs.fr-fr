---
title: Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante (PowerShell) | Microsoft Docs
description: Restaurer une base de données SQL Azure sur un nouveau serveur à partir d’une sauvegarde géo-redondante
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein

---
# Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Vue d'ensemble](sql-database-recovery-using-backups.md)
> * [Restauration géographique : portail Azure](sql-database-geo-restore-portal.md)
> 
> 

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration. Cela est possible via PowerShell.

[!INCLUDE [Démarrer votre session PowerShell](../../includes/sql-database-powershell.md)]

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
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées d’une base de données SQL Azure, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md).

<!---HONumber=AcomDC_0803_2016-->