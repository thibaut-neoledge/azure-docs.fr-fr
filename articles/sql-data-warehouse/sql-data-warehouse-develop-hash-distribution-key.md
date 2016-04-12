<properties
   pageTitle="Exécution de la distribution par hachage et impact sur les performances des requêtes dans SQL Data Warehouse | Microsoft Azure"
   description="Découvrez plus d’informations sur les tables avec distribution par hachage et leur impact sur les performances des requêtes dans Microsoft Azure SQL Data Warehouse, lors du développement de solutions."
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
   ms.date="03/03/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Exécution de la distribution par hachage et impact sur les performances des requêtes dans SQL Data Warehouse

L’une des méthodes les plus pertinentes pour améliorer les performances des requêtes consiste à prendre une décision avisée concernant la distribution par hachage.

En fait, trois facteurs sont importants :

1. réduire le nombre de déplacements de données ;
2. éviter le décalage des données ;
3. assurer une exécution équilibrée.

## Réduire le nombre de déplacements des données
Le déplacement des données se produit généralement en cas de jointure de tables, ou lors de l’agrégation de données dans des tables. Pour limiter ces déplacements, l’une des méthodes les plus efficaces consiste à distribuer des tables par hachage sur une clé partagée.

Toutefois, pour que cette opération soit efficace à ce niveau, l’ensemble des critères suivants doivent être avérés :

1. Les deux tables doivent faire l’objet d’une distribution par hachage et jointes sur la clé de distribution partagée.
2. Les types de données des deux colonnes doivent être identiques.
3. Les colonnes jointes doivent faire l’objet d’une équijointure (les valeurs de la colonne de la table de gauche doivent être égales à celles de la colonne de droite).
4. La jointure n’est **pas** de type `CROSS JOIN`.

> [AZURE.NOTE] Les colonnes utilisées dans les clauses `JOIN`, `GROUP BY`, `DISTINCT` et `HAVING` sont toutes adéquates pour une opération HASH. À l’inverse, les colonnes indiquées dans la clause `WHERE` ne sont **pas** adéquates pour cette opération. Consultez la section portant sur l’équilibrage de l’exécution (ci-dessous).

Le déplacement des données peut également résulter de la syntaxe de la requête (l’élément `COUNT DISTINCT` et la clause `OVER` en sont d’excellents exemples) lorsqu’elle est utilisée avec des colonnes qui n’incluent pas la clé de distribution par hachage.

> [AZURE.NOTE] La distribution par tourniquet (round robin) des tables entraîne souvent un déplacement des données. Les données de la table ont été allouées de manière non déterministe. Par conséquent, elles doivent être déplacées avant la fin de l’exécution de la plupart des requêtes.

## Éviter le décalage des données
Afin d’assurer la réussite de la distribution par hachage, il faut que la colonne que vous sélectionnez présente les propriétés suivantes :

1. Elle contient un nombre important de valeurs distinctes.
2. Elle ne souffre d’aucun **décalage de données**.

Chaque valeur distincte sera allouée à une distribution. Par conséquent, les données nécessitent un certain nombre de valeurs distinctes pour garantir la génération d’une quantité suffisante de valeurs de hachage. Dans le cas contraire, la qualité du hachage risque d’être médiocre. Si le nombre de distributions dépasse le nombre de valeurs distinctes, par exemple, certaines distributions seront vides. Cela risque de nuire aux performances.

De même, si toutes les lignes de la colonne hachée contiennent la même valeur, on parle de données **décalées**. Dans ce cas extrême, une seule valeur de hachage est créée ; de ce fait, toutes les lignes se terminent au sein d’une seule et même distribution. Dans l’idéal, chaque valeur distincte de la colonne hachée présente le même nombre de lignes.

> [AZURE.NOTE] Les tables avec distribution par tourniquet (round robin) ne montrent aucun signe de décalage des données. En effet, les données sont stockées de manière équitable dans les différentes distributions.

