---
title: "SQLPackage : Compatibilité des bases de données Azure SQL Server | Microsoft Docs"
description: "Cet article explique comment déterminer si une base de données SQL Server est compatible pour la migration vers SQL Database à l’aide de SqlPackage."
keywords: "Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ebe2cf3e-9561-4ede-8fb9-f3e6ce3fb7a6
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 11/08/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6728693290e2c1d6c970c60c7d8ef674764e053c
ms.openlocfilehash: 834dae604fa87f753e8d050ce7cdfa7e9613f462


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
   
    L’argument /p:TableData est utilisé afin de tester uniquement la compatibilité de base de données en vue d’une exportation vers Azure SQL DB V12. Nous ne cherchons pas en effet à exporter les données de toutes les tables. Malheureusement, l’argument d’exportation de sqlpackage.exe ne prend pas en charge l’extraction de zéro table. Vous devez spécifier au moins une table, telle une petite table simple. Le fichier < fichier_sortie > contient le rapport d’erreurs. La chaîne « >&2;>&1 » dirige le résultat standard et l’erreur standard générés par l’exécution de la commande vers le fichier de sortie spécifié.
   
    ![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

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




<!--HONumber=Jan17_HO2-->


