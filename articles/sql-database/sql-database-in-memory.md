<properties
	pageTitle="Prise en main de SQL In-Memory | Microsoft Azure"
	description="Les technologies SQL In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques. Apprenez à tirer parti de ces technologies."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Prise en main de In-Memory (version préliminaire)


Les technologies SQL In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques. In-Memory OLTP peut augmenter de débit des transactions jusqu’à 30 fois et In-Memory Analytics peut accélérer jusqu’à 100 fois les performances de requête, selon la charge de travail.

In-Memory Analytics est à la disposition générale. In-Memory OLTP est disponible en version préliminaire pour les bases de données SQL Azure Premium.


## Prise en main

Essayez In-Memory OLTP pour les charges de travail transactionnelles :


- [Enrichir une base de données AdventureWorksLT avec des exemples d’In-Memory OLTP](#Enrich-an-AdventureWorksLT-database-with-In-Memory-OLTP-samples).
- [Utiliser In-Memory OLTP dans une application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)
- [Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md).


Essayez In-Memory Analytics pour les charges de travail transactionnelles :

- [Installer In-Memory Analytics](#Install-the-In-Memory-Analytics-sample).
- En savoir plus sur les [index Columnstore](https://msdn.microsoft.com/library/gg492088.aspx) (MSDN).


## Enrichir une base de données AdventureWorksLT avec des exemples d’In-Memory OLTP.

Vous pouvez créer l’exemple de base de données AdventureWorksLT [V12] en quelques clics sur le portail Azure en version préliminaire. Ensuite, les étapes ci-après expliquent comment enrichir votre base de données AdventureWorksLT avec des tables et des procédures stockées compilées en mode natif pour illustrer les objets In-Memory OLTP.


1. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), créez une base de données Premium sur un serveur V12. Définissez comme valeur Source l’exemple de base de données AdventureWorksLT [V12].
 - Pour des instructions plus détaillées concernant cette étape, voir [cet article](sql-database-get-started.md).

2. Connectez-vous à la base de données avec [SQL Server Management Studio (SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) ou avec un utilitaire similaire.

3. Copiez le [script In-Memory OLTP](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) dans le Presse-papiers.
 - Le script crée les objets In-Memory nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.

4. Collez le script Transact-SQL dans SSMS.exe, puis exécutez-le.



&nbsp;

L’exemple contient les tables à mémoire optimisée suivantes :

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

Examinez les tables à mémoire optimisée via l’Explorateur d’objets ou des requêtes d’affichage de catalogue.

Exemple :


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


L’Explorateur d’objets ou des requêtes d’affichage de catalogue permettent également d’inspecter la procédure stockée compilée en mode natif SalesLT.usp\_InsertSalesOrder\_inmem.

Exemple :


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## Exécuter l’exemple de charge de travail

Utilisez les procédures stockées SalesLT.usp\_InsertSalesOrder\_inmem et SalesLT.usp\_InsertSalesOrder\_ondisk pour comparer les performances d’insertion des tables à mémoire optimisée à celles des tables sur disque.

Nous vous recommandons d’exécuter la charge de travail à l’aide de connexions clientes simultanées à partir d’une application résidant dans la même région Azure que l’exemple de base de données.

### Exemple d’insertion de commande client

Le script suivant insère un exemple de commande client de cinq lignes dans les tables à mémoire optimisée SalesLT.SalesOrderHeader\_inmem et SalesLT.SalesOrderDetail\_inmem :


```
		DECLARE
			@i int = 0,
			@od SalesLT.SalesOrderDetailType_inmem,
			@SalesOrderID int,
			@DueDate datetime2 = sysdatetime(),
			@CustomerID int = rand() * 8000,
			@BillToAddressID int = rand() * 10000,
			@ShipToAddressID int = rand() * 10000;

		INSERT INTO @od
		SELECT OrderQty, ProductID
		FROM Demo.DemoSalesOrderDetailSeed
		WHERE OrderID= cast((rand()*60) as int);

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### Exécution de l’exemple de charge de travail de stress

Pour exécuter un exemple de charge de travail de stress, créez une [machine virtuelle Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région que votre exemple de base de données. Servez-vous de l’outil en ligne de commande ostress pour exécuter la charge de travail. Pour obtenir des instructions concernant [l’installation et l’exécution d’ostress](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx), voir MSDN.

Lors de l’exécution à partir de l’invite de commandes RML, la commande suivante insère un million de commandes, de cinq lignes chacune, dans des tables à mémoire optimisée, à l’aide de 100 connexions simultanées :


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


Veillez à remplacer <servername> par le nom de votre serveur, <database> par le nom de votre base de données, et <login> et <password> par vos informations de connexion.

Pour comparer les performances d’insertion de tables à mémoire optimisée à celles de tables sur disque traditionnelles, utilisez la commande suivante pour insérer le même million de commandes dans les tables sur disque :


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


Les tests révèlent que les tables à mémoire optimisée fonctionnent 9 fois plus rapidement que les tables sur disque pour cette charge de travail, avec ostress s’exécutant sur une machine virtuelle dans la même région Azure que la base de données.

Après l’exécution de chaque test, veillez à réinitialiser l’exemple pour éviter de saturer l’espace de stockage en mémoire. Exécutez l’instruction T-SQL suivante dans votre base de données. Une seule exécution de test insérant un million de commandes génère plus de 500 Mo de données dans les tables à mémoire optimisée.


```
EXECUTE Demo.usp_DemoReset;
```


## Installer l’exemple In-Memory Analytics

**Premièrement**, créez une base de données SQL Azure.

- Choisissez n’importe quelle édition Premium (Premium est requis pour Columnstore).
- Veillez à créer la base de données à partir de l’exemple.

- Par souci de simplicité, nommez votre base de données AdventureworksLT.



**Ensuite**, connectez-vous à la base de données SQL Azure via [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), version préliminaire de septembre 2015 ou version ultérieure.


- Exécutez le script d’installation [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql).


- Créez le schéma requis pour les tables de faits et de dimension en exécutant le script. Le script crée 2 tables de faits d’environ 3,5 millions de lignes chacune.


- FactResellerSales\_CCI qui contient une table columnstore.


- FactResellerSalesXL\_PageCompressed qui est une table de type arbre B (B-tree) compressée en une page. **Remarque :** l’exécution de ce script pour générer les données peut prendre jusqu’à 15 minutes.


**Exécutez** les requêtes de démonstration figurant dans le fichier [clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) et découvrez la fonctionnalité.

## En savoir plus sur In-Memory OLTP

[In-Memory OLTP (optimisation en mémoire)](https://msdn.microsoft.com/library/dn133186.aspx)

Le [Livre blanc sur les modèles de charge de travail courants et autres considérations relatives à la migration](https://msdn.microsoft.com/library/dn673538.aspx) (en anglais) décrit des modèles de charge de travail où In-Memory OLTP apporte généralement des gains de performances significatifs.

## Considérations relatives à la version préliminaire

In-Memory OLTP est pris en charge **uniquement** pour les bases de données de l’édition Premium.

In-Memory OLTP est pris en charge uniquement dans de nouvelles bases de données. Il n’est pas pris en charge dans des bases de données créées à partir d’une base de données existante via la fonctionnalité de copie ou de restauration. En revanche, une fois la nouvelle base de données disponible, vous pouvez la copier ou la restaurer en conservant toutes les fonctionnalités d’In-Memory OLTP.

Pour vérifier si In-Memory OLTP est pris en charge dans une base de données, exécutez la requête suivante :


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


Si la requête retourne **1**, In-Memory OLTP est pris en charge dans cette base de données, ainsi que dans toute copie ou restauration de base de données créée à partir de cette base de données.

Si une base de données contient l’une des sortes d’objets ou de types suivants, la modification du niveau de service de la base de données en service de base ou standard n’est pas prise en charge. Pour passer à une version antérieure de la base de données, commencez par supprimer les objets.

- Tables à mémoire optimisée
- Types de tables à mémoire optimisée
- Modules compilés en mode natif. L’utilisation d’In-Memory OLTP avec des bases de données dans des pools élastiques n’est pas prise en charge.

L’utilisation d’In-Memory OLTP avec SQL Data Warehouse n’est pas prise en charge.

Le magasin de requêtes ne capture pas les requêtes à l’intérieur de modules compilés en mode natif.

Certaines fonctionnalités de Transact-SQL ne sont pas prises en charge avec In-Memory OLTP. Pour plus de détails, voir [Prise en charge de Transact-SQL pour In-Memory OLTP](https://msdn.microsoft.com/library/dn133180.aspx)

## Outils pris en charge

[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx), version préliminaire de septembre 2015 ou version ultérieure.

[SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx), version préliminaire de septembre 2015 ou version ultérieure.

## Ressources supplémentaires

En savoir plus sur In-Memory OLTP et In-Memory Analytics : [In-Memory OLTP (optimisation en mémoire)](https://msdn.microsoft.com/library/dn133186.aspx).

[Description des index columnstore](https://msdn.microsoft.com/library/gg492088.aspx) sur MSDN.

Le [Livre blanc sur les modèles de charge de travail courants et autres considérations relatives à la migration](https://msdn.microsoft.com/library/dn673538.aspx) (en anglais) décrit des modèles de charge de travail où In-Memory OLTP apporte généralement des gains de performances significatifs.

## Étapes suivantes

Essayer d’[utiliser In-Memory OLTP dans une application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)

[Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md) pour In-Memory OLTP.

<!---HONumber=Nov15_HO1-->