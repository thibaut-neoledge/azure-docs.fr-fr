<properties
   pageTitle="Partitionnement de tables dans SQL Data Warehouse | Microsoft Azure"
   description="Prise en main du partitionnement de tables dans Azure SQL Data Warehouse."
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
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Partitionnement de tables dans SQL Data Warehouse

> [AZURE.SELECTOR]
- [Vue d'ensemble][]
- [Types de données][]
- [Distribuer][]
- [Index][]
- [Partition][]
- [Statistiques][]
- [Temporaire][]

Le partitionnement est pris en charge sur tous les types de table SQL Data Warehouse, notamment columnstore en cluster, index en cluster et segment de mémoire. Le partitionnement est également pris en charge sur tous les types de distribution, notamment le hachage ou le tourniquet (round robin) distribué. Le partitionnement vous permet de diviser vos données en groupes de données plus restreints et, dans la plupart des cas, il est effectué sur une colonne de date.

## Avantages du partitionnement

Le partitionnement peut bénéficier de la maintenance des données et des performances des requêtes. Le fait qu’il bénéficie à ces deux éléments ou à un seul dépend de la façon dont les données sont chargées et du fait que la même colonne puisse être utilisée ou non dans les deux cas, étant donné que le partitionnement peut uniquement être effectué sur une colonne.

### Avantages pour les charges

Le principal avantage du partitionnement dans SQL Data Warehouse est d’améliorer l’efficacité et les performances de chargement de données en utilisant la suppression, le basculement et la fusion de partitions. Dans la plupart des cas, les données sont partitionnées sur une colonne de date qui est étroitement liée à la séquence sur laquelle les données sont chargées dans la base de données. L’un des avantages majeurs de l’utilisation de partitions pour la maintenance des données et qu’elle évite la journalisation des transactions. Bien que l’insertion, la mise à jour ou la suppression de données constituent l’approche la plus simple, avec un peu de réflexion et d’efforts, l’utilisation du partitionnement pendant votre processus de partitionnement peut considérablement améliorer les performances.

Le basculement de partitions peut servir à supprimer ou à remplacer rapidement une section d’une table. Par exemple, une table de faits des ventes peut contenir seulement des données pour les 36 derniers mois. À la fin de chaque mois, le mois de données de ventes le plus ancien est supprimé de la table. Ces données ont pu être supprimées à l’aide d’une instruction delete pour supprimer les données pour le mois plus ancien. Toutefois, la suppression d’une grande quantité de données ligne par ligne avec une instruction delete peut prendre beaucoup de temps, ainsi que créer le risque de transactions volumineuses qui pourraient nécessiter un temps de restauration considérable en cas de problème. Une approche plus optimale consiste à simplement supprimer la partition de données la plus ancienne. Alors que la suppression des lignes individuelle peut prendre des heures, la suppression d’une partition complète ne prend que secondes.

### Avantages pour les requêtes

Le partitionnement peut également servir à améliorer les performances des requêtes. Si une requête applique un filtre sur une colonne partitionnée, cela peut limiter l’analyse aux seules partitions éligibles, qui peuvent constituer un sous-ensemble de données bien plus restreint, en évitant une analyse de table complète. Avec l’introduction des index columnstore en cluster, les avantages des performances de l’élimination de prédicats sont moins notables, mais dans certains cas, il peut exister un avantage pour les requêtes. Par exemple, si la table de faits de ventes est partagée en 36 mois grâce au champ de date des ventes, alors les requêtes filtrées par date de vente peuvent ignorer la recherche dans les partitions de traitement qui ne correspondent pas au filtre.

## Guide de dimensionnement de partition

Tandis que le partitionnement peut être utilisé pour améliorer les performances de certains scénarios, la création d’une table avec **trop** de partitions peut nuire aux performances dans certaines circonstances. Ces inquiétudes sont particulièrement avérées pour les tables columnstore en cluster. Pour que le partitionnement soit utile, il est important de savoir quand utiliser le partitionnement et le nombre de partitions à créer. Il n’existe aucune règle absolue concernant le nombre de partitions ; cela dépend de vos données et du nombre de partitions que vous chargez simultanément. Toutefois, en règle générale, envisagez d’ajouter des dizaines, voire des centaines de partitions, mais pas des milliers.

Lorsque vous créez le partitionnement sur des tables **columnstore en cluster**, il est important de prendre en compte le nombre de lignes qui arrivera dans chaque partition. Pour une compression et des performances des tables columnstore en cluster optimales, un minimum de 1 million de lignes par partition et par distribution est nécessaire. Avant la création des partitions, SQL Data Warehouse divise déjà chaque table en 60 bases de données distribuées. Tout partitionnement ajouté à une table est en plus des distributions créées en arrière-plan. Dans cet exemple, si la table de faits de ventes contient 36 partitions mensuelles, et étant donné que SQL Data Warehouse comporte 60 distributions, la table de faits de ventes doit contenir 60 millions de lignes par mois, ou 2,1 milliards de lignes lorsque tous les mois sont remplis. Si une table contient beaucoup moins de lignes que le nombre minimum de lignes recommandé par partition, envisagez d’utiliser moins de partitions pour augmenter le nombre de lignes par partition. Consultez également l’article sur [l’indexation][Index] qui comporte les requêtes pouvant être exécutées sur SQL Data Warehouse afin d’évaluer la qualité des index columnstore en cluster.

## Différence de syntaxe à partir de SQL Server

SQL Data Warehouse présente une définition simplifiée de partitions qui diffère légèrement de celle de SQL Server. Les fonctions et les schémas de partitionnement ne sont pas utilisés dans SQL Data Warehouse, car ils se trouvent dans SQL Server. À la place, il vous suffit d’identifier la colonne partitionnée et les points de limite. Bien que la syntaxe de partitionnement puisse être légèrement différente de SQL Server, les concepts de base sont les mêmes. SQL Server et SQL Data Warehouse prennent en charge une colonne de partition par table, qui peut être une partition par plage. Pour en savoir plus sur le partitionnement, consultez [Tables et index partitionnés][].

Exemple ci-dessous d’une instruction [CREATE TABLE][] partitionnée par SQL Data Warehouse, partitions de la table FactInternetSales sur la colonne OrderDateKey :

```sql
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
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## Migration du partitionnement dans SQL Server

Pour migrer les définitions des partitions SQL Server vers SQL Data Warehouse, procédez simplement comme suit :

- Supprimez le [schéma de partition][] SQL Server.
- Ajoutez la définition de la [fonction de partition][] à votre instruction CREATE TABLE.

Si vous migrez une table partitionnée à partir d’une instance SQL Server, le SQL ci-dessous peut vous aider à interroger le nombre de lignes se trouvant dans chaque partition. N’oubliez pas que si la même granularité de partitionnement est utilisée sur SQL Data Warehouse, le nombre de lignes par partition diminue d’un facteur de 60.

```sql
-- Partition information for a SQL Server Database
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
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
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

## Gestion des charges de travail

Dernier facteur à prendre en compte en ce qui concerne les partitions de tables, la [gestion des charges de travail][]. La gestion des charges de travail dans SQL Data Warehouse consiste essentiellement en la gestion de mémoire et d’accès concurrentiel. Dans SQL Data Warehouse, la quantité de mémoire maximale allouée à chaque distribution lors de l’exécution des requêtes est définie par les classes de ressources. Dans l’idéal, vos partitions seront dimensionnées en tenant compte d’autres facteurs tels que les besoins en mémoire pour la création d’index columnstore en cluster. Les index columnstore en cluster présentent des avantages non négligeables lorsque davantage de mémoire leur est allouée. Vous devez donc vous assurer que la mémoire disponible pour une reconstruction d’index de partition est suffisante. Pour augmenter la quantité de mémoire disponible pour votre requête, vous devez passer du rôle par défaut, smallrc, à d’autres rôles, par exemple, largerc.

Si vous voulez obtenir des informations sur l’allocation de la mémoire pour chaque distribution, interrogez les vues de gestion dynamique du gouverneur de ressources. En réalité, votre allocation de mémoire sera inférieure aux nombres indiqués ci-dessous. Toutefois, cette figure peut vous aider à dimensionner vos partitions lors d’opérations de gestion des données. Évitez de dimensionner vos partitions en affectant une taille supérieure à la mémoire octroyée que fournit la classe de ressources de très grande taille. Si la taille de vos partitions dépasse cette valeur, vous risquez de solliciter la mémoire de manière trop intensive, ce qui peut nuire à l’efficacité de la compression.

```sql
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
;
```

## Basculement de partitions

SQL Data Warehouse prend en charge le fractionnement, la fusion et le basculement de partition. Chacune de ces fonctions est exécutée à l’aide de l’instruction [ALTER TABLE][].

Pour faire basculer une partition d’une table à une autre, vous devez vous assurer que les partitions s’alignent sur leurs limites respectives et que les définitions de tables correspondent. Comme aucune contrainte CHECK n’est disponible pour appliquer la plage de valeurs dans une table, la table source doit contenir les mêmes limites de partition que la table cible. Dans le cas contraire, le basculement de la partition échoue, car les métadonnées de celle-ci ne sont pas synchronisées.

### Fractionnement d’une partition contenant des données

Pour fractionner une partition qui contient déjà des données, la méthode la plus efficace consiste à utiliser une instruction `CTAS`. Si la table partitionnée est un CCI, la partition de table doit être vide pour pouvoir être fractionnée.

Voici un exemple de table columnstore partitionnée qui inclut une ligne dans chaque partition :

```sql
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
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] En créant l’objet de statistiques, nous favorisons la précision des métadonnées des tables. Si ces statistiques ne sont pas créées, SQL Data Warehouse utilise les valeurs par défaut. Pour en savoir plus sur les statistiques, voir [Gérer des statistiques dans SQL Data Warehouse][].

Nous pouvons ensuite rechercher le nombre de lignes utilisant la vue de catalogue `sys.partitions` :

```sql
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
;
```

Si nous essayons de fractionner cette table, l’erreur suivante s’affiche :

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Le message 35346, au niveau 15, état 1, ligne 44, indique que la clause SPLIT de l’instruction ALTER PARTITION a échoué, car la partition n’est pas vide. Seules les partitions vides peuvent faire l’objet d’un fractionnement lorsque la table inclut un index columnstore. Vous pouvez envisager de désactiver l’index columnstore avant de lancer l’instruction ALTER PARTITION, puis de reconstruire l’index columnstore une fois l’opération ALTER PARTITION terminée.

Cependant, nous pouvons utiliser la commande `CTAS` pour créer une table afin de stocker nos données.

```sql
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
FROM    FactInternetSales
WHERE   1=2
;
```

Comme les limites de la partition sont alignées, le basculement est autorisé. La table source présente donc une partition vide, que nous pouvons fractionner.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Il ne nous reste plus qu’à aligner nos données en fonction des limites de la nouvelle partition, via la commande `CTAS`, et à faire basculer nos données une nouvelle fois vers la table principale.

```sql
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
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Une fois le déplacement des données effectué, il est judicieux d’actualiser les statistiques sur la table cible, afin de vérifier qu’elles reflètent de manière précise la nouvelle distribution des données sur leurs partitions respectives.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### Contrôle de code source dans le cadre du partitionnement d’une table

Pour éviter que la définition de votre table ne **se corrompe** dans votre système de contrôle du code source, vous pouvez procéder comme suit :

1. Créez la table en tant que table partitionnée, mais sans ajouter de valeurs de partition.

```sql
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

2. Appliquez à la table la commande `SPLIT` lors du processus de déploiement :

```sql
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
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
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

## Étapes suivantes

Pour plus d’informations, consultez les articles [Table Overview][Overview] \(Vue d’ensemble des tables), [Table Data Types][Data Types] \(Types de données de table), [Distributing a Table][Distribute] \(Distribution d’une table), [Indexing a Table][Index] \(Indexation d’une table), [Maintaining Table Statistics][Statistics] \(Maintenance des statistiques de table) et [Tables temporaires][Temporary]. Pour en savoir plus sur les meilleures pratiques, consultez [Meilleures pratiques relatives à SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Vue d'ensemble]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Distribuer]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Gérer des statistiques dans SQL Data Warehouse]: ./sql-data-warehouse-tables-statistics.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Temporaire]: ./sql-data-warehouse-tables-temporary.md
[gestion des charges de travail]: ./sql-data-warehouse-develop-concurrency.md
[Meilleures pratiques relatives à SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Tables et index partitionnés]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/fr-FR/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[fonction de partition]: https://msdn.microsoft.com/library/ms187802.aspx
[schéma de partition]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->

<!---HONumber=AcomDC_0720_2016-->
