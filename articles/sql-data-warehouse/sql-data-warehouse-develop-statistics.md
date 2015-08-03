<properties
   pageTitle="Gérer des statistiques dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la gestion de statistiques dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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

# Gérer des statistiques dans SQL Data Warehouse
 SQL Data Warehouse s’appuie sur des statistiques pour évaluer le coût de différentes méthodes d’exécution d’une requête distribuée. Lorsque les statistiques sont précises, l’optimiseur de requête peut générer des plans de requête de haute qualité, qui améliorent les performances des requêtes.

La création et la mise à jour des statistiques sont des étapes importantes pour l’amélioration des performances des requêtes prévue par la conception du logiciel SQL Data Warehouse. Ce guide présente les différentes statistiques et vous indique comment effectuer les opérations suivantes :

- créer des statistiques dans le cadre de la conception de la base de données ;
- mettre à jour ces statistiques lors de la maintenance de la base de données ;
- afficher les statistiques grâce à des fonctions et vues système.

## Présentation des statistiques

Les statistiques sur une colonne sont des objets contenant des informations sur la plage et la fréquence de valeurs dans une colonne unique. L’optimiseur de requête utilise cet histogramme pour estimer le nombre de lignes figurant dans le résultat de la requête. Cette évaluation a un impact direct sur le choix du mode d’optimisation associé à la requête.

Les statistiques sur plusieurs colonnes sont des données créées sur un ensemble de colonnes. Elles incluent les statistiques sur une colonne sur la première colonne de cet ensemble, en ajoutant certaines informations de corrélation entre les différentes colonnes, appelées « densités ». Les statistiques sur plusieurs colonnes peuvent améliorer les performances des requêtes lors de certaines opérations, comme les clauses « group by » et les associations composites.

Pour en savoir plus, consultez la section [DBCC SHOW_STATISTICS][] de MSDN.

## Pourquoi les statistiques sont-elles nécessaires ?
Si les statistiques utilisées ne sont pas pertinentes, vous ne pourrez pas atteindre les performances prévues par la conception de SQL Data Warehouse. Dans les tables et colonnes, les statistiques ne sont pas automatiquement générées par SQL Data Warehouse ; vous devez les créer vous-même. Il est judicieux de les créer en même temps que la table, puis de les mettre à jour une fois cette dernière remplie.

> [AZURE.NOTE]Si vous l’utilisez, vous pouvez demander à SQL Server de créer et de mettre à jour des statistiques sur une colonne pour vous, le cas échéant. À ce niveau, SQL Data Warehouse fonctionne différemment. Comme les données sont distribuées, SQL Data Warehouse ne rassemble pas automatiquement les statistiques sur l’ensemble des données distribuées. Il se contente de générer des statistiques agrégées lorsque vous créez et mettez ces statistiques à jour.

## À quel moment faut-il créer les statistiques ?
SQL Data Warehouse requiert avant tout un ensemble cohérent de données à jour. Il est donc important de créer des statistiques lors de la conception de vos tables.

Le processus de création de statistiques sur une colonne dans chaque colonne est un bon moyen de se familiariser avec la notion de statistiques. Toutefois, lorsque vous créez et mettez des statistiques à jour, vous devez toujours faire un compromis entre les performances et les coûts. Si vous créez des statistiques sur une colonne sur toutes les colonnes, avant de constater que la mise à jour de l’ensemble de ces statistiques prend trop de temps, vous pouvez toujours ignorer une partie d’entre elles ou isoler un sous-ensemble, que vous mettrez à jour plus souvent.

Les statistiques sur plusieurs colonnes sont uniquement utilisées par l’optimiseur de requête lorsque les colonnes sont des associations composites ou des clauses « group by ». Actuellement, les filtres composites ne bénéficient pas de statistiques sur plusieurs colonnes.

Pour cette raison, lors du démarrage de votre développement SQL Data Warehouse, il peut être pertinent d’implémenter le modèle suivant : 1) création de statistiques sur une colonne sur chaque colonne de chaque table ; 2) création de statistiques sur plusieurs colonnes sur les colonnes utilisées par les requêtes dans les clauses « join » et « group by ».

