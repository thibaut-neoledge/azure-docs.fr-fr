<properties
	pageTitle="Prise en main des requêtes de bases de données croisées (partitionnement vertical) | Microsoft Azure"	
	description="comment utiliser une requête de base de données élastique avec des bases de données partitionnées verticalement"
	services="sql-database"
	documentationCenter=""  
	manager="jhubbard"
	authors="torsteng"/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="torsteng" />

# Prise en main des requêtes de bases de données croisées (partitionnement vertical) (version préliminaire)

Les requêtes de bases de données élastiques (en version préliminaire) pour base de données SQL Azure vous permettent d’exécuter des requêtes T-SQL qui s’étendent sur plusieurs bases de données via un seul point de connexion. Cette rubrique s'applique aux [bases de données partitionnées verticalement](sql-database-elastic-query-vertical-partitioning.md).

À l’issue du processus, vous serez capable de configurer et d’utiliser une base de données SQL Azure pour exécuter des requêtes qui s’étendent sur plusieurs bases de données associées.

Pour plus d’informations sur la fonctionnalité de requête de base de données élastique, veuillez consulter la [Vue d’ensemble de la requête de base de données élastique Azure SQL Database](sql-database-elastic-query-overview.md).

## Créer les exemples de bases de données

Pour commencer, nous devons créer deux bases de données, **Customers** et **Orders**, dans les mêmes serveurs logiques ou non.

Exécutez les requêtes suivantes sur la base de données **Orders** pour créer la table **OrderInformation** et ajouter les exemples de données.

	CREATE TABLE [dbo].[OrderInformation]( 
		[OrderID] [int] NOT NULL, 
		[CustomerID] [int] NOT NULL 
		) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
	INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Exécutez maintenant la requête suivante sur la base de données **Customers** pour créer la table **CustomerInformation** et ajouter les exemples de données.

	CREATE TABLE [dbo].[CustomerInformation]( 
		[CustomerID] [int] NOT NULL, 
		[CustomerName] [varchar](50) NULL, 
		[Company] [varchar](50) NULL 
		CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
	) 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
	INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## Créez des objets de base de données
### Clé principale et informations d’identification de la base de données

1. Ouvrez SQL Server Management Studio ou SQL Server Data Tools dans Visual Studio.
2. Connectez-vous à la base de données Orders et exécutez les commandes T-SQL suivantes :

		CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
		CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
		WITH IDENTITY = '<username>', 
		SECRET = '<password>';  

	Le « username » et le « password » doivent être le nom d'utilisateur et le mot de passe de connexion à la base de données Customers.

### Sources de données externes
Pour créer une source de données externe, exécutez la commande suivante sur la base de données Orders :

	CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
		(TYPE = RDBMS, 
		LOCATION = '<server_name>.database.windows.net', 
		DATABASE_NAME = 'Customers', 
		CREDENTIAL = ElasticDBQueryCred, 
	) ;

### Tables externes
Créez une table externe sur la base de données Orders qui corresponde à la définition de la table CustomerInformation :

	CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
	( [CustomerID] [int] NOT NULL, 
	  [CustomerName] [varchar](50) NOT NULL, 
	  [Company] [varchar](50) NOT NULL) 
	WITH 
	( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## Exécutez un exemple de requête T-SQL de base de données élastique

Après avoir défini vos tables externes et votre source de données externe, vous pouvez utiliser T-SQL pour exécuter des requêtes sur vos tables externes. Exécutez cette requête sur la base de données Orders :

	SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
	FROM OrderInformation 
	INNER JOIN CustomerInformation 
	ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## Coût

Actuellement, la fonctionnalité de requête de base de données élastique est incluse dans le coût de votre base de données SQL Azure.

Pour plus d’informations sur la tarification, voir [Tarification des bases de données SQL](/pricing/details/sql-database).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->

<!---HONumber=AcomDC_0601_2016-->