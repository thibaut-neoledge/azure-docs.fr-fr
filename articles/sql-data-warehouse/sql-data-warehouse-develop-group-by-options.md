<properties
   pageTitle="Options de regroupement dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’implémentation d’options de regroupement dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Options de regroupement dans SQL Data Warehouse

La clause [GROUP BY] est utilisée pour regrouper des données dans un ensemble de lignes récapitulatives. Elle présente également quelques options, qui étendent ses fonctionnalités ; celles-ci doivent faire l’objet d’un contournement, car elles ne sont pas directement prises en charge par Azure SQL Data Warehouse.

Ces options sont :
- GROUP BY avec ROLLUP
- GROUPING SETS
- GROUP BY avec CUBE

## Options ROLLUP et GROUPING SETS
L’option la plus simple consiste à utiliser `UNION ALL` à la place, afin d’effectuer le cumul, plutôt que de se fier à la syntaxe explicite. Le résultat est exactement le même.

Voici un exemple d’instruction GROUP BY utilisant l’option `ROLLUP` :

```
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

En utilisant l’option ROLLUP, nous avons demandé les agrégations suivantes :
- Pays et région
- Pays
- Total général

Pour remplacer cet élément, vous devons utiliser l’élément `UNION ALL`, en spécifiant les agrégations requises de manière explicite pour renvoyer les même résultats :

```
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Pour GROUPING SETS, il nous suffit d’adopter le même principe, en créant uniquement des sections UNION ALL pour les niveaux d’agrégation que nous voulons afficher.

## Options CUBE
Il est possible de créer une commande GROUP BY avec CUBE, à l’aide de l’approche UNION ALL. Il existe cependant un problème : le code peut rapidement devenir fastidieux et difficile à gérer. Pour réduire ce risque, vous pouvez utiliser cette approche plus avancée.

Utilisons l’exemple ci-dessus.

La première étape consiste à définir le « cube » qui définit tous les niveaux d’agrégation que nous souhaitons créer. N’oublions pas de tenir compte de l’action CROSS JOIN associant les deux tables dérivées. Cette action génère tous les niveaux qu’il nous faut. Le reste du code est uniquement placé à des fins de formatage.

```
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Les résultats de la commande CTAS sont affichés ci-dessous :

![][1]

La deuxième étape consiste à spécifier une table cible pour stocker les résultats temporaires :

```
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Quant à la troisième étape, elle consiste à effectuer une boucle sur notre cube de colonnes effectuant l’agrégation. La requête sera exécutée une seule fois pour chaque ligne de la table temporaire « #Cube » ; elle stockera les résultats dans la table temporaire « #Results ».

```
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Enfin, nous pouvons renvoyer les résultats en lisant simplement les données de la table temporaire « #Results ».

```
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Si nous fractionnons le code en sections et générons une construction en boucle, le code devient plus facile à gérer et à entretenir.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->

<!---------HONumber=AcomDC_0309_2016-->