Lorsque vous choisissez le mode d’interrogation des données, vous pouvez être amené à affiner ce modèle, notamment en cas de tables volumineuses. Pour obtenir des informations sur une méthode plus avancée, voir [Implémentation de fonctions de gestion des statistiques](##Implémentation de fonctions de gestion des statistiques).

## À quel moment doit-on mettre à jour les statistiques ?
Il est important d’inclure la mise à jour des statistiques dans votre routine de gestion de base de données. Lorsque la distribution des données change dans la base de données, les statistiques doivent être mises à jour. Sinon, les performances des requêtes risquent d’être moins élevées et les efforts de dépannage plus avancés de la requête, peu pertinents.

Par conséquent, l’une des premières questions que vous devez vous poser lorsque vous dépannez une requête est la suivante : « Les statistiques sont-elles à jour ? »

Or, vous ne pouvez pas répondre à cette question en vous appuyant sur l’âge ; en effet, un objet de statistiques à jour peut être très ancien. Lorsque le nombre de lignes change ou que la distribution des valeurs est matériellement modifiée dans une colonne donnée, *alors* il est nécessaire de mettre à jour les statistiques.

Par exemple, les statistiques des colonnes de date d’un entrepôt de données doivent souvent être mises à jour. Chaque fois que de nouvelles lignes sont chargées dans l’entrepôt, de nouvelles dates de transaction et de chargement sont également ajoutées. Ces dernières affectent la distribution des données et rendent les statistiques obsolètes.

À l’inverse, les statistiques d’une colonne indiquant le sexe d’un client dans une table n’auront peut-être jamais besoin d’être mises à jour. Si l’on part du principe que la distribution des données est constante d’un client à l’autre, l’ajout de nouvelles lignes dans une table ne devrait pas affecter cette distribution. Toutefois, si votre entrepôt de données ne fait mention que d’un seul sexe et qu’une nouvelle exigence nécessite le recours à plusieurs sexes, vous devez absolument mettre à jour les statistiques de la colonne relative au sexe.

Pour en savoir plus, consultez la section [Statistiques][] de MSDN.

## Implémentation de fonctions de gestion des statistiques

Il est souvent judicieux d’étendre le processus de chargement des données, afin de vérifier que les statistiques sont mises à jour à la fin du chargement. Le chargement des données se produit lorsque la taille et/ou la distribution des valeurs sont souvent modifiées dans les tables. Par conséquent, il est logique d’implémenter certains processus de gestion à ce niveau.

Certains principes généraux sont fournis ci-dessous, afin de vous aider à mettre à jour vos statistiques pendant le processus de chargement :

- Assurez-vous que chaque table chargée présente au moins un objet de statistiques mis à jour. Cela met à jour les informations sur la taille des tables (nombre de lignes et de pages) dans le cadre du processus de mise à jour des statistiques.
- Concentrez-vous sur les colonnes participant aux clauses JOIN, GROUP BY, ORDER BY et DISTINCT.
- Envisagez de mettre plus souvent à jour les colonnes de clé croissante, comme celles des dates de transactions, car ces valeurs ne seront pas incluses dans l’histogramme des statistiques.
- Envisagez de mettre moins souvent à jour les colonnes de distribution statiques.
- N’oubliez pas que chaque objet de statistiques est mis à jour à son tour. L’implémentation de l’élément `UPDATE STATISTICS <TABLE_NAME>` peut ne pas suffire, notamment lorsque les tables sont volumineuses et incluent un grand nombre d’objets de statistiques.

> [AZURE.NOTE]Pour en savoir plus sur la [clé croissante], consultez le livre blanc relatif aux modèles d’évaluation de la cardinalité de SQL Server 2014.

Pour en savoir plus, consultez la section [Évaluation de la cardinalité][] de MSDN.

## Exemples de création de statistiques

Ces exemples indiquent comment utiliser différentes options pour créer des statistiques. Les options à utiliser pour chaque colonne dépendent des caractéristiques de vos données et de l’utilisation de la colonne dans les requêtes.

### A. Créer des statistiques sur une colonne en utilisant les options par défaut

Pour créer des statistiques sur une colonne, il vous suffit d’indiquer le nom de l’objet de statistiques, ainsi que celui de la colonne.

Cette syntaxe a recours à toutes les options par défaut. Par défaut, le logiciel SQL Data Warehouse utilise un échantillon représentant 20 % de la table lorsqu’il crée des statistiques.

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Par exemple :

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B. Créer des statistiques sur plusieurs colonnes en examinant chaque ligne

Le taux d’échantillonnage par défaut est de 20 %, ce qui est suffisant pour la plupart des situations. Toutefois, vous pouvez l’ajuster en fonction de vos besoins.

Pour échantillonner la table entière, utilisez la syntaxe suivante :

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Par exemple :

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C. Créer des statistiques sur une colonne en spécifiant la taille de l’échantillon

Vous pouvez également spécifier cette taille sous la forme d’un pourcentage :

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D. Créer des statistiques sur une colonne sur certaines lignes uniquement

Vous pouvez également créer des statistiques sur une partie des lignes de votre table. On parle alors de « statistiques filtrées ».

Par exemple, vous pouvez utiliser les statistiques filtrées lorsque vous prévoyez d’interroger une partition spécifique dans une table partitionnée volumineuse. En créant des statistiques sur les valeurs des partitions uniquement, vous améliorez la précision des statistiques et, par conséquent, les performances des requêtes.

Dans cet exemple, des statistiques sont créées sur une plage de valeurs. Les valeurs peuvent facilement être définies de manière à correspondre à la plage de valeurs d’une partition.

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]Pour que l’optimiseur de requête envisage d’utiliser les statistiques filtrées lorsqu’il choisit le plan de requête distribuée, il faut que cette requête soit suffisamment petite pour pouvoir s’insérer dans la définition de l’objet de statistiques. Selon l’exemple précédent, la clause WHERE de la requête doit indiquer des valeurs incluses entre 2000101 et 20001231 dans la colonne col1.

