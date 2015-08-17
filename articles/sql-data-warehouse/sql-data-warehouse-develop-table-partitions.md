<properties
   pageTitle="Partitions de tables dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de partitions de tables dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="06/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Partitions de tables dans SQL Data Warehouse

Pour migrer les définitions des partitions SQL Server vers SQL Data Warehouse, procédez comme suit :

- Supprimez les schémas et fonctions de partition SQL Server, car le système les gère pour vous lorsque vous créez la table.
- Définissez les partitions lorsque vous créez la table. Il vous suffit d’indiquer les points de limite des partitions, en précisant s’ils doivent correspondre à un paramètre `RANGE RIGHT` ou `RANGE LEFT` effectif.

### Dimensionnement des partitions
Le dimensionnement des partitions est un aspect important à prendre en compte dans SQL Data Warehouse. En général, les opérations de gestion des données et les routines de chargement des données ciblent des partitions individuelles, et non toute la table directement. Cela est particulièrement utile pour les index columnstore cluster (CCI). Ce type d’index peut consommer une grande quantité de mémoire. Pour cette raison, nous pouvons être amenés à modifier la granularité du plan de partitionnement, mais nous voulons éviter d’affecter aux partitions une taille qui entraînerait une trop grande sollicitation de la mémoire lors de l’exécution des tâches de gestion.

Lorsque vous choisissez la granularité des partitions, vous ne devez pas oublier que SQL Data Warehouse distribue automatiquement les données dans des distributions. Par conséquent, les données qui devraient se trouver dans une table, au sein d’une partition d’une base de données SQL Server, se trouvent désormais dans une autre partition, distribuées dans différentes tables de la base de données SQL Data Warehouse. Pour garantir un nombre pertinent de lignes dans chaque partition, vous devez généralement modifier la taille des limites des partitions. Par exemple, si vous avez appliqué un partitionnement sur une période d’une journée aux données de votre entrepôt, vous pouvez envisager une période plus étendue (par exemple, un moins ou un trimestre).

Pour dimensionner la taille de la base de données actuelle au niveau de la partition, utilisez une requête similaire à ce qui suit :

```
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]        = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

Le nombre total de distributions est égal au nombre d’emplacements de stockage utilisés lorsque nous créons une table. Plus simplement, cela signifie que chaque table est créée une seule fois pour chaque distribution.

Vous pouvez également prévoir de manière approximative le nombre de lignes de chaque partition et, de ce fait, la taille de cette dernière. La partition de l’entrepôt de données source est divisée en plusieurs éléments dans chaque distribution.

Pour faciliter la définition de la taille de votre partition, appliquez le calcul suivant :

Taille de la partition MPP = Taille de la partition SMP / Nombre de distributions

Vous pouvez déterminer le nombre de distributions figurant dans votre base de données SQL Data Warehouse en appliquant la requête suivante :

```
SELECT  COUNT(*)
FROM    sys.pdw_distributions
;
```

Vous connaissez désormais la taille de chaque partition dans le système source et savez à peu près quelle taille la base de données SQLDW présentera. À présent, vous pouvez choisir les limites de votre partition.

### Gestion de la charge de travail
Il reste un dernier facteur à prendre en compte en ce qui concerne les partitions de tables : la gestion des charges de travail. Dans SQL Data Warehouse, la quantité de mémoire maximale allouée à chaque distribution lors de l’exécution des requêtes est définie par cette fonction. Pour en savoir plus sur la gestion des charges de travail, consultez [cet article]. Dans l’idéal, lorsque vous dimensionnez votre partition, vous tenez compte des opérations en mémoire (telles que la reconstruction d’index columnstore). Une reconstruction d’index est une opération qui exploite une grande quantité de mémoire. Vous devez donc vous assurer que la mémoire disponible pour une reconstruction d’index de partition est suffisante. Pour augmenter la quantité de mémoire disponible pour votre requête, vous devez passer du rôle par défaut à un autre rôle.

Si vous voulez obtenir des informations sur l’allocation de la mémoire pour chaque distribution, interrogez les vues de gestion dynamique du gouverneur de ressources. En réalité, votre allocation de mémoire sera inférieure aux nombres indiqués ci-dessous. Toutefois, cette figure peut vous aider à dimensionner vos partitions lors d’opérations de gestion des données.

```
SELECT  rp.[name]								AS [pool_name]
,       rp.[max_memory_kb]						AS [max_memory_kb]
,       rp.[max_memory_kb]/1024					AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576				AS [mex_memory_gb]
,       rp.[max_memory_percent]					AS [max_memory_percent]
,       wg.[name]								AS [group_name]
,       wg.[importance]							AS [group_importance]
,       wg.[request_max_memory_grant_percent]	AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups	wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools	rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
```

> [AZURE.NOTE]Évitez de dimensionner vos partitions en affectant une taille supérieure à la mémoire octroyée que fournit la classe de ressources de très grande taille. Si la taille de vos partitions dépasse cette valeur, vous risquez de solliciter la mémoire de manière trop intensive, ce qui peut nuire à l’efficacité de la compression.

## Basculement de partitions
Pour faire basculer une partition d’une table à une autre, vous devez vous assurer que les partitions s’alignent sur leurs limites respectives et que les définitions de tables correspondent. Comme aucune contrainte CHECK n’est disponible pour appliquer la plage de valeurs dans une table, la table source doit contenir les mêmes limites de partition que la table cible. Dans le cas contraire, le basculement de la partition échoue, car ses métadonnées ne sont pas synchronisées.

### Fractionnement d’une partition contenant des données
Pour fractionner une partition qui contient déjà des données, la méthode la plus efficace consiste à utiliser une instruction `CTAS`. Si la table partitionnée est un CCI, la partition de table doit être vide pour pouvoir être fractionnée.

Voici un exemple de table columnstore partitionnée qui inclut une ligne dans la partition finale :

```
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,20010101,1,1,1,1,1,1)

CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey)
```

> [AZURE.NOTE]En créant l’objet de statistiques, nous favorisons la précision des métadonnées des tables. Si ces statistiques ne sont pas créées, SQL Data Warehouse utilise les valeurs par défaut. Pour en savoir plus sur les statistiques, voir [Gérer des statistiques dans SQL Data Warehouse][].

Nous pouvons ensuite rechercher le nombre de lignes exploitant la vue de catalogue `sys.partitions` :

```
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
```

Si nous essayons de fractionner cette table, l’erreur suivante s’affiche :

```
ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

Le message 35346, au niveau 15, état 1, ligne 44, indique que la clause SPLIT de l’instruction ALTER PARTITION a échoué, car la partition n’est pas vide. Seules les partitions vides peuvent faire l’objet d’un fractionnement lorsque la table inclut un index columnstore. Vous pouvez envisager de désactiver l’index columnstore avant de lancer l’instruction ALTER PARTITION, puis de reconstruire l’index columnstore une fois l’opération ALTER PARTITION terminée.

Cependant, nous pouvons utiliser la commande `CTAS` pour créer une table afin de stocker nos données.

```
CREATE TABLE dbo.FactInternetSales_20010101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101
                                )
                            )
            )
AS
SELECT *
FROM	FactInternetSales
WHERE	1=2
```

Comme les limites de la partition sont alignées, le basculement est autorisé. La table source présente donc une partition vide, que nous pouvons fractionner.

```
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20010101 PARTITION 2

ALTER TABLE FactInternetSales SPLIT RANGE (20020101)
```

Il ne nous reste plus qu’à aligner nos données en fonction des limites de la nouvelle partition, via la commande `CTAS`, et à faire basculer nos données une nouvelle fois vers la table principale.

```
CREATE TABLE [dbo].[FactInternetSales_20010101_20020101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20010101,20020101
                                )
                            )
            )
AS
SELECT  *
FROM	[dbo].[FactInternetSales_20010101]
WHERE	[OrderDateKey] >= 20010101
AND     [OrderDateKey] <  20020101

ALTER TABLE FactInternetSales_20010101_20020101 SWITCH PARTITION 3 TO  FactInternetSales PARTITION 3
```

Une fois le déplacement des données effectué, il est judicieux d’actualiser les statistiques sur la table cible, afin de vérifier qu’elles reflètent de manière précise la nouvelle distribution des données sur leurs partitions respectives.

```
UPDATE STATISTICS [dbo].[FactInternetSales]
```

### Contrôle de code source dans le cadre du partitionnement d’une table
Pour éviter que la définition de votre table ne **se corrompe** dans votre système de contrôle du code source, vous pouvez procéder comme suit :

1. Créez la table en tant que table partitionnée, mais sans ajouter de valeurs de partition.

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. Appliquez à la table la commande `SPLIT` lors du processus de déploiement :

```
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                     --iterator for while loop
,       @q NVARCHAR(4000)              --query
,       @p NVARCHAR(20)     = N''      --partition_number
,       @s NVARCHAR(128)    = N'dbo'   --schema
,       @t NVARCHAR(128)    = N'table' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Grâce à cette méthode, le code faisant l’objet d’un contrôle de code source reste statique. Quant aux valeurs limites du partitionnement, elles peuvent rester dynamiques et continuer à évoluer au fil du temps.

>[AZURE.NOTE]Le basculement d’une partition est légèrement différent de celui qu’effectue SQL Server. Pour en savoir plus à ce sujet, voir [Migrer votre code SQL vers SQL Data Warehouse][].


## Étapes suivantes
Une fois que vous avez migré le schéma de base de données vers SQL Data Warehouse, vous pouvez parcourir l’un des articles suivants :

- [Migrer vos données][]
- [Migrer votre code SQL vers SQL Data Warehouse][]

<!--Image references-->

<!-- Article references -->
[Migrer votre code SQL vers SQL Data Warehouse]: sql-data-warehouse-migrate-code.md
[Migrer vos données]: sql-data-warehouse-migrate-data.md
[Gérer des statistiques dans SQL Data Warehouse]: sql-data-warehouse-develop-statistics.md
[cet article]: sql-data-warehouse-develop-concurrency.md

<!-- MSDN Articles -->

<!-- Other web references -->

<!---HONumber=August15_HO6-->