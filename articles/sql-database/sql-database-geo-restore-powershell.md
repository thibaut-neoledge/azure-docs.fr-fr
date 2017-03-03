---
title: "PowerShell : Restauration d’une base de données Azure SQL Database à partir d’une sauvegarde géo-redondante | Microsoft Docs"
description: "Restaurer une base de données SQL Azure sur un nouveau serveur à partir d’une sauvegarde géo-redondante avec PowerShell"
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
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 65ae5784e2d4cadc9e5dc348d9bb0d696a5f4908
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-powershell"></a>Restauration d’une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell

Cet article vous explique comment restaurer votre base de données sur un nouveau serveur à l’aide de la géo-restauration avec PowerShell. Cette tâche peut également être accomplie [à l’aide du portail Azure](sql-database-geo-restore-portal.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurer une base de données SQL Azure à partir d’une sauvegarde géo-redondante à l’aide de PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>Géo-restaurer votre base de données dans une base de données autonome

1. Récupérez la sauvegarde géo-redondante de la base de données à restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Démarrez la restauration à partir de la sauvegarde géo-redondante à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>Géo-restaurer votre base de données dans un pool élastique

1. Récupérez la sauvegarde géo-redondante de la base de données à restaurer à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Démarrez la restauration à partir de la sauvegarde géo-redondante à l’aide de l’applet de commande [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Spécifiez le nom du pool dans lequel vous voulez restaurer votre base de données.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble de la continuité des activités et des scénarios, consultez [Vue d’ensemble de la continuité des activités](sql-database-business-continuity.md).
* Pour en savoir plus sur les sauvegardes automatisées Azure SQL Database, consultez [Sauvegardes automatisées d’une base de données SQL](sql-database-automated-backups.md).
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, consultez [Restaurer une base de données à partir des sauvegardes initiées par le service](sql-database-recovery-using-backups.md).
* Pour en savoir plus sur les options de récupération plus rapides, consultez [Géo-réplication active](sql-database-geo-replication-overview.md).  
* Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour l’archivage, consultez [Copie de base de données](sql-database-copy.md).


