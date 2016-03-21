<properties
   pageTitle="Migration de votre code SQL vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la migration de votre code SQL vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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

# Migration de votre code SQL vers SQL Data Warehouse

Pour vérifier la conformité de votre code avec SQL Data Warehouse, il vous faudra probablement apporter des modifications à votre base de code. Certaines fonctionnalités de SQL Data Warehouse peuvent améliorer considérablement les performances, dans la mesure où elles sont conçues pour fonctionner directement selon un modèle distribué. Toutefois, pour maintenir des niveaux appropriés de performance et d’évolutivité, certaines fonctions ne sont pas disponibles.

## Modifications du code Transact-SQL

La liste suivante répertorie les fonctionnalités principales non prises en charge dans Microsoft Azure SQL Data Warehouse. Les liens vous présentent des solutions de contournement pour la fonctionnalité non prise en charge :

- [Jointures ANSI sur les opérations UPDATE][]
- [Jointures ANSI sur les opérations DELETE][]
- [Instruction MERGE][]
- Jonctions entre plusieurs bases de données
- [Curseurs][]
- [SELECT..INTO][]
- [INSERT..EXEC][]
- Clause OUTPUT
- Fonctions en ligne définies par l’utilisateur
- Fonctions à instructions multiples
- [Expressions de table commune](#Common-table-expressions)
- [Expressions récursives de table commune (CTE)](#Expressions-récursives-de-table-commune-(CTE)
- Fonctions et procédures CLR
- Fonction $partition
- Variables de table
- Paramètres de valeurs de table
- Transactions distribuées
- Tâche de validation/restauration
- Transaction d’enregistrement
- Contextes d’exécution (EXECUTE AS)
- [Regroupement par clause à l’aide des options rollup/cube/grouping sets][]
- [Imbrication des niveaux au-delà de 8][]
- [Mise à jour par le biais de vues][]
- [Utilisation de Select pour l’attribution des variables][]
- [Type de données no MAX pour les chaînes dynamiques SQL][]

Fort heureusement, la plupart de ces restrictions peuvent être contournées. Des explications sont fournies dans les articles de développement référencés ci-dessus.

### Expressions de table commune
L’implémentation actuelle des expressions de table communes (CTE) dans SQL Data Warehouse comporte les fonctionnalités et les limitations suivantes :

**La fonctionnalité CTE**
+ Une CTE peut être spécifiée dans une instruction SELECT.
+ Une CTE peut être spécifiée dans une instruction CREATE VIEW.
+ Une CTE peut être spécifiée dans une instruction CREATE TABLE AS SELECT (CTAS).
+ Une CTE peut être spécifiée dans une instruction CREATE REMOTE TABLE AS SELECT (CRTAS).
+ Une CTE peut être spécifiée dans une instruction CREATE EXTERNAL TABLE AS SELECT (CETAS).
+ Une table distante peut être référencée à partir d’une expression de table commune.
+ Une table externe peut être référencée à partir d’une expression de table commune.
+ Plusieurs définitions de requête CTE peuvent être définies dans une expression de table commune.

**Limitations d’une CTE**
+ Une CTE doit être suivie d’une instruction SELECT unique. Les instructions INSERT, UPDATE, DELETE et MERGE ne sont pas prises en charge.
+ Une expression de table commune qui inclut des références à elle-même (expression de table commune récursive) n’est pas prise en charge (voir la section ci-dessous).
+ La spécification de plusieurs clauses WITH dans une CTE n’est pas autorisée. Par exemple, si une définition CTE\_query\_definition contient une sous-requête, celle-ci ne peut pas contenir de clause WITH imbriquée définissant une autre CTE.
+ Une clause ORDER BY ne peut pas être utilisée dans une définition CTE\_query\_definition, sauf lorsqu’une clause TOP est spécifiée.
+ Lorsqu’une CTE est utilisée dans une instruction qui fait partie d’un lot, l’instruction qui la précède doit être suivie d’un point-virgule.
+ Lorsqu’une CTE est utilisée dans des instructions préparées par sp\_prepare, celle-ci se comporte de la même façon que les autres instructions SELECT dans PDW. Toutefois, si les expressions de table communes sont utilisées dans le cadre de CETAS préparées par sp\_prepare, le comportement peut différer selon qu’il s’agit d’instructions SQL Server ou PDW en raison de la façon dont la liaison est mise en œuvre pour sp\_prepare. Si l’instruction SELECT qui référence CTE utilise une colonne erronée, qui n’existe pas dans CTE, sp\_prepare transmet sans détecter l’erreur, mais l’erreur est levée pendant l’instruction sp\_execute.

### Expressions récursives de table commune (CTE)

Il s’agit d’un scénario de migration complexe, et la meilleure procédure consiste à diviser la CTE et à la gérer en procédant par étapes. Vous pouvez généralement utiliser une boucle pour une table temporaire pendant que vous parcourez les requêtes intermédiaires récursives. Une fois la table temporaire remplie, vous pouvez renvoyer les données sous forme d’un seul jeu de résultats. Une approche similaire a été utilisée pour résoudre `GROUP BY WITH CUBE` dans l’article [Regroupement par clause à l’aide des options rollup/cube/grouping sets][].

### Fonctions système

Certaines fonctions système ne sont pas prises en charge. Voici les principales fonctions habituellement associées aux entrepôts de données :

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

Là encore, nombre de ces problèmes peuvent être contournés.

Par exemple, le code ci-dessous est une solution alternative permettant la récupération des informations @@ROWCOUNT :

```
SELECT  SUM(row_count) AS row_count
FROM    sys.dm_pdw_sql_requests
WHERE   row_count <> -1
AND     request_id IN
                    (   SELECT TOP 1    request_id
                        FROM            sys.dm_pdw_exec_requests
                        WHERE           session_id = SESSION_ID()
                        ORDER BY end_time DESC
                    )
;
```

## Étapes suivantes
Pour bénéficier de recommandation sur le développement de votre code, consultez la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[Jointures ANSI sur les opérations UPDATE]: sql-data-warehouse-develop-ctas.md
[Jointures ANSI sur les opérations DELETE]: sql-data-warehouse-develop-ctas.md
[Instruction MERGE]: sql-data-warehouse-develop-ctas.md
[INSERT..EXEC]: sql-data-warehouse-develop-temporary-tables.md

[Curseurs]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[Regroupement par clause à l’aide des options rollup/cube/grouping sets]: sql-data-warehouse-develop-group-by-options.md
[Imbrication des niveaux au-delà de 8]: sql-data-warehouse-develop-transactions.md
[Mise à jour par le biais de vues]: sql-data-warehouse-develop-views.md
[Utilisation de Select pour l’attribution des variables]: sql-data-warehouse-develop-variable-assignment.md
[Type de données no MAX pour les chaînes dynamiques SQL]: sql-data-warehouse-develop-dynamic-sql.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!------HONumber=AcomDC_0309_2016-->