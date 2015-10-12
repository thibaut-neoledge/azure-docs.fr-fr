<properties
   pageTitle="Instruction Create Table As Select (CTAS) dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils de codage à l’aide de l’instruction Create Table As Select (CTAS) dans Azure SQL Data Warehouse pour le développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Instruction Create Table As Select (CTAS) dans SQL Data Warehouse
L’instruction Create Table As Select (CTAS) est l’une des fonctionnalités T-SQL les plus importantes. Il s’agit d’une opération entièrement parallélisée qui crée une table en fonction de la sortie d’une instruction Select. Vous pouvez l’envisager comme une version suralimentée de SELECT ... INTO.

CTAS permet également de contourner un certain nombre de fonctionnalités non prises en charge qui sont répertoriées ci-après. L’utilisation de cette instruction se révèle doublement avantageuse, car elle assure non seulement la conformité de votre code, mais également une vitesse d’exécution accrue de ce dernier sur SQL Data Warehouse. Ces deux avantages découlent de sa conception entièrement parallélisée.

> [AZURE.NOTE]Envisagez toujours de recourir à l’instruction CTAS en premier lieu. Si vous pensez pouvoir résoudre un problème en utilisant une instruction CTAS, il s’agit généralement de la meilleure approche à adopter, même si cela vous oblige à écrire davantage de données.

Les scénarios qui peuvent être contournés avec CTAS comprennent notamment :

- SELECT .. INTO
- Jointures ANSI sur les opérations UPDATE 
- Jointures ANSI sur les opérations DELETE
- Instruction MERGE

Ce document fournit également quelques recommandations en matière de codage avec CTAS.

## SELECT .. INTO
L’instruction SELECT ... INTO peut apparaître à de nombreux emplacements dans votre solution.

Voici un exemple d’utilisation de SELECT .. INTO :

```
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

La conversion de cette instruction en CTAS se révèle assez simple :

```
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

L’utilisation de l’instruction CTAS vous permet également de spécifier une préférence en matière de distribution des données et d’indexer la table si vous le souhaitez.

## Remplacement de jointures ANSI pour les instructions de mise à jour

Vous disposez peut-être d’une mise à jour complexe qui joint plus de deux tables à l’aide d’une syntaxe de jointure ANSI pour l’exécution d’une opération UPDATE ou DELETE.

Imaginons que vous souhaitiez mettre à jour la table suivante :

```
CREATE TABLE [dbo].[AnnualCategorySales]
(	[EnglishProductCategoryName]	NVARCHAR(50)	NOT NULL
,	[CalendarYear]					SMALLINT		NOT NULL
,	[TotalSalesAmount]				MONEY			NOT NULL
)
WITH
(
	DISTRIBUTION = ROUND_ROBIN
)
;
```

La requête d’origine pourrait ressembler à ceci :

```
UPDATE	acs
SET		[TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM	[dbo].[AnnualCategorySales] 	AS acs
JOIN	(
		SELECT	[EnglishProductCategoryName]
		,		[CalendarYear]
		,		SUM([SalesAmount])				AS [TotalSalesAmount]
		FROM	[dbo].[FactInternetSales]		AS s
		JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
		JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
		JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
		JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
		WHERE 	[CalendarYear] = 2004
		GROUP BY
				[EnglishProductCategoryName]
		,		[CalendarYear]
		) AS fis
ON	[acs].[EnglishProductCategoryName]	= [fis].[EnglishProductCategoryName]
AND	[acs].[CalendarYear]				= [fis].[CalendarYear]
;
```

Étant donné que SQL Data Warehouse ne prend pas en charge les jointures ANSI, vous ne pouvez pas copier ce code sans le modifier légèrement.

Vous pouvez remplacer ce code en combinant une instruction CTAS et une jointure implicite :

```
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT	ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)	AS [EnglishProductCategoryName]
,		ISNULL(CAST([CalendarYear] AS SMALLINT),0) 						AS [CalendarYear]
,		ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)						AS [TotalSalesAmount]
FROM	[dbo].[FactInternetSales]		AS s
JOIN	[dbo].[DimDate]					AS d	ON s.[OrderDateKey]				= d.[DateKey]
JOIN	[dbo].[DimProduct]				AS p	ON s.[ProductKey]				= p.[ProductKey]
JOIN	[dbo].[DimProductSubCategory]	AS u	ON p.[ProductSubcategoryKey]	= u.[ProductSubcategoryKey]
JOIN	[dbo].[DimProductCategory]		AS c	ON u.[ProductCategoryKey]		= c.[ProductCategoryKey]
WHERE 	[CalendarYear] = 2004
GROUP BY
		[EnglishProductCategoryName]
,		[CalendarYear]
;

-- Use an implicit join to perform the update 
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## Remplacement de jointures ANSI pour les instructions de suppression
L’utilisation de l’instruction CTAS constitue parfois la meilleure approche pour supprimer des données. Plutôt que de supprimer les données, vous pouvez simplement sélectionner les données à conserver. Cette approche est particulièrement recommandée dans le cas des instructions DELETE qui utilisent une syntaxe de jointure ANSI, car cette dernière n’est pas prise en charge par SQL Data Warehouse.

Voici un exemple d’instruction DELETE convertie :

```
CREATE TABLE dbo.DimProduct_upsert
WITH 
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p 
RIGHT JOIN dbo.stg_DimProduct s 
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## Remplacement d’instructions MERGE
Vous pouvez remplacer les instructions MERGE, du moins partiellement, à l’aide de CTAS. Vous pouvez consolider les instructions `INSERT` et `UPDATE` dans une seule et même instruction. Tous les enregistrements supprimés doivent être isolés dans une seconde instruction.