### E. Créer des statistiques sur une colonne en utilisant toutes les options

Bien sûr, vous pouvez combiner les options. L’exemple ci-dessous permet de créer un objet de statistiques filtrées avec une taille d’échantillon personnalisée :

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Pour accéder à la référence complète, consultez la section [CREATE STATISTICS][] de MSDN.

### F. Créer des statistiques sur plusieurs colonnes

Pour créer des statistiques sur plusieurs colonnes, il vous suffit d’utiliser les exemples précédents, en spécifiant davantage de colonnes.

> [AZURE.NOTE]L’histogramme, qui est utilisé pour estimer le nombre de lignes dans le résultat d’une requête, est uniquement disponible pour la première colonne répertoriée dans la définition d’objet de statistiques.

Dans cet exemple, l’histogramme concerne l’élément *product_category*. Les statistiques portant sur différentes colonnes sont calculées sur la base des éléments *product_category* et *product_sub_category* :

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Dans la mesure où il existe une corrélation entre les éléments *product_category* et *product_sub_category*, des statistiques sur plusieurs colonnes peuvent être utiles lorsque le système accède à ces colonnes en même temps.

### G. Créer des statistiques sur toutes les colonnes d’une table

Pour créer des statistiques, vous pouvez par exemple émettre des commandes CREATE STATISTICS après avoir créé la table.

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1;
CREATE STATISTICS stats_col2 on dbo.table2;
CREATE STATISTICS stats_col3 on dbo.table3;
```

### H. Utiliser une procédure stockée pour créer des statistiques sur toutes les colonnes d’une base de données

SQL Data Warehouse n’inclut pas de procédure stockée par le système équivalente à [sp_create_stats][] dans SQL Server. Cette procédure stockée crée un objet de statistiques sur une colonne portant sur chaque colonne de la base de données non pourvue de statistiques.

Cela vous aidera à commencer à concevoir votre base de données. N’hésitez pas à l’adapter à vos besoins.

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
WHERE       l.[object_id] IS NULL
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')'
        WHEN 2
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN'
        WHEN 3
        THEN    'CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT'
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Pour créer des statistiques sur toutes les colonnes de la table avec cette procédure, il vous suffit d’appeler cette dernière.

```
prc_sqldw_create_stats;
```

## Exemple de mise à jour des statistiques

Pour effectuer cette opération, vous avez différentes possibilités :

1. Mettez à jour un objet de statistiques. Spécifiez le nom de l’objet de statistiques que vous souhaitez mettre à jour.
2. Mettez à jour tous les objets de statistiques sur une table. Spécifiez le nom de la table, et non un objet de statistiques spécifique.


### A. Mettre à jour un objet de statistiques spécifique ###
Pour réaliser cette opération, utilisez la syntaxe suivante :

```
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Par exemple :

