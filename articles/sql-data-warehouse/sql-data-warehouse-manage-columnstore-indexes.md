<properties
   pageTitle="Gérer les index columnstore dans Azure SQL Data Warehouse | Microsoft Azure"
   description="Didacticiel pour la gestion des index columnstore pour optimiser la compression et les performances de requête dans Azure SQL Data Warehouse."
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
   ms.date="04/07/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gérer les index columnstore dans Azure SQL Data Warehouse

Ce didacticiel explique comment gérer des index columnstore pour améliorer les performances de requête.

Les requêtes sur les index columnstore s’exécutent de manière idéale lorsque l’index compresse les lignes en « groupes de lignes » d’un million de lignes (1 048 576 exactement). Cette compression optimale engendre des performances de requête optimales. Toutefois, certaines conditions peuvent se produire dans lesquelles les groupes de lignes contiennent un nombre très inférieur à un million de lignes. Lorsque les groupes de lignes ne contiennent pas un nombre important de lignes, envisagez d’effectuer des ajustements.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

- Utilisation des métadonnées pour déterminer le nombre moyen de lignes par groupe de lignes
- Recherche de la cause racine de groupes de lignes peu remplis
- Reconstruction d’un index columnstore pour recompresser toutes les lignes en nouveaux groupes de lignes 

Pour prendre en main les index columnstore, consultez [Columnstore Guide](https://msdn.microsoft.com/library/gg492088.aspx) (Prise en main des columnstore).

## Étape 1 : créer une vue qui affiche les métadonnées de groupes de lignes

Cette vue calcule les lignes moyennes par groupe de lignes. Elle affiche également des informations supplémentaires sur les groupes de lignes.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
WITH CSI
AS
(
SELECT
        SUBSTRING(@@version,34,4)                                               AS [build_number]
,       GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       t.name                                                                  AS [table_name]
,		COUNT(DISTINCT rg.[partition_number])									AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,		CEILING	(	(SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id])
						)/1048576
				)																AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
