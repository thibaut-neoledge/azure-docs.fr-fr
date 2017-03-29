---
title: "SqlPackage : exportation d’une base de données SQL Server vers un fichier BACPAC (Azure) | Microsoft Docs"
description: "Cet article explique la procédure d’exportation d’une base de données SQL Server à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande SqlPackage."
keywords: "Base de données SQL Microsoft Azure, migration de base de données, exporter une base de données, exporter un fichier BACPAC, sqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 7b9541c5-5590-4c70-ad36-73007389f6dc
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: a17bfa6868ac581dc5ae562b3174f28571d52698
ms.lasthandoff: 02/11/2017


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exporter une base de données SQL Azure ou une base de données SQL Server dans un fichier BACPAC à l’aide de SqlPackage

Cet article explique comment exporter une base de données SQL Azure ou une base de données SQL Server dans un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.

Pour une vue d’ensemble de l’exportation dans un fichier BACPAC, consultez [Export to a BACPAC](sql-database-export.md) (Exporter dans un fichier BACPAC).

> [!NOTE]
> Vous pouvez également exporter votre fichier de base de données SQL Azure dans un fichier BACPAC à l’aide du [portail Azure](sql-database-export-portal.md), de [SQL Server Management Studio](sql-database-export-ssms.md) ou de [PowerShell](sql-database-export-powershell.md).
>

## <a name="sqlpackage-utility"></a>Utilitaire SQLPackage

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe. Cet utilitaire est fourni avec Visual Studio et SQL Server. Utilisez la fonction de recherche de votre ordinateur pour rechercher le chemin d’accès dans votre environnement.
2. Exécutez la commande sqlpackage.exe suivante avec les arguments ci-dessous pour votre environnement :
   
```    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >
```
   
   | Argument | Description |
   | --- | --- |
   | < server_name > |source server name |
   | < database_name > |source database name |
   | < target_file > |file name and location for BACPAC file |
   
   ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Next steps

* To learn about importing a BACPAC using SQLPackage, see [Import a BACPAC to Azure SQL Database using SqlPackage](sql-database-import-sqlpackage.md)
* To learn about importing a BACPAC using the Azure portal, see [Import a BACPAC to Azure SQL Database using the Azure portal](sql-database-import-portal.md)
* To learn about importing a BACPAC using PowerShell, see [Import a BACPAC to Azure SQL Database using PowerShell](scripts/sql-database-import-from-bacpac-powershell.md)
* For a discussion of the entire SQL Server database migration process, including performance recommendations, see [Migrate a SQL Server database to Azure SQL Database](sql-database-cloud-migrate.md).
* To learn about long-term backup retention of an Azure SQL database backup as an alternative to exported a database for archive purposes, see [Long term backup retention](sql-database-long-term-retention.md)
* To learn about importing a BACPAC to a SQL Server database, see [Import a BACPCAC to a SQL Server database](https://msdn.microsoft.com/library/hh710052.aspx)



