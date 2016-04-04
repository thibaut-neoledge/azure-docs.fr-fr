<properties
   pageTitle="Gestion des index | Microsoft Azure"
   description="Conseils pour aider les utilisateurs à gérer leurs index"
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
   ms.date="03/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestion des index
Cet article explique certaines techniques principales de gestion des index.

## Optimisation des reconstructions d’index
Vous pouvez optimiser votre reconstruction d’index de deux manières :

1. Partitionnez votre table et exécutez les reconstructions d’index au niveau de la partition
2. Utilisez [CTAS][] pour recréer la partition des données dans une nouvelle table et basculez la partition vers la nouvelle table

## Reconstructions d’index partitionnés

Voici un exemple montrant comment reconstruire une partition unique :

```
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

ALTER INDEX..REBUILD est mieux adapté aux petits volumes de données, en particulier sur les index columnstore. Les groupes de lignes ouvertes, fermées et compressées sont inclus dans la reconstruction. Toutefois, si la partition est volumineuse, vous constaterez que `CTAS` est l’opération la plus efficace. Voici un exemple de reconstruction d’index complet

```
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

Consultez l’article [ALTER INDEX][] pour plus d’informations sur cette syntaxe.

## Utilisation de CTAS pour reconstruire une partition

Voici un exemple montrant comment reconstruire une partition à l’aide de CTAS :

```
-- Step 01. Select the partition of data and write it out to a new table using CTAS
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 02. Create a SWITCH out table
 
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 03. Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 04. Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;

```

## Étapes suivantes
Consultez l’article sur le [partitionnement de table][] pour obtenir des instructions sur la création et la taille des partitions. Cet article contient également un exemple pour vous aider à identifier les limites de partition.

Pour plus de conseils sur la gestion, consultez la présentation de la [gestion][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[partitionnement de table]: sql-data-warehouse-develop-table-partitions.md
[gestion]: sql-data-warehouse-manage-monitor.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/fr-FR/library/ms188388.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0323_2016-->