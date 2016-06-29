<properties
   pageTitle="Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage | Microsoft Azure"
   description="Base de données SQL Microsoft Azure, migration de base de données, exporter une base de données, exporter un fichier BACPAC, sqlPackage"
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
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Cet article explique comment exporter une base de données SQL Server à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4), à l’aide de l’utilitaire d’invite de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe Cet utilitaire est fourni avec Visual Studio et SQL Server. Utilisez la fonction de recherche de votre ordinateur pour rechercher le chemin d’accès dans votre environnement.
2. Exécutez la commande sqlpackage.exe suivante avec les arguments ci-dessous pour votre environnement :

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argument | Description |
	|---|---|
	| < server_name > | nom du serveur source |
	| < database_name > | nom de la base de données source |
	| < target_file > | nom de fichier et emplacement du fichier BACPAC |

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Étapes suivantes

- [Version la plus récente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Version la plus récente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide du portail Azure](sql-database-import.md)
- [Importer un fichier BACPAC vers une base de données SQL Azure à l’aide de PowerShell](sql-database-import-powershell.md)

## Ressources supplémentaires

- [Base de données SQL V12](sql-database-v12-whats-new.md)
- [Fonctions partiellement ou non prises en charge de Transact-SQL](sql-database-transact-sql-information.md)
- [Migration de bases de données non-SQL Server avec l’Assistant Migration SQL Server](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0615_2016-->