GROUP BY
        t.[name]
)
SELECT  *
FROM    CSI
;
```

## Étape 2 : interroger la vue

Une fois la vue créée, exécutez cet exemple de requête pour afficher les métadonnées de groupes de lignes pour l’index columnstore.

```sql
SELECT	[table_name]
,		[table_partition_count]
,		[row_count_total]
,		[row_count_per_distribution_MAX]
,		[COMPRESSED_rowgroup_rows]
,		[COMPRESSED_rowgroup_rows_AVG]
,		[COMPRESSED_rowgroup_rows_DELETED]
,		[COMPRESSED_rowgroup_count]
,		[OPEN_rowgroup_count]
,		[OPEN_rowgroup_rows]
,		[CLOSED_rowgroup_count]
,		[CLOSED_rowgroup_rows]
FROM	[dbo].[vColumnstoreDensity]
WHERE	[table_name] = 'FactInternetSales'
```

## Étape 3 : analyser les résultats

Une fois que vous avez exécuté la requête, vous pouvez commencer à examiner les données et analyser vos résultats. Cette table explique ce que vous devez rechercher dans votre analyse de groupe de lignes.


| des colonnes | Utilisation de ces données |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Si la table est partitionnée, vous pouvez vous attendre à un nombre plus élevé de groupes de lignes ouverts. Chaque partition de la distribution peut, en théorie, avoir un groupe de lignes ouvert associé. Tenez-en compte dans votre analyse. Une petite table qui a été partitionnée peut être optimisée en supprimant complètement le partitionnement pour améliorer la compression. |
| [row\_count\_total] | Nombre total de lignes de la table. Par exemple, vous pouvez utiliser cette valeur pour calculer le pourcentage de lignes à l’état compressé. |
| [row\_count\_per\_distribution\_MAX] | Si toutes les lignes sont réparties uniformément, cette valeur est le nombre cible de lignes par distribution. Comparez cette valeur avec compressed\_rowgroup\_count. |
| [COMPRESSED\_rowgroup\_rows] | Nombre total de lignes au format columnstore pour la table |
| [COMPRESSED\_rowgroup\_rows\_AVG] | Si le nombre moyen de lignes est considérablement inférieur au nombre maximal de lignes pour un groupe de lignes, utilisez CTAS ou ALTER INDEX REBUILD pour recompresser les données |
| [COMPRESSED\_rowgroup\_count] | Nombre de groupes de lignes au format columnstore. Si ce nombre est très élevé par rapport à la table, il indique que la densité de columnstore est faible. |
| [COMPRESSED\_rowgroup\_rows\_DELETED] | Les lignes sont logiquement supprimées au format columnstore. Si le nombre est élevé par rapport à la taille de la table, recréez la partition ou reconstruisez l’index, car cette opération les supprime physiquement. |
| [COMPRESSED\_rowgroup\_rows\_MIN] | Utilisez cette valeur avec les colonnes AVG et MAX pour comprendre la plage de valeurs des groupes de lignes dans votre columnstore. Un nombre bas au-dessus du seuil de chargement (102 400 par distribution alignée sur la partition) suggère que des optimisations sont disponibles dans le chargement des données |
| [COMPRESSED\_rowgroup\_rows\_MAX] | Identique à ce qui précède |
| [OPEN\_rowgroup\_count] | Les groupes de lignes ouverts sont normaux. On peut raisonnablement s’attendre à un groupe de lignes ouvert par distribution de tables (60). Les nombres excessifs suggèrent un chargement des données sur plusieurs partitions de données. Vérifiez la stratégie de partitionnement pour vous assurer qu’elle est saine |
| [OPEN\_rowgroup\_rows] | Chaque groupe de lignes peut contenir 1 048 576 lignes maximum. Utilisez cette valeur pour connaître l’état de remplissage actuel des groupes de lignes ouverts |
| [OPEN\_rowgroup\_rows\_MIN] | Les groupes ouverts indiquent que les données sont chargées de manière progressive dans la table ou que la charge précédente a débordé sur les lignes restantes de ce groupe de lignes. Utilisez les colonnes MIN, MAX et AVG pour afficher la quantité de données stockées dans les groupes de ligne ouverts. Pour les petites tables, il peut s’agir de 100 % des données. Dans ce cas, l’instruction ALTER INDEX REBUILD force les données dans le columnstore. |
| [OPEN\_rowgroup\_rows\_MAX] | Identique à ce qui précède |
| [OPEN\_rowgroup\_rows\_AVG] | Identique à ce qui précède |
| [CLOSED\_rowgroup\_rows] | Examinez les lignes des groupes de lignes fermés pour effectuer un contrôle de validité. Si présent |
| [CLOSED\_rowgroup\_count] | S’il existe des groupes de lignes fermés, ils doivent être en petit nombre. Les groupes de lignes fermés peuvent être convertis en groupes de lignes compressés à l’aide de la commande ALTER INDEX... REORGANISE. Toutefois, cela n’est généralement pas nécessaire. Les groupes fermés sont automatiquement convertis en groupes de lignes du columnstore par le processus « moteur de tuple » en arrière-plan. |
| [CLOSED\_rowgroup\_rows\_MIN] | Les groupes de lignes fermés doivent avoir un taux de remplissage très élevé. Si le taux de remplissage d’un groupe de lignes fermé est faible, une analyse plus approfondie du columnstore est nécessaire. |
| [CLOSED\_rowgroup\_rows\_MAX] | Identique à ce qui précède |
| [CLOSED\_rowgroup\_rows\_AVG] | Identique à ce qui précède |


## Étape 4 : examiner la cause racine

L’examen de la cause racine vous permet de savoir si vous pouvez modifier la conception de tables, le chargement et d’autres processus afin d’augmenter la densité des lignes de vos groupes de lignes ; cela optimise la compression et les performances de requête.

En raison de ces facteurs, le nombre de lignes dans chaque groupe de lignes de l’index columnstore peut être très inférieur à 1 048 576 lignes. Ces facteurs peuvent également entraîner le transfert des lignes vers un groupe de lignes delta au lieu d’un groupe de lignes compressé.

1. Opérations DML lourdes
2. Opérations de chargement progressives ou légères
3. Nombre trop important de partitions

### Opérations DML lourdes** 

Les opérations DML lourdes qui mettent à jour et suppriment des lignes provoquent l’inefficacité du columnstore. Cela est particulièrement vrai lorsque la majorité des lignes dans un groupe de lignes est modifiée.

- La suppression d’une ligne dans un groupe de lignes compressé ne fait que marquer logiquement la ligne comme étant supprimée. La ligne reste dans le groupe de lignes compressé jusqu’à ce que la partition ou la table soit reconstruite.
- L’insertion d’une ligne ajoute cette dernière à une table de groupe de lignes interne appelée groupe de lignes delta. La ligne insérée n’est pas convertie en columnstore jusqu’à ce que le groupe de lignes delta soit rempli et marqué comme étant fermé. Les groupes de lignes sont fermés lorsqu’ils atteignent leur capacité maximale de 1 048 576 lignes. 
- La mise à jour d’une ligne au format columnstore est traitée en tant que suppression logique, puis en tant qu’insertion. La ligne insérée est stockée dans le deltastore.

Les opérations de mise à jour par lot et d’insertion qui dépassent le seuil de 102 400 lignes par distribution alignée sur la partition seront écrites directement au format columnstore. Toutefois, pour que cela se produise et en supposant que la répartition soit uniforme, vous devriez modifier plus de 6,144 millions de lignes en une seule opération. Si le nombre de lignes d’une distribution alignée sur la partition donnée est inférieur à 102 400, les lignes seront transférées au deltastore et y resteront jusqu’à ce que l’une des trois conditions suivantes ait été remplie : un nombre suffisant de lignes a été inséré ; un nombre suffisant de lignes a été modifié pour pouvoir fermer le groupe de lignes ; l’index a été reconstruit.

### Opérations de chargement progressives ou légères

Les charges légères entrant dans SQL Data Warehouse sont parfois appelées charges progressives. Elles représentent généralement un flux presque constant de données que le système reçoit. Toutefois, comme ce flux est presque continu, le volume des lignes n’est pas forcément important. La plupart du temps, les données sont bien inférieures au seuil requis pour une charge directe au format columnstore.

Dans ces situations, il est souvent préférable d’envoyer les données dans Azure Blob Storage et de les laisser s’accumuler avant le chargement. Cette technique est souvent appelée *micro-batching* ou micro-traitement par lots.

### Nombre trop important de partitions

Vous pourriez avoir un nombre trop important de partitions. En arrière-plan, une table définie par l’utilisateur est distribuée et implémentée comme 60 tables dans l’architecture MPP (Massively Parallel Processing). Chaque index columnstore est donc implémenté sous la forme de 60 index columnstore. De même, chaque partition est implémentée sur ces 60 index columnstore. Cela **diffère grandement** de SQL Server, qui dispose d’une architecture SMP (multitraitement symétrique).

Si vous chargez 1 048 576 lignes dans une partition d’une table SQL Server SMP, celle-ci sera compressée dans le columnstore. Toutefois, si vous chargez 1 048 576 lignes dans une partition d’une table SQL Data Warehouse, chacune des 60 distributions accueillera 17 467 lignes (en supposant une répartition égale des données). Étant donné que 17 467 est un nombre inférieur au seuil de 102 400 lignes par distribution, SQL Data Warehouse conservera les données dans les groupes de lignes du deltastore. Par conséquent, pour charger les lignes directement au format columnstore compressé, vous devez charger plus de 6,144 millions de lignes (60 x 102 400) dans une seule partition d’une table SQL Data Warehouse.

## Étape 5 : affecter des ressources de calcul supplémentaires

La reconstruction d’index, en particulier les tables de grande taille, nécessite souvent des ressources supplémentaires. SQL Data Warehouse comprend une fonctionnalité de gestion de la charge de travail qui peut être utilisée pour allouer davantage de mémoire à un utilisateur. Pour comprendre comment réserver plus de mémoire pour les reconstructions d’index, consultez la section sur la gestion de la charge de travail de l’article [Gestion de la concurrence et des charges de travail dans SQL Data Warehouse][].

## Étape 6 : reconstruire l’index pour optimiser les lignes moyennes par groupe de lignes

Pour fusionner les groupes de lignes compressés existants et forcer les groupes de lignes delta dans le columnstore, vous devez reconstruire l’index. En règle générale, les données sont trop importantes pour pouvoir reconstruire l’index complet ; il est préférable de reconstruire une ou plusieurs partitions. Dans SQL Data Warehouse, vous pouvez reconstruire l’index de l’une des deux façons suivantes :

1. Utilisez [ALTER INDEX][] pour reconstruire une partition.
2. Utilisez [CTAS][] pour recréer une partition dans une nouvelle table, puis utilisez le basculement de partitions pour ramener la partition dans la table d’origine.

Quelle méthode est la meilleure ? Pour les gros volumes de données, [CTAS][] est généralement plus rapide que [ALTER INDEX][]. Pour les volumes de données plus petits, [ALTER INDEX][] est plus facile à utiliser.

### Méthode 1 : utilisez ALTER INDEX pour reconstruire de petits volumes de données hors connexion

Ces exemples illustrent comment reconstruire l’index columnstore entier et une partition unique. Sur des tables volumineuses, la reconstruction d’une partition unique est la seule option pratique. Cette opération se fait hors connexion.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

Pour plus d’informations, consultez la section ALTER INDEX REBUILD dans [Columnstore Indexes Defragmentation](https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1) (Défragmentation d’index columnstore) et la rubrique de syntaxe [ALTER INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188388.aspx).

### Méthode 2 : utilisez CTAS pour reconstruire et basculer les partitions de volumes importants de données en ligne

Cet exemple utilise [CTAS][] et le basculement de partitions pour reconstruire une partition de table.


```sql
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

Pour plus d’informations sur la recréation de partitions à l’aide de `CTAS`, consultez les articles [Partitions de tables dans SQL Data Warehouse][] et [Gestion de la concurrence et des charges de travail dans SQL Data Warehouse][].


## Étapes suivantes

Pour obtenir plus d’informations sur la gestion des index, consultez l’article [Gérer les index][].

Pour plus de conseils sur la gestion, consultez la présentation de la [gestion][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Partitions de tables dans SQL Data Warehouse]: sql-data-warehouse-develop-table-partitions.md
[Gestion de la concurrence et des charges de travail dans SQL Data Warehouse]: sql-data-warehouse-develop-concurrency.md
[gestion]: sql-data-warehouse-manage-monitor.md
[Gérer les index]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0413_2016-->