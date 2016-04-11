<properties 
	pageTitle="Instructions dans Analytics Application Insights" 
	description="Requêtes, expressions et instructions let dans Analytics, le puissant outil de recherche d’Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

 
# Instructions Analytics dans Application Insights

[Analytics](app-analytics.md) vous permet d’exécuter de puissantes requêtes sur les données de télémétrie collectées par [Application Insights](app-insights-overview.md) à partir de votre application. Ces pages décrivent son langage de requête.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Modèle de données

Dans AIQL :

* Une *base de données* ne contient aucune *table* ou plusieurs.
* Une *table* contient :
 * Une ou plusieurs *colonnes* nommées. Chaque colonne a un *type*.
 * Une ou plusieurs *lignes*.
* Chaque ligne comporte une ou plusieurs *cellules*, une pour chaque colonne de sa table.
* Une cellule peut contenir la valeur du type de sa colonne ou `null`.


## Noms d’entité

Chaque colonne, table ou base de données a un nom unique au sein de son conteneur.

Référence à une entité par son nom (si le contexte n’est pas équivoque) ou par la qualification par son conteneur. Par exemple, `MyColumn` peut également être référencé comme `MyTable.MyColumn` ou `MyDatabase.MyTable.MyColumn`.

Les noms peuvent comprendre jusqu’à 1 024 caractères. Ils respectent la casse et peuvent contenir des lettres, des chiffres et des traits de soulignement (`_`).

En outre, les noms peuvent être mis entre guillemets afin de pouvoir contenir d’autres caractères. Par exemple :

|||
|---|---|
|`['path\\file\n\'x\'']` | Utilisez `` comme caractère d'échappement|
|`["d-e.=/f#\n"]` | |
|`[@'path\file']`| Aucun caractère d’échappement - `` est littéral|
|`[@"\now & then"]` | |
|`[where]` | Utilisation d’un mot-clé de langue comme nom|

Les noms peuvent également être qualifiés avec le nom de la base de données (voir [les requêtes de bases de données croisées](#cross-database-queries))

```
database("MyDb").Table
```


## Instructions

Il existe quatre types d’instructions dans CSL :

### Requêtes de données
  
Demandes en lecture seule concernant les données stockées dans Analytics. Par exemple :

* `event` : renvoie tous les enregistrements dans la table nommée « événement ».
* `event | count` : renvoie un nombre d’enregistrements dans « événement ».

    
## Instructions let

#### Vue d’ensemble
Il est possible de faire précéder une instruction de requête de données par une ou plusieurs instructions let. Elles permettent de lier un nom (un nom d’entité valide) à une expression. Les noms définis par une instruction let peuvent ensuite être utilisés une ou plusieurs fois dans l’instruction de requête de données suivante.

Une instruction let peut lier un nom à des expressions de types suivants :
1. Valeur scalaire
2. Données tabulaires 

En cas de liaison avec des données tabulaires, il est possible de promouvoir une instruction dans une « vue » qui permet à l’instruction de participer à des opérations « union * ».

#### Syntaxe

`let <name> = <expression>`

<expression> peut être une déclaration lambda avec zéro, un ou plusieurs arguments :

`() {...}`

`(<arg0>:<type0>, ...) {...}`

Exemples : l’exemple suivant lie le nom « x » au littéral scalaire « 1 » :

```
let x=1;
range y from x to x step x
```

L’exemple suivant lie un les journaux obtenus pendant une heure au nom Window, puis effectue une jointure réflexive sur Window :


```
let Window=Logs | where Timestamp > ago(1h);
Window | where  ... | join (Window | where …) on ActivityId| ...
```

Les deux exemples suivants illustrent l’utilisation d’une instruction let avec une expression lambda :


```
let Test = () { range x from 1 to 10 step 1 };
Test | count

let step=1;
let Test = (start:long, end:long) { range x from start to end step 1 };
Test(1, 10) | count
```

Utilisez le mot clé « view » pour promouvoir des instructions let dans une vue participant aux opérations union *.


```
let Test1 = view () { range x from start to end step 1 };
let Test2 = view () { range x from start to end step 1 };
union * | count
// Result: 20
```


## Instruction restrict

#### Vue d’ensemble
Il est possible de limiter l’accès à des requêtes figurant dans la liste d’instructions à l’aide de l’instruction suivante :


```
restrict access to (<entity1, entity2, ...>);
```
 
L’instruction autorise l’accès uniquement aux entités (instructions let ou entités tabulaires) qui sont autorisées par l’instruction restrict.
 
Exemples :

```
// Restricting access to Test statement only
let Test = () { range x from 1 to 10 step 1 };
restrict access to (Test);
Test
 
// Assume that there is a table called Table1, Table2 in the database
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
 
// When those statements appear before the command - the next works
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
View1 |  count
 
// When those statements appear before the command - the next access is not allowed
let View1 = view () { Table1 | project Column1 };
let View2 = view () { Table2 | project Column1, Column2 };
restrict access to (View1, View2);
Table1 |  count
```


## Composition d'une requête

Une *requête* a le modèle suivant :

- *source* | *filtre* | *filtre* ...

Par exemple :


```
Logs | where Timestamp > ago(1d) | count
```

* *Source* est le nom d’une table de base de données ou d’une table de résultats définie précédemment.
* Chaque *filtre* appelle un opérateur de requête tel que `where` ou `count` avec les paramètres appropriés. Les paramètres peuvent être *expressions scalaires*, des *requêtes de données* imbriquées ou des noms de colonne.

Par exemple :


```
Logs 
| where Timestamp > ago(1d) 
| join 
(
    Events 
    | where continent == 'Europe'
) on RequestId 
``` 

## Instructions let

Une instruction let peut être utilisée pour définir des fonctions (des expressions nommées avec zéro ou plusieurs arguments) et renvoyer des valeurs. Des instructions supplémentaires sont ensuite en mesure d’« appeler » ces fonctions.

### Valeurs nommées

Définir des noms pour représenter des valeurs scalaires :


```
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

Définir des noms pour représenter des résultats de requête :


```
let Cities = Events | summarize dcount(city) by country;
Cities | take 10
```

L’exemple suivant est particulièrement utile en cas de jointure réflexive :


```
let Recent = Events | where timestamp > ago(7d);
Recent | where name contains "session_started" 
| project start = timestamp, session_id
| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
    on session_id
| extend duration = stop - start 
```

## Fonctions nommées

Définir des fonctions renvoyant des résultats scalaires :


```
let square = (n:long){n*n};
// yield 9 rows
Events | take square(3)    
```

Définir des fonctions renvoyant des résultats de requête :


```
let TopEvents= (n:long, when:datetime){Events 
                | where timestamp > when | take n};
TopEvents(5, ago(7d)) 
```

Les paramètres pour des fonctions nommées doivent être scalaires.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->