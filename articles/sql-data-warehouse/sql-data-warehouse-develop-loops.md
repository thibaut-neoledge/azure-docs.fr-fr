<properties
   pageTitle="Boucles dans SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à l’utilisation de boucles Transact-SQL et au remplacement de curseurs dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
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

# Boucles dans SQL Data Warehouse
SQL Data Warehouse prend en charge la boucle [WHILE][] pour les blocs d’instructions dont l’exécution se répète. Cette opération se poursuit aussi longtemps que les conditions spécifiées sont vraies, ou jusqu’à ce que le code arrête la boucle de manière spécifique, via le mot clé `BREAK`. Les boucles sont particulièrement utiles pour remplacer des curseurs définis dans le code SQL. Heureusement, presque tous les curseurs écrits en code SQL présentent le type à avance rapide, en lecture seule. Par conséquent, les boucles [WHILE] sont des méthodes alternatives intéressantes si vous devez en remplacer un.

## Valorisation de boucles et remplacement de curseurs dans SQL Data Warehouse
Toutefois, avant de vous lancer, vous devez vous poser la question suivante : « Puis-je réécrire ce curseur afin d’utiliser des opérations basées sur un jeu ? ». Dans de nombreux cas, la réponse est Oui, et cette approche est souvent la meilleure. Une opération basée sur un jeu s’exécute généralement beaucoup plus rapidement qu’une méthode itérative de type ligne par ligne.

Les curseurs à avance rapide et en lecture seule peuvent facilement être remplacés par des constructions en boucle. Voici un exemple simple. Cet exemple de code met à jour les statistiques de chaque table dans la base de données. En effectuant une itération sur les tables dans la boucle, nous sommes en mesure d’exécuter chaque commande dans l’ordre.

Tout d’abord, créez une table temporaire contenant un numéro de ligne unique, utilisé pour identifier les instructions individuelles :

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Deuxièmement, initialisez les variables nécessaires pour exécuter la boucle :

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Ensuite, effectuez une boucle avec les instructions, en les exécutant l’une après l’autre :

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Enfin, supprimez la table temporaire créée à la première étape.

```
DROP TABLE #tbl;
```


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[WHILE]: https://msdn.microsoft.com/library/ms178642.aspx


<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->