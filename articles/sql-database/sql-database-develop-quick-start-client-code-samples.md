<properties 
	pageTitle="Exemples de code de démarrage rapide sur SQL Database | Microsoft Azure" 
	description="Fournit des exemples de code et des pilotes pour Node.js sur Linux, Python sur Mac OS, Java et Windows et bien d’autres clients de base de données SQL Azure."
	services="sql-database" 
	documentationCenter="" 
	authors="annemill" 
	manager="jhubbard" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/10/2016" 
	ms.author="annemill"/>


# Exemples de code de démarrage rapide client pour SQL Database


Cette rubrique fournit des liens vers des échantillons de code de démarrage rapide que vous pouvez utiliser pour vous connecter à SQL Azure Database.


- Exemples courts de connexion et de requête.
- Exemples de nouvelle tentative de connexion et de requête, mais, si une erreur a été rencontrée, une tentative automatique est considérée comme une [*erreur temporaire*](sql-database-develop-error-messages.md#bkmk_connection_errors) (délai de connexion, par exemple).


Les exemples regroupent :


- Une variété de langages de programmation.
- Windows, Linux et Mac OS comme systèmes d’exploitation de votre programme client.
- Liens pour les téléchargements des pilotes de connexion nécessaires.
- Des exemples de code de démarrage rapide.
- Exemples plus longs qui contiennent une gestion des erreurs temporaires sous la forme d'une logique de nouvelle tentative automatique.
- Échantillons de code qui convertissent des jeux de résultats relationnels en format orienté objet.


> [AZURE.NOTE] Des exemples de code pour davantage de langages sont en cours de préparation et des liens vers ceux-ci seront ajoutés à cette rubrique.


## Clients sous Linux


Cette section fournit des liens vers des rubriques d'échantillons de code pour les programmes clients qui s'exécutent sous Linux.


| Langage | Exemple court | Exemple de nouvelle tentative | Relationnel à l'objet |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS, pymssql](sql-database-develop-python-simple-ubuntu-linux.md) | . | . |
| Ruby | [FreeTDS, TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Clients sous Mac OS


Cette section fournit des liens vers des rubriques d’exemples de code pour les programmes clients qui s’exécutent sous Mac OS.


| Langage | Exemple court | Exemple de nouvelle tentative | Relationnel à l'objet |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS, TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Clients sous Windows


Cette section fournit des liens vers des rubriques d'échantillons de code pour les programmes clients qui s'exécutent sous Windows.


| Langage | Exemple court | Exemple de nouvelle tentative | Relationnel à l'objet |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET personnalisé](sql-database-develop-csharp-retry-windows.md)<br/><br/>[Enterprise Library](sql-database-develop-entlib-csharp-retry-windows.md) | (Entity Framework) |
| C++ | [Pilote ODBC](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java. JDBC, JDK. Insert, Transaction, Select.](sql-database-develop-java-simple-windows.md) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC personnalisé](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## Voir aussi


- [Téléchargements pour les Kits de développement logiciel (SDK) et les outils, pour de nombreux langages et plateformes](https://azure.microsoft.com/downloads/#cmd-line-tools)
- [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md)
- [Liste de codes numériques des erreurs temporaires](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>&nbsp;
- [Développement de base de données SQL Azure : rubriques Procédures](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [Connexion à la base de données SQL : liens, meilleures pratiques et règles de conception](sql-database-connect-central-recommendations.md)
- [Créer votre première base de données SQL Azure](sql-database-get-started.md)
- [Entity Framework 6 ici, EF 7 sur GitHub](http://entityframework.codeplex.com/)

<!---HONumber=AcomDC_0420_2016-->