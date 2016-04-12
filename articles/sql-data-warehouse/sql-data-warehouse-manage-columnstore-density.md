<properties
   pageTitle="Gérer le décalage de la distribution de table | Microsoft Azure"
   description="Conseils pour aider les utilisateurs à identifier le décalage de la distribution dans leurs tables distribuées"
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Gestion des index columnstore
Les index columnstore sont l’épine dorsale d’Azure SQL Data Warehouse. En conservant les index dans un état optimal, vous maximisez les performances de votre système.

Cet article explique comment interroger les métadonnées d’index columnstore de vos tables. Ce qui vous permet de diagnostiquer les problèmes et de les résoudre rapidement.

## Interrogation des métadonnées de Columnstore
Pour comprendre la densité de votre index columnstore, vous devez interroger les métadonnées système. Voici un exemple du type d’informations que vous pouvez obtenir.

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

Après avoir créé la vue, les métadonnées du columnstore peuvent être facilement analysées. Vous trouverez ci-dessous un exemple de requête.

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

## Amélioration de la densité de columnstore
Une fois que vous avez exécuté la requête, vous pouvez commencer à examiner les données et analyser vos résultats.

Plusieurs choses doivent être examinées :

| Colonne | Utilisation de ces données |
| ---------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [table\_partition\_count] | Si la table est partitionnée, vous pouvez vous attendre à un nombre plus élevé de groupes de lignes ouverts. Chaque partition de la distribution peut, en théorie, avoir un groupe de lignes ouvert associé. Tenez-en compte dans votre analyse. Une petite table qui a été partitionnée peut être optimisée en supprimant complètement le partitionnement pour améliorer la compression. |
| [row\_count\_total] | Nombre total de lignes de la table. Cette valeur peut être utilisée pour calculer le pourcentage de lignes compressées, par exemple |
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

## Gestion des index
Vous pouvez recompresser des groupes de lignes en créant les partitions à l’aide de [CTAS][] ou en reconstruisant l’index lui-même à l’aide de la commande [ALTER INDEX][]. La commande [CTAS][] est généralement plus rapide que [ALTER INDEX][], en particulier pour les tables ou partitions de grande taille. Toutefois, pour les tables ou partitions plus petites, [ALTER INDEX][] est souvent beaucoup plus pratique.

>[AZURE.NOTE] ALTER INDEX...REBUILD est une opération hors connexion. ALTER INDEX...REORGANISE est une opération en ligne. Toutefois, REORGANISE ne s’applique pas aux groupes de lignes ouverts. Pour inclure les groupes de lignes ouverts, utilisez ALTER INDEX...REBUILD.

La reconstruction d’index, en particulier les tables de grande taille, nécessite souvent des ressources supplémentaires. Azure SQL Data Warehouse comprend une fonctionnalité de gestion de la charge de travail qui peut être utilisée pour allouer davantage de mémoire à un utilisateur. Pour comprendre comment réserver plus de mémoire pour les reconstructions d’index, consultez la section sur la gestion de la charge de travail de l’article [Accès concurrentiel][].

## Étapes suivantes
Pour en savoir plus sur la recréation des partitions à l’aide de `CTAS`, consultez les articles suivants :

* [Partitionnement de table][]
* [accès concurrentiel][]

Pour obtenir d’informations sur la gestion des index, consultez l’article [Gérer les index][].

Pour plus d’informations sur la gestion, consultez la présentation de la [gestion][]

<!--Image references-->

<!--Article references-->
[CTAS]: sql-data-warehouse-develop-ctas.md
[Partitionnement de table]: sql-data-warehouse-develop-table-partitions.md
[Accès concurrentiel]: sql-data-warehouse-develop-concurrency.md
[gestion]: sql-data-warehouse-manage-monitor.md
[Gérer les index]: sql-data-warehouse-manage-indexes.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/fr-FR/library/ms188388.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->