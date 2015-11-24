<properties
	pageTitle="Prise en main de SQL In-Memory | Microsoft Azure"
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
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# Prise en main de In-Memory (version préliminaire) dans la base de données SQL

Les tables SQL régulières sont stockées uniquement sur un disque dur. En utilisant les fonctionnalités In-Memory, vous pouvez améliorer une table de manière à ce qu’elle se trouve dans la mémoire active pendant que le serveur est en cours d’exécution.


Les technologies In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques.

- Avec In-Memory OLTP, vous pouvez multiplier jusqu’à 30 le débit des transactions, selon les caractéristiques de la charge de travail.
 - Tables à mémoire optimisée.
 - Procédures stockées compilées en mode natif.

- Avec In-Memory Analytics, vous pouvez bénéficier de requêtes jusqu’à 100 fois plus performantes.
 - Index columnstore.


Pour les [analyses en temps réel](http://msdn.microsoft.com/library/dn817827.aspx), vous pouvez combiner ces technologies pour obtenir :

- Une vue globale de l’entreprise en temps réel en fonction des données opérationnelles.
- Un traitement OLTP accéléré.


#### Availability


- *Disponibilité générale :* In-Memory Analytics est à la disposition générale.
- *Version préliminaire :*In-Memory OLTP et Real-Time Operational Analytics sont disponibles en version préliminaire.
 - Ces derniers sont disponibles uniquement pour les bases de données SQL Azure. [Premium](sql-database-service-tiers.md).


#### OLTP et Analytics

Les deux sections principales de cette rubrique sont les suivantes :

- A. In-Memory [OLTP](#install_oltp_manuallink), qui implique la lecture et l’écriture.
- B. In-Memory [Analytics](#install_analytics_manuallink),qui implique la lecture.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A. Installer l’exemple In-Memory OLTP

Vous pouvez créer l’exemple de base de données AdventureWorksLT [V12] en quelques clics sur le [portail Azure en version préliminaire](http://portal.azure.com/). Les étapes de cette section expliquent comment enrichir votre base de données AdventureWorksLT grâce aux éléments suivants :

- Tables In-Memory.
- Procédure stockée compilée en mode natif.


#### Procédure d’installation :

1. Dans le [portail Azure en version préliminaire](http://portal.azure.com/), créez une base de données Premium sur un serveur V12. Définissez comme valeur **Source** l’exemple de base de données AdventureWorksLT [V12].
 - Pour obtenir des instructions détaillées, voir [Créer votre première base de données SQL Azure](sql-database-get-started.md).

2. Connectez-vous à la base de données avec [SQL Server Management Studio (SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiez le [script In-Memory OLTP Transact-SQL](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql) dans le Presse-papiers.
 - Le script T-SQL crée les objets In-Memory nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.

4. Collez le script T-SQL dans SSMS, puis exécutez-le.
 - La clause `MEMORY_OPTIMIZED = ON` des instructions CREATE TABLE est essentielle, comme dans l’exemple ci-dessous :


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### Erreur 40536


Si vous obtenez une erreur 40536 lorsque vous exécutez le script T-SQL, exécutez le script T-SQL suivant pour vérifier que la base de données prend en charge In-Memory :


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un résultat de **0** signifie que In-Memory n’est pas pris en charge, et un résultat de 1 signifie qu’il l’est.


#### À propos des éléments créés à mémoire optimisée.

**Tables** : l’exemple contient les tables à mémoire optimisée suivantes :

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Vous pouvez inspecter les tables à mémoire optimisée via l’**Explorateur d’objets** dans SSMS en procédant de la façon suivante :

- Cliquez avec le bouton droit sur **Tables** > **Filtre** > **Paramètres de filtre** > **Est à mémoire optimisée** (égal à 1).


Vous pouvez aussi interroger les vues de catalogue, telles que :


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**Procédure stockée compilée en mode natif** : SalesLT.usp\_InsertSalesOrder\_inmem peut être inspecté via une requête de vue de catalogue :


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## Exécuter l’exemple de charge de travail OLTP

La seule différence entre les deux *procédures stockées* est que la première utilise les versions à mémoire optimisée des tables, tandis que la deuxième utilise les tables sur disque régulières :

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


Dans cette section, vous apprendrez à utiliser l’utilitaire **ostress.exe**, pratique pour exécuter les deux procédures stockées à des niveaux de contrainte élevée. Vous pouvez comparer le temps d’exécution des deux contraintes.


Lorsque vous exécutez ostress.exe, nous vous recommandons de transmettre des valeurs de paramètre conçues pour :

- Exécuter un grand nombre de connexions simultanées, en utilisant par exemple -n100.
- Répéter chaque boucle de connexion une centaine de fois, en utilisant par exemple -r500.


Toutefois, vous pouvez commencer avec des valeurs plus petites, telles que -n10 et -r50 pour vous assurer que tout fonctionne.


### Script pour ostress.exe


Cette section affiche le script T-SQL incorporé à la ligne de commande ostress.exe. Le script utilise des éléments créés par le script T-SQL installé précédemment.


Le script suivant insère un exemple de commande client avec cinq lignes dans les *tables* à mémoire optimisée suivantes :

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


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
	
EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
	@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


Pour créer la version \_ondisk du script T-SQL précédent pour ostress.exe, il vous suffit de remplacer les deux occurrences de la sous-chaîne *\_inmem* par *\_ondisk*. Ces remplacements affectent les noms des tables et des procédures stockées.


### Installer les utilitaires RML et ostress


Dans l’idéal, vous devez prévoir d’exécuter ostress.exe sur une machine virtuelle Azure. Vous devez créer une [machine virtuelle Azure](http://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région géographique Azure que celle où se trouve votre base de données AdventureWorksLT. Vous pouvez aussi exécuter ostress.exe sur votre ordinateur portable.


Sur la machine virtuelle (ou sur l’hôte que vous avez choisi d’utiliser), installez les utilitaires RML qui incluent ostress.exe.

- Consultez la discussion ostress.exe [Extensions à AdventureWorks pour illustrer In-Memory OLTP](http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx),
 - [Exemple de base de données pour In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx),
 - ou encore [Blog pour l’installation de ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014, whereas mt465764.aspx is for SQL 2016.
-->



### Commencez par exécuter la charge de travail de contrainte \_inmem


Vous pouvez utiliser une fenêtre d’*invite de commande RML* pour exécuter la ligne de commande ostress.exe.

Lorsque vous exécutez à partir de l’invite de commande RML, la commande ostress.exe effectue les actions suivantes :

- Insère un million de commandes client, avec cinq lignes chacune, dans des tables à mémoire optimisée.
- Utilise 100 connexions simultanées (-n100).


```
ostress.exe -n100 -r500 -S<servername>.database.windows.net
	 -U<login> -P<password> -d<database>
	 -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
	 @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
	 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*
	 10000;
	 INSERT INTO @od SELECT OrderQty, ProductID FROM
	 Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int);
	 WHILE (@i < 20) begin;
	 EXECUTE SalesLT.usp_InsertSalesOrder_inmem
	 @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID,
	 @ShipToAddressID, @od; set @i += 1; end"
```


Pour exécuter la ligne de commande ostress.exe précédente :


1. Réinitialisez la base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données insérées lors des exécutions précédentes : ```
EXECUTE Demo.usp_DemoReset;
```

2. Copiez le texte dans le Presse-papiers.

3. Remplacez tous les caractères de fin de ligne (\\r\\n) et tous les onglets (\\t) par des espaces.

4. Remplacez le <placeholders> pour les paramètres -S - U -P -d avec les valeurs réelles correctes.


#### Il en résulte une durée


Lorsque ostress.exe est terminé, la durée d’exécution est indiquée à la dernière ligne de sortie de fenêtre de commande RML. Par exemple, une série de tests plus courte a duré environ 1,5 minute :

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Réinitialisez, faites la modification pour \_ondisk, puis exécutez de nouveau.


Une fois le résultat de l’exécution de \_inmem obtenu, effectuez les opérations suivantes pour l’exécution de \_indisk :


1. Réinitialisez la base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données insérées lors de l’exécution précédente : ```
EXECUTE Demo.usp_DemoReset;
```

2. Modifiez la ligne de commande ostress.exe pour remplacer toutes les occurrences de *\_inmem* par *\_ondisk*.

3. Réexécutez ostress.exe, puis capturez le résultat de la durée.

4. Réinitialisez de nouveau la base de données pour le nettoyage du responsable.
 - Une seule série de tests insérant un million de commandes génère plus de 500 Mo de données dans les tables à mémoire optimisée.


#### Résultats de la comparaison attendus

Les tests révèlent que les tables à mémoire optimisée fonctionnent **9 fois** plus rapidement que les tables sur disque pour cette charge de travail, avec ostress s’exécutant sur une machine virtuelle dans la même région Azure que la base de données.

Les performances peuvent être améliorées davantage lorsque la conversion des procédures stockées compilées en mode natif est ajoutée.


## B. Installer l’exemple In-Memory Analytics


Dans cette section, vous pourrez comparer les résultats des statistiques et les résultats d’E/S lors de l’utilisation d’un index columnstore par rapport à un index normal.


Les index columnstore sont logiquement identiques aux index classiques, mais différents physiquement. Un index columnstore organise les données de manière exotique pour mieux les compresser. Cela permet d’améliorer significativement les performances.


Pour l’analyse en temps réel sur une charge de travail OLTP, il est souvent préférable d’utiliser un index columnstore NONclustered. Pour plus d’informations, voir [Index columnstore décrits](http://msdn.microsoft.com/library/gg492088.aspx).



### Préparer le test d’analyse columnstore


1. Utilisez le portail Azure pour créer une base de données AdventureWorksLT à partir de l’exemple.
 - Utilisez ce nom exact.
 - Choisissez un niveau de service Premium.

2. Copiez [sql\_in-memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql) dans le Presse-papiers.
 - Le script T-SQL crée les objets In-Memory nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.
 - Le script crée la table Dimension et deux tables de faits. Les tables de faits comprennent 3,5 millions de lignes chacune.
 - Le script peut prendre 15 minutes pour s’exécuter.

3. Collez le script T-SQL dans SSMS, puis exécutez-le.
 - Le mot clé **COLUMNSTORE** est essentiel dans l’instruction **CREATE INDEX**, comme dans l’exemple ci-dessous :<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Définissez AdventureWorksLT sur le niveau de compatibilité 130 :<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`


#### Tables et index columnstore essentiels


- dbo.FactResellerSalesXL\_CCI est une table contenant un index **columnstore** en cluster, ce qui permet la compression avancée au niveau des *données*.

- dbo.FactResellerSalesXL\_PageCompressed est une table qui possède un index en cluster régulier équivalent, compressé uniquement au niveau de la *page*.


#### Requêtes essentielles pour comparer l’index columnstore


Vous trouverez [ici](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) plusieurs types de requête T-SQL que vous pouvez exécuter pour mettre en évidence les améliorations des performances. À l’étape 2 du script T-SQL, deux requêtes vous seront particulièrement utiles. Ces deux requêtes diffèrent uniquement d’une ligne :


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un index columnstore en cluster se trouve dans la table FactResellerSalesXL**\_CCI**.

L’extrait de script T-SQL suivant imprime des statistiques d’E/S et d’heure pour la requête de chaque table.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```


## Considérations relatives à la version préliminaire pour In-Memory


Les fonctionnalités In-Memory dans la base de données SQL Azure sont [disponibles en version préliminaire depuis le 28 octobre 2015](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Pendant la phase d’évaluation précédant la mise à disposition générale, In-Memory OLTP est pris en charge uniquement pour les éléments suivants :

- Bases de données appartenant à un niveau de service *Premium*.

- Bases de données créées après que les fonctionnalités In-Memory sont devenues actives.
 - Une nouvelle base de données restaurée à partir d’une sauvegarde effectuée avant la date d’activation de In-Memory ne peut pas prendre en charge In-Memory.
 - Supposons que vous sauvegardiez une base de données prenant en charge In-Memory, puis que vous restauriez cette sauvegarde dans une ancienne base de données Premium. L’ancienne base de données prend désormais en charge In-Memory.


En cas de doute, vous pouvez toujours exécuter le script T-SQL SELECT suivant pour déterminer si votre base de données prend en charge In-Memory OLTP. Un résultat de **1** signifie que la base de données ne prend pas en charge In-Memory :

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la requête renvoie **1**, In-Memory OLTP est pris en charge dans cette base de données, ainsi que dans toutes les copies ou restaurations de base de données créées à partir de cette base de données.


#### Objets autorisés uniquement au niveau Premium


Si une base de données contient l’une des sortes d’objets ou de types In-Memory OLTP suivants, la rétrogradation du niveau de service de la base de données en service de base ou standard n’est pas prise en charge. Pour passer à une version antérieure de la base de données, commencez par supprimer ces objets :

- Tables à mémoire optimisée
- Types de tables à mémoire optimisée
- Modules compilés en mode natif


#### Autres relations


- L’utilisation des fonctionnalités In-Memory OLTP avec les bases de données dans des pools élastiques n’est pas prise en charge pour l’instant dans la version préliminaire, mais le sera à l’avenir.

- L’utilisation d’In-Memory OLTP avec SQL Data Warehouse n’est pas prise en charge.
 - La fonctionnalité d’index columnstore de In-Memory Analytics est prise en charge dans SQL Data Warehouse.

- Le magasin de requêtes ne capture pas les requêtes à l’intérieur de modules compilés en mode natif pour l’instant dans la version préliminaire, mais cela sera possible à l’avenir.

- Certaines fonctionnalités de Transact-SQL ne sont pas prises en charge avec In-Memory OLTP. Cela s’applique à Microsoft SQL Server et à la base de données SQL Azure. Pour plus d'informations, consultez :
 - [Prise en charge de Transact-SQL pour OLTP en mémoire](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Constructions Transact-SQL non prises en charge par In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx)


## Étapes supplémentaires


- Essayer d’[utiliser In-Memory OLTP dans une application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)


## Ressources supplémentaires

#### Informations supplémentaires

- [Découvrir In-Memory OLTP, qui s’applique à Microsoft SQL Server et à la base de données SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [En savoir plus sur Real-Time Operational Analytics sur MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Le [Livre blanc sur les modèles de charge de travail courants et autres considérations relatives à la migration](http://msdn.microsoft.com/library/dn673538.aspx) décrit des modèles de charge de travail où In-Memory OLTP apporte généralement des gains de performances significatifs.

#### Conception des applications

- [In-Memory OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utiliser In-Memory OLTP dans une application SQL Azure existante.](sql-database-in-memory-oltp-migration.md)

#### Outils

- [Version préliminaire de SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), pour la dernière version mensuelle.

- [Description des utilitaires RML pour SQL Server](http://support.microsoft.com/fr-FR/kb/944837)

- [Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md) pour In-Memory OLTP.

<!---HONumber=Nov15_HO4-->