```
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

En mettant à jour des objets de statistiques spécifiques, vous pouvez réduire les ressources et le temps requis pour gérer les statistiques. Toutefois, la sélection des objets de statistiques les plus appropriés pour une mise à jour prend du temps.


### B. Mettre à jour tous les objets de statistiques dans une table ###
Voici une méthode simple pour mettre à jour tous les objets de statistiques dans une table.

```
UPDATE STATISTICS [schema_name].[table_name];
```

Par exemple :

```
UPDATE STATISTICS dbo.table1;
```

Cette instruction est facile à utiliser. N’oubliez pas que cette action met à jour toutes les statistiques dans la table et, par conséquent, peut effectuer davantage de tâches que nécessaire. Si les performances ne sont pas un problème, il s’agit sans aucun doute de la méthode la plus simple et la plus exhaustive pour garantir que les statistiques sont à jour.

> [AZURE.NOTE]Lors de la mise à jour de toutes les statistiques d’une table, SQL Data Warehouse procède à une analyse pour échantillonner la table, afin de rechercher chaque statistique. Si la table est volumineuse et comprend un grand nombre de colonnes et de statistiques, il peut s’avérer plus efficace de mettre à jour les statistiques au niveau individuel, en fonction des besoins.

Pour en savoir plus sur l’implémentation d’une procédure `UPDATE STATISTICS`, consultez l’article relatif aux [tables temporaires]. La méthode d’implémentation est légèrement différente de celle de la procédure `CREATE STATISTICS` ci-dessus, mais le résultat final est le même.

Pour accéder à la syntaxe complète, consultez la section [UPDATE STATISTICS][] de MSDN.

## Métadonnées de statistiques
Vous pouvez utiliser plusieurs fonctions et vues système pour rechercher des informations sur des statistiques. Par exemple, vous pouvez voir si un objet de statistiques peut-être obsolète à l’aide de la fonction « stats-date » (qui permet de connaître la date de création ou de dernière mise à jour des statistiques).

### Vues de catalogue des statistiques
Ces vues système fournissent des informations sur les statistiques :

| Vue de catalogue | Description |
| :----------- | :---------- |
| [sys.columns][] | Une ligne pour chaque colonne. |
| [sys.objects][] | Une ligne pour chaque objet de la base de données. | |
| [sys.schemas][] | Une ligne pour chaque schéma de la base de données. | |
| [sys.stats][] | Une ligne pour chaque objet de statistiques. |
| [sys.stats_columns][] | Une ligne pour chaque colonne de l’objet de statistiques. Paramètre lié à l’élément « sys.columns ». |
| [sys.tables][] | Une ligne pour chaque table (y compris les tables externes). |
| [sys.table_types][] | Une ligne pour chaque type de données. |


### Fonctions système relatives aux statistiques
Ces fonctions système sont utiles lorsque vous gérez des statistiques :

| Fonction système | Description |
| :-------------- | :---------- |
| [STATS_DATE][] | Date de la dernière mise à jour de l’objet de statistiques. |
| [DBCC SHOW_STATISTICS][] | Fournit des informations détaillées et récapitulatives sur la distribution des valeurs, telles que l’objet de statistiques la comprend. |

### Combiner des fonctions et des colonnes de statistiques en une seule vue

Cette vue regroupe les colonnes portant sur les statistiques et les résultats de la fonction [STATS_DATE()][].

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     sts.[user_created] = 1
;
```

## Exemples portant sur la fonction DBCC SHOW_STATISTICS()

La fonction DBCC SHOW_STATISTICS() présente les données contenues dans un objet de statistiques. Ces données sont affichées en trois parties.

1. En-tête
2. Vecteur de densité
3. Histogramme

Métadonnées de l’en-tête sur les statistiques. L’histogramme affiche la distribution des valeurs dans la première colonne de l’objet de statistiques. Le vecteur de densité mesure la corrélation entre les colonnes. SQLDW calcule les évaluations de cardinalité avec certaines données dans l’objet de statistiques.

### Afficher l’en-tête, la densité et l’histogramme

Cet exemple simple illustre les trois parties d’un objet de statistiques.

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Par exemple :

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### Afficher une ou plusieurs parties de la fonction DBCC SHOW_STATISTICS();

Si vous êtes uniquement intéressé par l’affichage de certaines parties spécifiques, utilisez la clause `WITH` et spécifiez les parties que vous voulez voir :

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Par exemple :

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## Différences liées à la fonction DBCC SHOW_STATISTICS()
La fonction DBCC SHOW_STATISTICS() est implémentée de manière plus stricte dans SQLDW que dans SQL Server.

1. Les fonctions non documentées ne sont pas prises en charge.
- Impossible d’utiliser le paramètre « Stats_stream ».
- Impossible de joindre les résultats de sous-ensembles spécifiques de données de statistiques, par exemple : STAT_HEADER JOIN DENSITY_VECTOR.
2. L’élément NO_INFOMSGS ne peut pas être défini pour la suppression des messages.
3. Vous ne pouvez pas placer de crochets autour des noms de statistiques.
4. Vous ne pouvez pas utiliser les noms de colonnes pour identifier les objets de statistiques.
5. L’erreur personnalisée 2767 n’est pas prise en charge.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, consultez la rubrique [Vue d’ensemble sur le développement SQL Data Warehouse][].

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[Vue d’ensemble sur le développement SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[tables temporaires]: ./sql-data-warehouse-develop-temporary-tables/

<!-- External Links -->
[Évaluation de la cardinalité]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[Statistiques]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=July15_HO4-->