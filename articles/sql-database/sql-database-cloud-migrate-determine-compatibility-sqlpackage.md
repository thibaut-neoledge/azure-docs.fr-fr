<properties
   pageTitle="Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe"
   description="Base de données SQL Microsoft Azure, migration de base de données, compatibilité Base de données SQL, SqlPackage"
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

# Déterminer la compatibilité de Base de données SQL à l’aide de SqlPackage.exe

> [AZURE.SELECTOR]
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)

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

	L’argument /p:TableName est utilisé afin de tester uniquement la compatibilité de base de données en vue d’une exportation vers Azure SQL DB V12. Nous ne cherchons pas en effet à exporter les données de toutes les tables. Malheureusement, l’argument d’exportation de sqlpackage.exe ne permet pas de n’extraire aucune table. Vous devrez donc spécifier une petite table. Le fichier < output_file > contiendra le rapport d’erreurs. La chaîne "> 2>&1" dirige le résultat standard et l'erreur standard générés par l'exécution de la commande vers le fichier de sortie spécifié.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Ouvrez le fichier de sortie et passez en revue les erreurs de compatibilité, le cas échéant.

	![Exporter une application de la couche Données à partir du menu Tâches](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Étape suivante : résoudre les problèmes de compatibilité, le cas échéant

[Résoudre les problèmes de compatibilité de base de données](sql-database-cloud-migrate-fix-compatibility-issues.md), le cas échéant.

<!---HONumber=AcomDC_0316_2016-->