Voici un exemple d’utilisation d’une instruction consolidée `UPSERT` :

```
CREATE TABLE dbo.[DimProduct_upsert]
WITH 
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS 
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## Recommandation CTAS : déclarer explicitement le type de données et la possibilité de valeur NULL de la sortie

Lorsque vous procédez à la migration de votre code, vous pouvez constater que vous exécutez le type de modèle de codage suivant :

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f 
;
```

Instinctivement, vous pourriez envisager de convertir ce code en instruction CTAS, et vous auriez raison. Toutefois, un problème se dissimule derrière ce scénario.

Le code suivant ne génère PAS le même résultat :

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Notez que la colonne « result » indique le type de données et les valeurs de possibilité de valeur NULL de l’expression. De légers écarts de valeurs peuvent en découler si vous n’y prenez pas garde.

Essayez l’exemple de code suivant :

```
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

La valeur stockée en guise de résultat diffère. Étant donné que la valeur persistante dans la colonne de résultat est utilisée dans d’autres expressions, l’erreur devient encore plus significative.

![][1]

Ceci se révèle particulièrement important dans le cas des migrations de données. Bien que la seconde requête soit effectivement plus précise, il existe un problème. Les données obtenues diffèrent de celles du système source, ce qui compromet l’intégrité de la migration. Il s’agit là de l’un des rares cas dans lesquels la « mauvaise » réponse est en réalité la bonne !

Cet écart entre les deux résultats est dû à la conversion de type (transtypage) implicite. Dans le premier exemple, la table définit la définition de colonne. Lorsque la ligne est insérée, une conversion de type implicite est effectuée. Dans le second exemple, aucune conversion de type implicite ne se produit, car l’expression définit le type de données de la colonne. Notez également que la colonne figurant dans le second exemple a été définie comme une colonne Nullable, ce qui n’est pas son cas dans le premier exemple. Lorsque la table a été créée dans le premier exemple, la possibilité de valeur Null de la colonne a été définie explicitement. Dans le second exemple, cet aspect est déterminé par l’expression et entraîne par défaut une définition de valeur Null.

Pour résoudre ces problèmes, vous devez explicitement définir la conversion de type et la possibilité de valeur Null dans la partie SELECT de l’instruction CTAS. Vous ne pouvez pas définir ces propriétés dans la partie CREATE TABLE.

L’exemple ci-dessous indique comme corriger le code :

```
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Notez les points suivants : nous aurions pu utiliser CAST ou CONVERT ; la possibilité de valeur Null est forcée avec ISNULL, et non avec COALESCE ; ISNULL est la fonction située la plus à l’extérieur ; la seconde partie de l’instruction ISNULL est une constante, c’est-à-dire 0.

> [AZURE.NOTE]La possibilité de valeur Null doit être impérativement définie avec ISNULL, et non avec COALESCE. COALESCE n’est pas une fonction déterministe ; le résultat de l’expression sera donc toujours Nullable. ISNULL fonctionne différemment. Cette fonction est déterministe. Par conséquent, lorsque la seconde partie de la fonction ISNULL est une constante ou un littéral, la valeur résultante est NOT NULL.

Ce conseil ne vous sert pas seulement à assurer l’intégrité de vos calculs. Il est également important dans le cadre du basculement de partition de table. Imaginons que vous ayez défini la table suivante :

```
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH 
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
) 
;
```

Toutefois, le champ de valeur est une expression calculée qui ne fait pas partie des données sources.

Pour créer votre jeu de données partitionné, vous pouvez exécuter le code suivant :

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Cette requête s’exécute parfaitement. Toutefois, un problème se pose lorsque vous essayez de procéder au basculement de partition. Les définitions de table ne correspondent pas. Vous devez donc modifier l’instruction CTAS pour faire correspondre ces définitions.

```
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product] 
,   [store] 
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Vous pouvez alors constater que la cohérence des types et le maintien des propriétés de possibilité de valeur Null sur une instruction CTAS constituent des approches d’ingénierie judicieuses. Ces opérations contribuent à garantir l’intégrité de vos calculs, ainsi que la possibilité de basculement de partition.

Pour plus d’informations sur l’utilisation de l’instruction [CTAS][], voir le site MSDN. Cette instruction est l’une des plus importantes d’Azure SQL Data Warehouse. Vous devez donc faire en sorte d’en comprendre les moindres aspects.

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/fr-FR/library/mt204041.aspx

<!--Other Web references-->

<!---HONumber=Oct15_HO1-->