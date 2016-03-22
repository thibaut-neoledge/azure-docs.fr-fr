<properties
   pageTitle="Tables temporaires dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de tables temporaires dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Tables temporaires dans SQL Data Warehouse
Les tables temporaires se trouvent au niveau de la session dans SQL Data Warehouse. Elles sont définies en tant que tables temporaires locales. Contrairement aux tables SQL Server, elles sont accessibles depuis n’importe quel point de la session.

Les tables temporaires sont très utiles lors du traitement des données, notamment lors d’une transformation, lorsque les résultats intermédiaires sont temporaires.

Cet article met en évidence certaines méthodes spécifiques permettant d’exploiter des tables temporaires, afin de vous aider à modulariser votre code.

## Modularisation du code

La possibilité d’afficher des tables temporaires depuis n’importe quel point d’une session utilisateur peut vous aider à modulariser votre code d’application.

Prenons un exemple pratique.

La procédure stockée ci-dessous génère le code DDL nécessaire pour mettre à jour les statistiques dans chaque colonne de la base de données :

```
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
```

Vous pouvez voir qu’aucune action n’a été appliquée aux données, en réalité. Cette procédure a simplement généré le code DDL, qu’elle a stocké dans une table temporaire.

Toutefois, SQL Data Warehouse peut utiliser cette table temporaire en dehors de la procédure qui l’a créée. Ce n’est pas le cas de SQL Server. En fait, la table temporaire peut être utilisée à **n’importe quel point** de la session.

Cela peut optimiser la facilité de gestion et la modularité du code.

```
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

Dans certains cas, les fonctions à instructions multiples et inline peuvent également être remplacées via cette technique.

> [AZURE.NOTE] Vous pouvez également étendre cette solution. Si vous souhaitez simplement mettre à jour une table unique, par exemple, il vous suffit de filtrer la table #stats\_ddl.

## Limitations relatives aux tables temporaires
SQL Data Warehouse impose quelques restrictions lors de l’implémentation de tables temporaires.

Voici les principales limitations :

- Les tables temporaires globales ne sont pas prises en charge.
- Vous ne pouvez pas créer des vues sur des tables temporaires.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---------HONumber=AcomDC_0309_2016-->