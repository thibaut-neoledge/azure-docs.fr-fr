<properties
   pageTitle="Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage"
   description="Base de données SQL Microsoft Azure, migration de base de données, importer une base de données, importer un fichier BACPAC, sqlpackage"
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
   ms.date="12/17/2015"
   ms.author="carlrab"/>

# Importation vers Base de données SQL à partir d’un fichier BACPAC à l’aide de SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Cet article explique comment effectuer une importation vers Base de données SQL à partir d’un fichier [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4), à l’aide de l’utilitaire de ligne de commande [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà configuré un serveur de Base de données SQL, que vous disposez des informations de connexion et que vous avez vérifié la compatibilité de votre base de données source.

## Importer un fichier BACPAC dans une base de données SQL Azure à l’aide de SqlPackage

Procédez comme suit pour importer une base de données SQL Server (ou Base de données SQL Azure) compatible à partir d’un fichier BACPAC à l’aide de l’utilitaire de ligne de commande [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx).

> [AZURE.NOTE]Les étapes ci-dessous supposent que vous avez déjà configuré un serveur de base de données SQL Azure et que vous disposez des informations de connexion.

1. Ouvrez une invite de commandes et modifiez un répertoire contenant l’utilitaire de ligne de commande sqlpackage.exe Cet utilitaire est fourni avec Visual Studio et SQL Server.
2. Exécutez la commande sqlpackage.exe suivante avec les arguments ci-dessous pour votre environnement :

	’sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Argument | Description |
	|---|---|
	| < server_name > | nom du serveur cible |
	| < database_name > | nom de la base de données cible |
	| < user_name > | nom d'utilisateur sur le serveur cible |
	| < password > | mot de passe de l'utilisateur |
	| < source_file > | nom de fichier et emplacement du fichier BACPAC en cours d'importation |

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

<!---HONumber=AcomDC_1223_2015-->