<properties
   pageTitle="Ajouter ou supprimer un tableau croisé dynamique dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’ajout ou la suppression d’un tableau croisé dynamique dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="06/26/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Ajouter ou supprimer un tableau croisé dynamique dans SQL Data Warehouse

Dans SQL Data Warehouse, vous pouvez ajouter un tableau croisé dynamique à partir des données, via l’instruction CASE.

Cet article contient deux exemples simples vous permettant d’ajouter ou de supprimer des tableaux croisés dynamiques sans utiliser les syntaxes correspondantes proposées par SQL Server.

## Ajouter un tableau croisé dynamique

```
CREATE TABLE AnnualSales_pvt
WITH    (   DISTRIBUTION = ROUND_ROBIN
        )
AS
WITH SalesPVT 
AS
(   SELECT  [EnglishProductCategoryName]
    ,       CASE WHEN [CalendarYear] = 2001 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2001'
    ,       CASE WHEN [CalendarYear] = 2002 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2002'
    ,       CASE WHEN [CalendarYear] = 2003 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2003'
    ,       CASE WHEN [CalendarYear] = 2004 THEN SUM([SalesAmount]) ELSE 0 END AS 'CY_2004'
    FROM    [dbo].[FactInternetSales] s
    JOIN    [dbo].[DimDate] d               ON s.[OrderDateKey]          = d.[DateKey]
    JOIN    [dbo].[DimProduct] p            ON s.[ProductKey]            = p.[ProductKey]
    JOIN    [dbo].[DimProductSubCategory] u ON p.[ProductSubcategoryKey] = u.[ProductSubcategoryKey]
    JOIN    [dbo].[DimProductCategory] c    ON u.[ProductCategoryKey]    = c.[ProductCategoryKey]
    GROUP BY 
            [EnglishProductCategoryName]
    ,       [CalendarYear]
)
SELECT  [EnglishProductCategoryName]
,       SUM([CY_2001])  AS 'CY_2001'
,       SUM([CY_2002])  AS 'CY_2002'
,       SUM([CY_2003])  AS 'CY_2003'
,       SUM([CY_2004])  AS 'CY_2004'
FROM    SalesPVT
GROUP BY 
        [EnglishProductCategoryName]
;
```

## Supprimer le tableau croisé dynamique

L’opération de suppression d’un tableau croisé dynamique est légèrement plus complexe. Toutefois, vous pouvez l’effectuer via l’instruction `CASE`. Pour cela, vous devez d’abord déterminer le nombre de colonnes concernées par la suppression du tableau croisé dynamique. Dans l’exemple précédent, ce nombre se montait à quatre. Conservez-le. Pour effectuer la suppression du tableau croisé dynamique, nous devrons développer le jeu de données de manière temporaire, selon un facteur de 4. Il en résulte un processus en deux étapes :

Tout d’abord, créez une table temporaire contenant quatre lignes. Nous allons l’utiliser pour développer les données :

```
CREATE TABLE #Nmbr
WITH 
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION     = USER_DB
)
AS
SELECT 1 AS 'Number'
UNION ALL
SELECT 2
UNION ALL
SELECT 3
UNION ALL
SELECT 4
OPTION (LABEL = 'CTAS : #Nmbr : CREATE')
;
```

La deuxième étape consiste à utiliser CASE pour supprimer le tableau croisé dynamique, de manière conditionnelle, afin d’obtenir à nouveau des lignes. Pour ce faire, nous devons créer un produit cartésien en joignant à la table temporaire la valeur #Nmbr créée à la première étape :

```
SELECT  [EnglishProductCategoryName]
,       CASE    c.[Number]
                WHEN 1 THEN [CY_2001]
                WHEN 2 THEN [CY_2002]
                WHEN 3 THEN [CY_2003]
                WHEN 4 THEN [CY_2004]
        END as Sales
FROM AnnualSales_pvt
JOIN #Nmbr c ON 1=1
WHERE   CASE    c.[Number]
                WHEN 1 THEN CY_2001
                WHEN 2 THEN CY_2002
                WHEN 3 THEN CY_2003
                WHEN 4 THEN CY_2004
        END IS NOT NULL
OPTION (LABEL = 'Unpivot :  : SELECT')
;
```

Pour finir, n’oubliez pas d’effectuer le nettoyage en supprimant la table temporaire.

```
DROP TABLE #Nmbr
;
```

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->