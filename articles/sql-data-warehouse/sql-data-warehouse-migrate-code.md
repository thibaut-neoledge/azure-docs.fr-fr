<properties
   pageTitle="Migration de votre code SQL vers SQL Data Warehouse | Microsoft Azure"
   description="Conseils relatifs à la migration de votre code SQL vers Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
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
   ms.date="09/09/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migration de votre code SQL vers SQL Data Warehouse

Pour vérifier la conformité de votre code avec SQL Data Warehouse, il vous faudra probablement apporter des modifications à votre base de code. Certaines fonctionnalités de SQL Data Warehouse peuvent améliorer considérablement les performances, dans la mesure où elles sont conçues pour fonctionner directement selon un modèle distribué. Toutefois, pour maintenir des niveaux appropriés de performance et d’évolutivité, certaines fonctions ne sont pas disponibles.

## Modifications du code Transact-SQL

La liste suivante répertorie les fonctionnalités principales non prises en charge dans Microsoft Azure SQL Data Warehouse. Les liens vous présentent des solutions de contournement pour la fonctionnalité non prise en charge :

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
- [Expressions récursives de table commune (CTE)](#Expressions-récursives-de-table-commune-(CTE)
- [Mises à jour via les CTE](#Updates-through-CTEs)
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
- [Type de données no MAX pour les chaînes dynamiques SQL][]

Fort heureusement, la plupart de ces restrictions peuvent être contournées. Des explications sont fournies dans les articles de développement référencés ci-dessus.

### Expressions récursives de table commune (CTE)

Il s'agit d'un scénario complexe sans solution rapide. Les CTE doivent être décomposées et gérées étape par étape. Vous pouvez généralement utiliser une boucle assez complexe, afin de remplir une table temporaire pendant que vous parcourez les requêtes intermédiaires récursives. Une fois la table temporaire remplie, vous pouvez renvoyer les données sous forme d’un seul jeu de résultats. Une approche similaire a été utilisée pour résoudre `GROUP BY WITH CUBE` dans l’article [Regroupement par clause à l’aide des options rollup/cube/grouping sets][].

### Mises à jour via les CTE

Si la CTE est non récursive, vous pouvez réécrire la requête pour utiliser des sous-requêtes. Pour les CTE récursives, vous devez d'abord créer le jeu de résultats comme décrit ci-dessus, puis joindre un jeu de résultats final à la table cible et effectuer la mise à jour.

### Fonctions système

Certaines fonctions système ne sont pas prises en charge. Voici les principales fonctions habituellement associées aux entrepôts de données :

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT\_BIG
- ERROR\_LINE()

Là encore, nombre de ces problèmes peuvent être contournés.

Par exemple, le code ci-dessous est une solution alternative permettant la récupération des informations @@ROWCOUNT :

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
[Type de données no MAX pour les chaînes dynamiques SQL]: sql-data-warehouse-develop-dynamic-sql.md
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Sept15_HO2-->