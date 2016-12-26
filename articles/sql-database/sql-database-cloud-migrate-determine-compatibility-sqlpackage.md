---
title: "Déterminer la compatibilité de SQL Database à l’aide de SqlPackage.exe | Microsoft Docs"
description: "Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: e8bb9e5a02a7caf95dae0101c720abac1c2deff3
ms.openlocfilehash: a3e43c6cb8e26daf7359f935816648bf498407ec


---
# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe
> [!div class="op_single_selector"]
> * [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
> * [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
> * [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
> * [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
> 

Cet article explique comment déterminer si une base de données SQL Server est compatible pour la migration vers Base de données SQL à l’aide de l’utilitaire d’invite de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) .

## <a name="using-sqlpackageexe"></a>Avec SqlPackage.exe
1. Ouvrez une invite de commandes et modifiez le répertoire contenant la version la plus récente de sqlpackage.exe. Cet utilitaire est fourni avec les dernières versions de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et de [SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Vous pouvez également télécharger la dernière version de [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) directement à partir du Centre de téléchargement Microsoft.
2. Exécutez la commande SqlPackage suivante avec les arguments ci-dessous pour votre environnement :

```   
    sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1
```   
   | Argument | Description |
   | --- | --- |
   | < nom_serveur > |nom du serveur source |
   | < nom_base_de_données > |nom de la base de données source |
   | < fichier_cible > |nom de fichier et emplacement du fichier BACPAC |
   | < nom_schéma.nom_table > |tables pour lesquelles les données sont générées dans le fichier cible |
   | < fichier_sortie > |nom de fichier et emplacement du fichier de sortie avec des erreurs, le cas échéant |
   
    The reason for the /p:TableName argument is that we only want to test for database compatibility for export to Azure SQL DB V12 rather than export the data from all tables. Unfortunately, the export argument for sqlpackage.exe does not support extracting zero tables. You need to specify at least one table, such as a single small table. The < output_file > contains the report of any errors. The "> 2>&1" string pipes both the standard output and the standard error resulting from the command execution to specified output file.
   
    ![Export a data-tier application from the Tasks menu](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)
3. Ouvrez le fichier de sortie et passez en revue les erreurs de compatibilité, le cas échéant. 
   
    ![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Étapes suivantes
* [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
  [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
* [Résoudre les problèmes de compatibilité de migration de la base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
* [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Fonctionnalités SQL Database](sql-database-features.md)
* [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
* [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)




<!--HONumber=Nov16_HO4-->


