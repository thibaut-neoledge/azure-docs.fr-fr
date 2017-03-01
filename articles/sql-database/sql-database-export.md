---
title: "Exporter une base de données SQL Azure dans un fichier BACPAC | Microsoft Docs"
description: "Exporter une base de données SQL Azure dans un fichier BACPAC à l’aide du Portail Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 41d63a97-37db-4e40-b652-77c2fd1c09b7
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2461f706f8fc1150e69312098640c0676206a531
ms.openlocfilehash: 4edd728fe4798450106a4991b353b9dac9de0d88
ms.lasthandoff: 02/17/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file"></a>Exporter une base de données SQL Azure ou une base de données SQL Server dans un fichier BACPAC

Cet article décrit l’exportation de votre base de données SQL Azure ou d’une base de données SQL Server dans un fichier BACPAC. 

> [!IMPORTANT]
> La fonctionnalité Automatiser l’exportation Azure SQL Database est maintenant disponible en version préliminaire et sera supprimée le 1er mars 2017. À partir du 1er décembre 2016, vous ne pourrez plus configurer l’exportation automatisée sur une base de données SQL. Tous vos travaux d’exportation automatisée existants continueront à fonctionner jusqu’au 1er mars 2017. Après le 1er décembre 2016, vous pouvez utiliser [Rétention des sauvegardes à long terme](sql-database-long-term-retention.md) ou [Azure Automation](../automation/automation-intro.md) pour archiver des bases de données SQL régulièrement à l’aide de PowerShell en fonction d’une planification de votre choix. Pour obtenir un exemple de script, vous pouvez télécharger [l’exemple de script sur GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export). 
>

## <a name="overview"></a>Vue d'ensemble

Lorsque vous avez besoin d’exporter une base de données à des fins d’archivage ou de migration vers une autre plate-forme, vous pouvez exporter le schéma de base de données et les données dans un fichier BACPAC. Un fichier BACPAC est un fichier ZIP avec l’extension BACPAC. Il peut être stocké ultérieurement dans Azure Blob Storage ou un stockage local dans un emplacement local, puis importé dans Azure SQL Database ou une installation locale SQL Server. 

* Vous pouvez exporter votre base de données SQL Azure à l’aide du [portail Azure](sql-database-export-portal.md), de [PowerShell](sql-database-export-powershell.md), de [SQLPackage](sql-database-export-sqlpackage.md) ou de [SQL Server Management Studio](sql-database-export-ssms.md).
* Vous pouvez exporter une base de données SQL Server à l’aide de [PowerShell](sql-database-export-powershell.md), [SQLPackage](sql-database-export-sqlpackage.md) ou [SQL Server Management Studio](sql-database-export-ssms.md).

> [!IMPORTANT]
> Si vous exportez depuis SQL Server avant de procéder à la migration vers Azure SQL Database, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
> 

## <a name="considerations"></a>Considérations

* Pour qu’un export soit cohérent au niveau transactionnel, vous devez vérifier qu’aucune activité d’écriture n’a lieu lors de l’exportation ou effectuer une exportation à partir d’une [copie cohérente au niveau transactionnel](sql-database-copy.md) de votre base de données SQL Azure.
* Si vous exportez dans le stockage Blob, la taille maximale d’un fichier BACPAC est de 200 Go. Pour archiver un fichier BACPAC plus volumineux, exportez dans le stockage local.
* L’exportation d’un fichier BACPAC dans le stockage Premium Azure en utilisant les méthodes décrites dans cet article n’est pas prise en charge.
* Si l’opération d’exportation depuis Azure SQL Database dure plus de 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
  * Augmentez temporairement votre niveau de service.
  * Interrompez toutes les activités de lecture et d’écriture lors de l’exportation.
  * Utilisez un [index cluster](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non nulles sur toutes les tables de grande taille. Sans index cluster, une exportation peut échouer si elle dure plus de 6 à 12 heures. Cela est dû au fait que les services d’exportation doivent effectuer une analyse complète de la table pour tenter de l’exporter en entier. Une bonne méthode pour déterminer si vos tables sont optimisées pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et à vérifier que *RANGE_HI_KEY* n’est pas nul et que sa valeur a une bonne distribution. Pour plus d’informations, consultez [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> Les BACPAC ne sont pas conçus pour être utilisés pour les opérations de sauvegarde et de restauration. La base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, consultez [Vue d’ensemble de la continuité de l’activité avec la base de données SQL Azure](sql-database-business-continuity.md) et [Découvrir les sauvegardes SQL Database](sql-database-automated-backups.md).  
> 


## <a name="next-steps"></a>Étapes suivantes

* Pour une description du processus complet de migration d’une base de données SQL Server, consultez [Migration de base de données SQL Server vers SQL Database dans le cloud](sql-database-cloud-migrate.md).
* Pour une vue d’ensemble de la copie d’une base de données dans Azure, consultez également [Copie d’une base de données SQL Azure](sql-database-copy.md).
* Vous pouvez copier votre base de données SQL Azure à l’aide du [portail Azure](sql-database-copy-portal.md), de [PowerShell](sql-database-copy-powershell.md) ou de [Transact-SQL](sql-database-copy-transact-sql.md). 

