<properties
   pageTitle="Tables temporaires dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de tables temporaires dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="06/06/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Tables temporaires dans SQL Data Warehouse
Les tables temporaires sont très utiles lors du traitement des données, notamment lors d’une transformation, lorsque les résultats intermédiaires sont temporaires. Les tables temporaires se trouvent au niveau de la session dans SQL Data Warehouse. Toutefois, elles sont toujours définies en tant que tables temporaires locales. Contrairement aux tables SQL Server, elles sont accessibles depuis n’importe quel point de la session.

Cet article contient des conseils de base pour l’utilisation des tables temporaires et met en évidence les principes des tables temporaires au niveau de la session. Ces informations peuvent vous aider à modulariser votre code. La modularité du code est importante pour faciliter sa maintenance et sa réutilisation.

## Création de tables temporaires
La création d’une table temporaire est très simple. Il vous suffit d’ajouter # en préfixe au nom de la table, comme dans l’exemple ci-dessous :

```sql
CREATE TABLE #stats_ddl
(
	[schema_name]			NVARCHAR(128) NOT NULL
,	[table_name]            NVARCHAR(128) NOT NULL
,	[stats_name]            NVARCHAR(128) NOT NULL
,	[stats_is_filtered]     BIT           NOT NULL
,	[seq_nmbr]              BIGINT        NOT NULL
,	[two_part_name]         NVARCHAR(260) NOT NULL
,	[three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
```

Vous pouvez également utiliser `CTAS` pour créer des tables temporaires à l’aide de la même approche.

```sql
CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
,	HEAP
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS` est une commande très puissante et présente l’avantage d’être très efficace dans son utilisation de l’espace de journal des transactions.


## Suppression de tables temporaires

Pour vous assurer que vos instructions `CREATE TABLE` réussissent, il est important de vous assurer que la table n’existe pas déjà dans la session. Pour ce faire, utilisez une vérification d’existence préalable simple avec le modèle suivant :

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END
```

> [AZURE.NOTE] Pour la cohérence de codage, il est recommandé d’utiliser ce modèle pour les tables et les tables temporaires.

Il est également judicieux d’utiliser `DROP TABLE` pour supprimer les tables temporaires lorsque vous avez terminé de les utiliser dans votre code.

```sql
DROP TABLE #stats_ddl
```

Dans le développement de procédure stockée, il est assez courant de voir les commandes de suppression regroupées ensemble à la fin d’une procédure pour s’assurer que ces objets sont nettoyés.

## Modularisation du code

La possibilité d’afficher des tables temporaires depuis n’importe quel point d’une session utilisateur peut vous aider à modulariser votre code d’application.

Prenons un exemple pratique.

La procédure stockée ci-dessous réunit les exemples mentionnés ci-dessus. Le code peut être utilisé pour générer le DDL nécessaire pour mettre à jour les statistiques dans chaque colonne de la base de données :

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
	,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
	DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
	DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
		sm.[name]				                                                AS [schema_name]
,		tb.[name]				                                                AS [table_name]
,		st.[name]				                                                AS [stats_name]
,		st.[has_filter]			                                                AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,								 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,		QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM	sys.objects			AS ob
JOIN	sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
JOIN	sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND st.[object_id]		= sc.[object_id]
JOIN	sys.columns			AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
JOIN	sys.tables			AS tb	ON	co.[object_id]		= tb.[object_id]
JOIN	sys.schemas			AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE	1=1
AND		st.[user_created]   = 1
GROUP BY
		sm.[name]
,		tb.[name]
,		st.[name]
,		st.[filter_definition]
,		st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

À ce stade, aucune action ne s’est produite sur la table. La procédure a simplement généré le DDL nécessaire pour mettre à jour les statistiques et a stocké ce code dans une table temporaire.

Cependant, notez également que la procédure stockée n’inclut pas de commande `DROP TABLE` à la fin. Nous avons cependant inclus une vérification d’existence préalable dans la procédure stockée pour rendre le code fiable et reproductible, ceci afin de nous assurer que notre `CTAS` n’échouera pas en raison d’un objet dupliqué dans la session.

Passons maintenant à la partie intéressante !

Dans SQL Data Warehouse, il est possible d’utiliser la table temporaire en dehors de la procédure qui l’a créée. Ce n’est pas le cas de SQL Server. En fait, la table temporaire peut être utilisée à **n’importe quel point** de la session.

Cela peut optimiser la facilité de gestion et la modularité du code. Examinez l’exemple ci-dessous :

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Le code obtenu est bien plus compact.

Dans certains cas, les fonctions à instructions multiples et en ligne peuvent également être remplacées via cette technique.

> [AZURE.NOTE] Vous pouvez également étendre cette solution. Si vous souhaitez simplement mettre à jour une table unique, par exemple, il vous suffit de filtrer la table #stats\_ddl.

## Limitations relatives aux tables temporaires
SQL Data Warehouse impose quelques restrictions lors de l’implémentation de tables temporaires.

Voici les principales limitations :

- Les tables temporaires globales ne sont pas prises en charge.
- Vous ne pouvez pas créer des vues sur des tables temporaires.

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->