## Mettre en place une exécution équilibrée
Vous obtenez une exécution équilibrée lorsque chaque distribution présente le même nombre de tâches à accomplir. Le processus de traitement massivement parallèle (MPP) est un travail d’équipe ; chaque participant doit atteindre la ligne d’arrivée pour qu’un vainqueur puisse être déterminé. Si chaque distribution possède le même nombre de tâches (c’est-à-dire la même quantité de données à traiter), toutes les requêtes s’achèveront en même temps. On parle alors d’une « exécution équilibrée ».

Comme nous l’avons vu, le décalage des données peut affecter l’équilibrage de l’exécution. Toutefois, le choix de la clé de distribution par hachage peut également avoir cet effet. Si une colonne a été choisie et apparaît dans la clause `WHERE` d’une requête, il est probable que la requête ne sera pas équilibrée.

> [AZURE.NOTE] En effet, la clause `WHERE` permet généralement d’identifier les colonnes les mieux utilisées lors du partitionnement.

Voici un exemple représentatif d’un type de colonne apparaissant dans la clause `WHERE` : une colonne de champ de date. En général, les colonnes de champs de date constituent des exemples de colonnes de partitionnement efficaces. Par contre, elles ne sont pas efficaces pour la distribution par hachage. En règle générale, les requêtes des entrepôts de données portent sur une période donnée (un jour, une semaine ou un mois, par exemple). La distribution des données par hachage en fonction de la date peut tout à fait limiter l’évolutivité et nuire aux performances. Si, par exemple, la plage de dates spécifiée est d’une semaine, le nombre maximal de hachages est de 7 (un par jour). Cela signifie que seules sept de nos distributions contiendraient des données ; les autres seraient vides. Cela se traduirait par une exécution déséquilibrée des requêtes, car les données seraient traitées par sept distributions seulement.

> [AZURE.NOTE] Les tables de distribution par tourniquet (round robin) assurent généralement une exécution équilibrée. En effet, les données sont stockées de manière équitable dans les différentes distributions.

## Recommandations
Pour optimiser vos performances et le débit global des requêtes, faites en sorte que les tables avec distribution par hachage respectent le modèle suivant, autant que possible :

La clé de distribution par hachage :

1. Est une valeur statique, car vous ne pouvez pas mettre à jour la colonne de hachage.
2. est utilisée dans les clauses `JOIN`, `GROUP BY`, `DISTINCT` et `HAVING` dans vos requêtes ;
2. n’est pas utilisée dans les clauses `WHERE` ;
3. présente un grand nombre de valeurs différentes (1 000, au minimum) ;
4. ne présente pas un nombre beaucoup trop important de lignes, dont le hachage concernera un petit nombre de distributions ;
5. est définie comme NOT NULL. Les lignes définies comme NULL seront réunies dans une distribution.

## Résumé

Le principe de fonctionnement de la distribution par hachage peut être résumé comme suit :

- La fonction de hachage est déterministe ; la même valeur est toujours affectée à la même distribution.
- Une des colonnes est utilisée en tant que colonne de distribution. La fonction de hachage utilise la colonne désignée pour calculer les attributions de ligne associées aux distributions.
- Cette fonction repose sur le type de colonne, et non sur les valeurs elles-mêmes.
- La distribution par hachage d’une table peut parfois donner lieu à une table décalée.
- En général, les tables avec distribution par hachage nécessitent moins de déplacements des données lors de la résolution des requêtes ; de ce fait, les performances des requêtes sont améliorées dans le cadre de tables de faits volumineuses.
- Tenez compte des recommandations relatives à la sélection de la colonne avec distribution par hachage pour améliorer le débit des requêtes.

> [AZURE.NOTE] Dans SQL Data Warehouse, la cohérence des types de données a son importance ! Assurez-vous que le schéma existant utilise toujours le même type pour une colonne. Ce facteur est très important, notamment pour la clé de distribution. Si les types de données des clés de distribution ne sont pas synchronisés et que les tables sont jointes, il se peut que les données soient déplacées sans nécessité. Cela peut s’avérer coûteux lorsque les tables sont volumineuses. De plus, le débit peut être réduit, ainsi que les performances.


## Étapes suivantes
Pour obtenir des conseils supplémentaires en matière de développement, voir la [vue d’ensemble sur le développement][].

<!--Image references-->

<!--Article references-->
[vue d’ensemble sur le développement]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---------HONumber=AcomDC_0309_2016-->