<properties
	pageTitle="Prise en main de SQL In-Memory | Microsoft Azure"
	description="Les technologies SQL In-Memory améliorent considérablement les performances des charges de travail transactionnelles et analytiques. Apprenez à tirer parti de ces technologies."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jhubbard"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/29/2016"
	ms.author="jodebrui"/>


# Prise en main de In-Memory (version préliminaire) dans la base de données SQL

Les fonctions In-Memory améliorent considérablement la performance des charges de travail transactionnelles et analytiques dans les bonnes situations.

Cette rubrique met l’accent sur deux démonstrations, l’une pour l’OLTP en mémoire, et l’autre pour l’analytique en mémoire. Chaque démonstration est fournie avec les opérations et le code dont vous avez besoin pour exécuter la démonstration. Vous pouvez :

- utiliser le code pour tester les variations pour voir les différences dans les résultats de performances ; ou
- lire le code pour comprendre le scénario et savoir comment créer et utiliser des objets In-Memory.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- L’article [Démarrage rapide 1 : Technologies OLTP en mémoire pour des performances T-SQL plus rapides](http://msdn.microsoft.com/library/mt694156.aspx) peut vous aider à commencer.

#### OLTP In-Memory.

Les fonctionnalités d’[OLTP](#install_oltp_manuallink) In-Memory (traitement transactionnel en ligne) sont :

- Tables à mémoire optimisée.
- Procédures stockées compilées en mode natif.


Une table à mémoire optimisée place une représentation d’elle-même dans la mémoire active, en plus de la représentation standard sur un disque dur. Les transactions commerciales sur la table s’exécutent plus rapidement car elles interagissent directement uniquement avec la représentation dans la mémoire active.

Avec l’OLTP en mémoire, vous pouvez multiplier jusqu’à 30 le débit des transactions, selon les caractéristiques de la charge de travail.


Les procédures stockées compilées en mode natif exigent moins d’instructions machine pendant l’exécution que les procédures stockées interprétées traditionnelles. Nous avons vu des résultats de compilation natifs obtenus dans une durée d’1/100e de la durée interprétée.


#### In-Memory Analytics 

La fonction In-Memory [Analytics](#install_analytics_manuallink) est :

Les index ColumnStore améliorent les performances des analyses et des requêtes de rapport.


#### Analyse en temps réel

Pour l’[analyse en temps réel](http://msdn.microsoft.com/library/dn817827.aspx), vous combinez l’OLTP In-Memory et Analytics pour obtenir :

- Une vue globale de l’entreprise en temps réel en fonction des données opérationnelles.


#### Availability


Disponibilité générale :

- les [Index Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) qui sont *sur disque*.


Aperçu :

- OLTP In-Memory.
- Real-Time Operational Analytics


Les éléments à prendre en compte tant que les fonctionnalités en mémoire sont en version préliminaire sont décrits [plus loin dans cette rubrique](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Ces fonctionnalités en version préliminaire sont uniquement disponibles pour [*Premium*](sql-database-service-tiers.md) bases de données SQL Azure, pas pour les bases de données sur le niveau de service Standard ou de base.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A. Installer l’exemple In-Memory OLTP

Vous pouvez créer l’exemple de base de données AdventureWorksLT [V12] en quelques clics sur le [portail Azure](https://portal.azure.com/). Les étapes de cette section expliquent comment enrichir votre base de données AdventureWorksLT grâce aux éléments suivants :

- Tables In-Memory.
- Procédure stockée compilée en mode natif.


#### Procédure d’installation :

1. Dans le [portail Azure](https://portal.azure.com/), créez une base de données Premium sur un serveur V12. Définissez comme valeur **Source** l’exemple de base de données AdventureWorksLT [V12].
 - Pour obtenir des instructions détaillées, voir [Créer votre première base de données SQL Azure](sql-database-get-started.md).

2. Connectez-vous à la base de données avec [SQL Server Management Studio (SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiez le [script In-Memory OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) dans le Presse-papiers.
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


Un résultat de **0** signifie que In-Memory n’est pas pris en charge, et un résultat de 1 signifie qu’il l’est. Pour diagnostiquer le problème :

- Assurez-vous que la base de données a été créée après que les fonctionnalités OLTP In-Memory sont devenues actives pour la version d’essai.
- Vérifiez que la base de données se trouve sur le niveau de service Premium.


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


Dans cette section, vous apprendrez à utiliser l’utilitaire **ostress.exe**, pratique pour exécuter les deux procédures stockées à des niveaux de contrainte élevés. Vous pouvez comparer le temps d’exécution des deux contraintes.


Lorsque vous exécutez ostress.exe, nous vous recommandons de transmettre des valeurs de paramètre conçues pour :

- Exécuter un grand nombre de connexions simultanées, en utilisant -n100.
- Répéter chaque boucle de connexion une centaine de fois, en utilisant -r500.


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
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
	SET @i = @i + 1;
end
```


Pour créer la version \_ondisk du script T-SQL précédent pour ostress.exe, il suffit de remplacer les deux occurrences de la sous-chaîne *\_inmem* par *\_ondisk*. Ces remplacements affectent les noms des tables et des procédures stockées.


### Installer les utilitaires RML et ostress


Dans l’idéal, vous devez prévoir d’exécuter ostress.exe sur une machine virtuelle Azure. Vous devez créer une [machine virtuelle Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région géographique Azure que celle où se trouve votre base de données AdventureWorksLT. Vous pouvez aussi exécuter ostress.exe sur votre ordinateur portable.


Sur la machine virtuelle (ou sur l’hôte que vous avez choisi d’utiliser), installez les utilitaires RML qui incluent ostress.exe.

- Consultez la discussion sur ostress.exe dans [Exemple de base de données OLTPIn-Memory](http://msdn.microsoft.com/library/mt465764.aspx),
 - ou l’[exemple de base de données pour In-Memory OLTP](http://msdn.microsoft.com/library/mt465764.aspx),
 - ou encore, [Blog pour l’installation de ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### Commencez par exécuter la charge de travail de contrainte \_inmem


Vous pouvez utiliser une fenêtre d’*invite de commande RML* pour exécuter la ligne de commande ostress.exe. Les paramètres de ligne de commande dirigent ostress vers :

- Exécuter 100 connexions simultanément (-n100).
- Chaque connexion doit exécuter le script T-SQL 50 fois (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Pour exécuter la ligne de commande ostress.exe précédente :


1. Réinitialisez le contenu de la base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données insérées lors des exécutions précédentes :
```
EXECUTE Demo.usp_DemoReset;
```

2. Copiez le texte de la ligne de commande ostress.exe qui précède dans le presse-papiers.

3. Remplacez le `<placeholders>` des paramètres -S - U -P -d par les valeurs réelles correctes.

4. Exécutez la ligne de commande que vous avez modifiée dans la fenêtre de commande RML.


#### Il en résulte une durée


Lorsque ostress.exe est terminé, la durée d’exécution est indiquée à la dernière ligne de sortie de fenêtre de commande RML. Par exemple, une série de tests plus courte a duré environ 1,5 minute :

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### Réinitialisez, faites la modification pour \_ondisk, puis exécutez de nouveau.


Une fois le résultat de l’exécution de \_inmem obtenu, effectuez les opérations suivantes pour l’exécution de \_indisk :


1. Réinitialisez la base de données en exécutant la commande suivante dans SSMS, et ce, pour supprimer toutes les données insérées lors de l’exécution précédente :
```
EXECUTE Demo.usp_DemoReset;
```

2. Modifiez la ligne de commande ostress.exe pour remplacer toutes les occurrences de *\_inmem* par *\_ondisk*.

3. Réexécutez ostress.exe une deuxième fois, puis enregistrez le résultat de durée.

4. Réinitialisez à nouveau la base de données pour supprimer une quantité de données de test qui peut s’avérer conséquente.


#### Résultats de la comparaison attendus

Nos tests en mémoire montrent une multiplication par **9** de l’amélioration des performances pour cette charge de travail simple, avec ostress s’exécutant sur une machine virtuelle Azure dans la même région Azure que la base de données.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## B. Installer l’exemple In-Memory Analytics


Dans cette section, vous comparez les résultats des statistiques et les résultats d’E/S lors de l’utilisation d’un index columnstore par rapport à un index d’arbre B traditionnel.


Pour l’analyse en temps réel sur une charge de travail OLTP, il est souvent préférable d’utiliser un index columnstore NONclustered. Pour plus d’informations, voir [Index columnstore décrits](http://msdn.microsoft.com/library/gg492088.aspx).



### Préparer le test d’analyse columnstore


1. Utilisez le portail Azure pour créer une base de données AdventureWorksLT à partir de l’exemple.
 - Utilisez ce nom exact.
 - Choisissez un niveau de service Premium.

2. Copiez [sql\_in-memory\_analytics\_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) dans le Presse-papiers.
 - Le script T-SQL crée les objets In-Memory nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.
 - Le script crée la table Dimension et deux tables de faits. Les tables de faits comprennent 3,5 millions de lignes chacune.
 - Le script peut prendre 15 minutes pour s’exécuter.

3. Collez le script T-SQL dans SSMS, puis exécutez-le.
 - Le mot clé **COLUMNSTORE** est essentiel dans l’instruction **CREATE INDEX**, comme dans l’exemple ci-dessous :<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Définissez AdventureWorksLT sur le niveau de compatibilité 130 :<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Le niveau 130 n’est pas directement lié aux fonctionnalités In-Memory. Mais le niveau 130 offre généralement des performances de requêtes supérieures que le niveau 120.


#### Tables et index columnstore essentiels


- dbo.FactResellerSalesXL\_CCI est une table contenant un index **columnstore** en cluster, ce qui permet la compression avancée au niveau des *données*.

- dbo.FactResellerSalesXL\_PageCompressed est une table qui possède un index en cluster régulier équivalent, compressé uniquement au niveau de la *page*.


#### Requêtes essentielles pour comparer l’index columnstore


Vous trouverez [ici](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) plusieurs types de requête T-SQL que vous pouvez exécuter pour mettre en évidence les améliorations des performances. À l’étape 2 du script T-SQL, deux requêtes vous seront particulièrement utiles. Ces deux requêtes diffèrent uniquement d’une ligne :


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



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## Considérations relatives à la version préliminaire pour OLTP In-Memory


Les fonctionnalités OLTP In-Memory dans la base de données SQL Azure sont [disponibles en version préliminaire depuis le 28 octobre 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Dans la version préliminaire actuelle, OLTP In-Memory est pris en charge uniquement pour les bases de données suivantes :

- Bases de données appartenant à un niveau de service *Premium*.

- Bases de données créées après que les fonctionnalités OLTP In-Memory sont devenues actives.
 - Une nouvelle base de données ne peut pas prendre en charge OLTP In-Memory si elle est restaurée à partir d’une base de données qui a été créée avant que les fonctionnalités OLTP en mémoire soient devenues actives.


En cas de doute, vous pouvez toujours exécuter le script T-SQL SELECT suivant pour déterminer si votre base de données prend en charge l’OLTP en mémoire. Un résultat de **1** signifie que la base de données ne prend pas en charge OLTP In-Memory :

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Si la requête renvoie **1**, l’OLTP en mémoire est pris en charge dans cette base de données, ainsi que dans toutes les copies ou restaurations de base de données créées à partir de cette base de données.


#### Objets autorisés uniquement au niveau Premium


Si une base de données contient l’une des sortes d’objets ou de types In-Memory OLTP suivants, la rétrogradation du niveau de service de la base de données en service de base ou standard n’est pas prise en charge. Pour passer à une version antérieure de la base de données, commencez par supprimer ces objets :

- Tables à mémoire optimisée
- Types de tables à mémoire optimisée
- Modules compilés en mode natif


#### Autres relations


- L’utilisation des fonctionnalités In-Memory OLTP avec les bases de données dans des pools élastiques n’est pas prise en charge pour l’instant dans la version préliminaire.
 - Pour déplacer vers un pool élastique une base de données qui contient ou a contenu des objets In-Memory OLTP, procédez comme suit :
  - 1. Supprimez les tables optimisées en mémoire, les types de table et les modules T-SQL compilés en mode natif de la base de données
  - 2. Modifiez le niveau de service de la base de données en standard
  - 3. Déplacez la base de données vers le pool élastique

- L’utilisation d’In-Memory OLTP avec SQL Data Warehouse n’est pas prise en charge.
 - La fonctionnalité d’index columnstore de In-Memory Analytics est prise en charge dans SQL Data Warehouse.

- Le magasin de requêtes ne capture pas les requêtes à l’intérieur de modules compilés en mode natif.

- Certaines fonctionnalités de Transact-SQL ne sont pas prises en charge avec In-Memory OLTP. Cela s’applique à Microsoft SQL Server et à la base de données SQL Azure. Pour plus d'informations, consultez les rubriques :
 - [Prise en charge de Transact-SQL pour OLTP en mémoire](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Constructions Transact-SQL non prises en charge par In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx)


## Étapes suivantes


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

<!---HONumber=AcomDC_0831_2016-->