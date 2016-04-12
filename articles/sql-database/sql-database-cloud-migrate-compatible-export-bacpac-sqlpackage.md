<properties
   pageTitle="Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage"
   description="Base de données SQL Microsoft Azure, migration de base de données, exporter une base de données, exporter un fichier BACPAC, sqlPackage"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Exporter une base de données SQL Server vers un fichier BACPAC à l’aide de SqlPackage

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

## Étape suivante : importation dans Base de données SQL à partir d’un fichier BACPAC

- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portail Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

<!---HONumber=AcomDC_0316_2016-->