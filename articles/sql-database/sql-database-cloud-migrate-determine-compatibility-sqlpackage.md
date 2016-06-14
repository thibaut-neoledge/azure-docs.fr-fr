<properties
   pageTitle="Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Conseiller de mise à niveau](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Dans cet article, vous découvrirez comment déterminer si une base de données SQL Server est compatible pour la migration vers Base de données SQL grâce à l’utilitaire d’invite de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx).

## Avec SqlPackage.exe

1. Ouvrez une invite de commandes et modifiez le répertoire contenant la version la plus récente de sqlpackage.exe. Cet utilitaire est fourni avec Visual Studio et SQL Server. Téléchargez le [version la plus récente de SQL Server Data Tools pour Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) pour obtenir la dernière version de l’utilitaire SqlPackage.
2. Exécutez la commande SqlPackage suivante avec les arguments ci-dessous pour votre environnement :

	’sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1’

	| Argument | Description |
	|---|---|
	| < server_name > | nom du serveur source |
	| < database_name > | nom de la base de données source |
	| < target_file > | nom de fichier et emplacement du fichier BACPAC |
	| < schema_name.table_name > | tables pour lesquelles les données seront générées dans le fichier cible |
	| < output_file > | nom de fichier et emplacement du fichier de sortie avec des erreurs, le cas échéant |

	L’argument /p:TableName est utilisé afin de tester uniquement la compatibilité de base de données en vue d’une exportation vers Azure SQL DB V12. Nous ne cherchons pas en effet à exporter les données de toutes les tables. Malheureusement, l’argument d’exportation de sqlpackage.exe ne permet pas de n’extraire aucune table. Vous devrez donc spécifier une petite table. Le fichier < output_file > contiendra le rapport d’erreurs. La chaîne "> 2>&1" dirige le résultat standard et l'erreur standard générés par l'exécution de la commande vers le fichier de sortie spécifié.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Ouvrez le fichier de sortie et passez en revue les erreurs de compatibilité, le cas échéant.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Étapes suivantes

- [Dernière version de SSDT](https://msdn.microsoft.com/library/mt204009.aspx) [Dernière version de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Résoudre les problèmes de compatibilité de migration de la base de données](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrez une base de données SQL Server compatible vers une base de données SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## En savoir plus

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->