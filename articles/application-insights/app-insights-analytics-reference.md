---
title: "Référence pour Analytics dans Azure Application Insights | Microsoft Docs"
description: "Référence pour les instructions dans Analytics, le puissant outil de recherche d’Application Insights. "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: eea324de-d5e5-4064-9933-beb3a97b350b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/05/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 3fb2464e3757d316367487506f0aca9f1c2e35cc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---
# <a name="reference-for-analytics"></a>Référence pour Analytics
[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante [d’Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.

Sources d’information supplémentaires :

* Beaucoup de documents de référence sont disponibles dans Analytics. Saisissez une requête et l’outil vous propose des réponses possibles.
* [La page du didacticiel](app-insights-analytics-tour.md) fournit une présentation pas à pas des fonctionnalités du langage.
* [L’aide-mémoire des utilisateurs de SQL](https://aka.ms/sql-analytics) traduit les idiomes courants.
* [Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo) si votre propre application n’envoie pas de données à Application Insights.
 

## <a name="index"></a>Index
**Let** [let](#let-clause) | [materialize](#materialize) 

**Requêtes et opérateurs** [as](#as-operator) | [autocluster](#evaluate-autocluster) | [basket](#evaluate-basketv2) | [count](#count-operator) | [datatable](#datatable-operator) | [diffpatterns](#evaluate-diffpatterns) | [distinct](#distinct-operator) | [evaluate](#evaluate-operator) | [extend](#extend-operator) | [extractcolumns](#evaluate-extractcolumns) | [find](#find-operator) | [getschema](#getschema-operator) | [join](#join-operator) | [limit](#limit-operator) | [make-series](#make-series-operator) | [mvexpand](#mvexpand-operator) | [parse](#parse-operator) | [project](#project-operator) | [project-away](#project-away-operator) | [range](#range-operator) | [reduce](#reduce-operator) | [render directive](#render-directive) | [restrict clause](#restrict-clause) | [sample](#sample-operator) | [sample-distinct](#sample-distinct-operator) | [sort](#sort-operator) | [summarize](#summarize-operator) | [table](#table-operator) | [take](#take-operator) | [top](#top-operator) | [top-nested](#top-nested-operator) | [union](#union-operator) | [where](#where-operator) 

**Agrégations** [any](#any) | [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [count](#count) | [countif](#countif) | [dcount](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [min](#min) | [percentile](#percentile) | [percentiles](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [stdev](#stdev) | [sum](#sum) | [variance](#variance)

**Valeurs scalaires** [Littéraux Boolean](#boolean-literals) | [Opérateurs booléens](#boolean-operators) | [Casts](#casts) | [Comparaisons scalaires](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [isnull](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Chiffres** [Opérateurs arithmétiques](#arithmetic-operators) | [Littéraux numériques](#numeric-literals) | [abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) | [journal](#log) | [rand](#rand) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Séries numériques** 
[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) | [series_periods](#seriesperiods) | [series_stats](#seriesstats) | 

**Date et heure** [Expressions de date et heure](#date-and-time-expressions) | [Littéraux Date et d’heure](#date-and-time-literals) | [ago](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [maintenant](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Chaîne** [GUID](#guids) | [Littéraux de chaîne masqués](#obfuscated-string-literals) | [Littéraux de chaîne](#string-literals) | [Comparaisons de chaînes](#string-comparisons) | [countof](#countof) | [extract](#extract) | [in, !in](#in) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Tableaux, objets et dynamique** [Littéraux de tableau et d’objet](#array-and-object-literals) | [Fonctions de l’objet dynamique](#dynamic-object-functions) | [Objets dynamiques dans les clauses let](#dynamic-objects-in-let-clauses) | [Expressions de chemin JSON](#json-path-expressions) | [Noms](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [in, !in](#in) | [parsejson](#parsejson) | [range](#range) | [todynamic](#todynamic) | [treepath](#treepath)

## <a name="let"></a>Let
### <a name="let-clause"></a>clause let
**Clause let tabulaire - nommer une table**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Clause let scalaire - nommer une valeur**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Clause let lambda - nommer une fonction**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime)
    {
      strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ", 
      bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
    };
    requests 
    | summarize count() by bin(timestamp, 1h) 
    | project count_, pacificTime=us_date(timestamp-8h)

Une clause let associe un [nom](#names) à un résultat tabulaire, une valeur scalaire ou une fonction. La clause est un préfixe d’une requête tandis que la portée de la liaison correspond à cette requête. (La clause let ne permet pas de nommer des objets que vous pourrez utiliser ultérieurement dans votre session.)

**Syntaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *type :* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](#dynamic-type)
* *plain_query :* requête non précédée d’une clause let.

**Exemples**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...

Convertissez un tableau de résultat en scalaire et utilisez-le dans une requête :

```
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

### <a name="materialize"></a>materialize

Utilisez materialize() pour améliorer les performances, où le résultat d’une clause let est utilisé plusieurs fois en aval. Materialize() évalue et met en cache le résultat d’une clause let tabulaire au moment de l’exécution de la requête, garantissant ainsi que la requête n’est pas exécutée plusieurs fois.

**Syntaxe**

    materialize(expression)

**Arguments**

* `expresion` : expression tabulaire à évaluer et mettre en cache pendant l’exécution de la requête.

**Conseils**

* Utilisez materialize lorsque vous disposez de joint/union, où leurs opérandes incluent des sous-requêtes mutuelles ne pouvant être exécutées qu’une seule fois (voir les exemples ci-dessous).
* Utile également dans des scénarios lorsque nous avons besoin de branchements join/union.
* Materialize ne peut être utilisé que dans des instructions let en donnant un nom au résultat mis en cache.
* Materialize a une limite de taille de cache de 5 Go. Cette limite s’entend par nœud de cluster et est mutuelle pour toutes les requêtes.

**Exemple : self-join**


```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let materializedScope = pageViews
| summarize by name, Day = startofday(timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project name, Day1 = Day
| join kind = inner
(
    cachedResult
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

La version non mise en cache utilise deux fois le résultat `scope` :

```AIQL
let totalPagesPerDay = pageViews
| summarize by name, Day = startofday(timestamp)
| summarize count() by Day;
let scope = pageViews
| summarize by name, Day = startofday(timestamp);
scope      // First use of this table.
| project name, Day1 = Day
| join kind = inner
(
    scope  // Second use can cause evaluation twice.
    | project name, Day2 = Day
)
on name
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1
```

## <a name="queries-and-operators"></a>Requêtes et opérateurs
Une requête sur vos données de télémétrie est constituée d’une référence à un flux source, suivie d’un pipeline de filtres. Par exemple :

```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```

Chaque filtre précédé de la barre verticale `|` est une instance d’un *opérateur*, assortie de certains paramètres. L’entrée de l’opérateur est la table résultant du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](#scalars) sur les colonnes de l’entrée. Dans certains cas, les paramètres correspondent aux noms des colonnes d’entrée et, parfois, le paramètre est une seconde table. Le résultat d’une requête est toujours une table, même si elle ne contient qu’une colonne et qu’une ligne.

Les requêtes peuvent contenir des sauts de ligne uniques, mais se terminent par une ligne vide. Elles peuvent contenir des commentaires entre `//` et la fin de la ligne.

Une requête peut être précédée d’une ou de plusieurs [clauses let](#let-clause), qui définissent des valeurs scalaires, des tables ou des fonctions utilisables dans la requête.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` est utilisé dans les exemples de requête ci-dessous pour désigner la table source ou le pipeline précédent.
> 
> 

### <a name="as-operator"></a>opérateur as

Lit temporairement un nom à l’expression tabulaire d’entrée.

**Syntaxe**

    T | as name

**Arguments**

* *name :* nom temporaire de la table

**Remarques**

* Utilisez [let](#let-clause) au lieu de *as* si vous souhaitez utiliser le nom dans une sous-expression ultérieure.
* Utilisez *as* pour spécifier le nom de la table tel qu’il apparaît dans le résultat d’une requête [union](#union-operator), [find](#find-operator) ou [search](#search-operator).

**Exemple**

```AIQL
range x from 1 to 10 step 1 | as T1
| union withsource=TableName (requests | take 10 | as T2)
```

### <a name="count-operator"></a>opérateur count
L’opérateur `count` retourne le nombre d’enregistrements (lignes) dans le jeu d’enregistrements d’entrée.

**Syntaxe**

    T | count

**Arguments**

* *T*: données tabulaires dont les enregistrements doivent être comptabilisés.

**Retourne**

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de la seule cellule correspond au nombre d’enregistrements dans *T*. 

**Exemple**

```AIQL
requests | count
```

### <a name="datatable-operator"></a>opérateur DataTable

Spécifiez une table intégrée. Le schéma et les valeurs sont définis dans la requête elle-même.

Notez que cet opérateur ne dispose pas d’un pipeline d’entrée.

**Syntaxe**

    datatable ( ColumnName1 : ColumnType1 , ...) [ScalarValue1, ...]

* *ColumnName* : un nom pour une colonne.
* *ColumnType* Un [type de données](#scalars). 
* *ScalarValue* : une valeur du type approprié. Le nombre de valeurs doit être un multiple du nombre de colonnes. 

**Retourne**

Une table qui contient les valeurs spécifiées.

**Exemple**

```AIQL
datatable (Date:datetime, Event:string)
    [datetime(1910-06-11), "Born",
     datetime(1930-01-01), "Enters Ecole Navale",
     datetime(1953-01-01), "Published first book",
     datetime(1997-06-25), "Died"]
| where strlen(Event) > 4
```

### <a name="distinct-operator"></a>opérateur distinct

Renvoie une table qui contient l’ensemble de lignes qui ont des combinaisons distinctes de valeurs. Projette, de façon facultative, sur un sous-ensemble de colonnes avant l’opération.

**Syntaxe**

    T | distinct *              // All columns
    T | distinct Column1, ...   // Columns to project

**Exemple**

```AIQL
datatable (Supplier: string, Fruit: string, Price:int) 
["Contoso", "Grapes", 22,
"Fabrikam", "Apples", 14,
"Contoso", "Apples", 15,
"Fabrikam", "Grapes", 22]
| distinct Fruit, Price 
```


|Fruit|Prix|
|---|---|
|Grapes|22|
|Pommes|14|
|Pommes|15|


### <a name="evaluate-operator"></a>opérateur evaluate
`evaluate` est un mécanisme d’extension qui permet d’ajouter des algorithmes spécialisés aux requêtes.

`evaluate` doit être le dernier opérateur du pipeline de requête (à l’exception d’un éventuel `render`). Il ne doit pas apparaître dans le corps d’une fonction.

[evaluate autocluster](#evaluate-autocluster) | [evaluate basket](#evaluate-basketv2) | [evaluate diffpatterns](#evaluate-diffpatterns) | [evaluate extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>evaluate autocluster
     T | evaluate autocluster()

Autocluster est un moyen rapide de rechercher les regroupements naturels dans un jeu de données. Par exemple, à partir d’une masse de données de demande, vous pouvez rapidement identifier que 80 % des échecs 404 étaient des demandes pour une URL particulière, effectuée par un client dans une ville spécifique.

AutoCluster recherche les modèles courants d’attributs discrets (dimensions) dans les données et réduit les résultats de la requête d’origine (qu’elle fasse 100 ou 100 000 lignes) à un petit nombre de modèles. AutoCluster a été développé pour analyser les échecs (par exemple, les exceptions, les incidents) mais peut éventuellement fonctionner sur n’importe quel jeu de données filtré. 

**Syntaxe**

    T | evaluate autocluster( arguments )

**Retourne**

AutoCluster retourne un jeu de modèles (généralement petit) qui permettent de capturer des parties de données comportant des valeurs courantes partagées par plusieurs attributs discrets. Chaque modèle est représenté par une ligne dans les résultats. 

Les deux premières colonnes correspondent au nombre et au pourcentage de lignes de la requête d’origine qui sont capturés par le modèle. Les colonnes restantes sont issues de la requête d’origine et leur valeur est soit une valeur spécifique de la colonne soit « * », qui correspond à des valeurs de variables. 

Notez que les modèles ne sont pas disjoints : ils peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.

**Conseils**

* Utilisez `where` et `project` dans le canal d’entrée pour réduire les données uniquement à ce qui vous intéresse.
* Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre `where` .

**Arguments (tous facultatifs)**

* `output=all | values | minimal` 
  
    Format des résultats. Les colonnes correspondant au nombre et au pourcentage apparaissent toujours dans les résultats. 
  
  * `all` : toutes les colonnes de l’entrée sont générées.
  * `values` : retire les colonnes indiquant « * » comme seul résultat.
  * `minimal` : retire également les colonnes qui sont identiques pour toutes les lignes dans la requête d’origine. 
* `min_percent=`*double* (valeur par défaut : 1)
  
    Pourcentage de couverture minimal des lignes générées.
  
    Exemple : `T | evaluate autocluster("min_percent=5.5")`
* `num_seeds=` *int* (valeur par défaut : 25) 
  
    Le nombre de valeurs initiales détermine le nombre de points de recherche locaux initiaux de l’algorithme. Dans certains cas, selon la structure des données, l’augmentation du nombre de valeurs initiales augmente le nombre (ou la qualité) des résultats par le biais d’un espace de recherche plus important avec un compromis de requête plus lent. L’argument num_seeds présente une diminution des résultats dans les deux sens. Ainsi, si on baisse sa valeur en-deçà de 5, on obtient des améliorations de performances négligeables et si on l’augmente au-delà de 50, cela génère rarement des modèles supplémentaires.
  
    Exemple : `T | evaluate autocluster("num_seeds=50")`
* `size_weight=` *0<double<1*+ (valeur par défaut : 0,5)
  
    Vous permet de contrôler l’équilibre entre le générique (couverture élevée) et l’informatif (nombreuses valeurs partagées). L’augmentation de size_weight réduit généralement le nombre de modèles et chaque modèle a tendance à couvrir un pourcentage plus élevé. La diminution de size_weight produit généralement des modèles plus spécifiques avec davantage de valeurs partagées et un pourcentage de couverture moins élevé. La formule en arrière-plan est une moyenne géométrique pondérée entre le score générique normalisé et le score informatif, avec size_weight et 1-size_weight comme pondérations. 
  
    Exemple : `T | evaluate autocluster("size_weight=0.8")`
* `weight_column=` *column_name*
  
    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1») ; il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
  
    Exemple : `T | evaluate autocluster("weight_column=sample_Count")` 

#### <a name="evaluate-basket-deprecated"></a>evaluate basket (déconseillé)

     T | evaluate basket()

**Cette version de `basket` est déconseillée. Utilisez [basket_v2](#evaluate-basketv2).**

**Retourne**

Tous les modèles qui apparaissent dans plus d’une fraction spécifiée (valeur par défaut 0,05) des événements.

**Arguments (tous facultatifs)**

* `threshold=` *0.015<double<1* (valeur par défaut : 0,05) 
  
    Définit le taux minimal de lignes pouvant être considérées comme fréquentes (les modèles dont le taux est moins élevé ne seront pas retournés).
  
    Exemple : `T | evaluate basket("threshold=0.02")`
* `weight_column=` *column_name*
  
    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1») ; il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
  
    Exemple : T | evaluate basket("weight_column=sample_Count")
* `max_dims=` *1<int* (valeur par défaut : 5)
  
    Définit le nombre maximal de dimensions non corrélées par panier, limité par défaut pour réduire le temps d’exécution de la requête.
* `output=minimize` | `all` 
  
    Format des résultats. Les colonnes correspondant au nombre et au pourcentage apparaissent toujours dans les résultats.
  
  * `minimize` : retire les colonnes indiquant « * » comme seul résultat.
  * `all` : toutes les colonnes de l’entrée sont générées.

<a name="evaluate-basket"></a>
#### <a name="evaluate-basketv2"></a>evaluate basket_v2

     T | evaluate basket_v2()

Basket recherche tous les modèles fréquents d’attributs discrets (dimensions) dans les données et retourne l’ensemble des modèles fréquents ayant franchi le seuil de fréquence dans la requête d’origine. La recherche, par basket, de tous les modèles fréquents dans les données est garantie, mais pas son exécution polynomiale. L’exécution de la requête est linéaire pour le nombre de lignes mais peut, dans certains cas, être exponentielle pour le nombre de colonnes (dimensions). Basket repose sur l’algorithme Apriori, développé à l’origine pour l’exploration de données d’analyse du panier. 

Remplace la syntaxe `evaluate basket` déconseillée.

**Retourne**

Tous les modèles qui apparaissent dans plus d’une fraction spécifiée (valeur par défaut 0,05) des événements. Pour chaque modèle, les colonnes qui ne sont pas définies dans le modèle (c’est-à-dire sans restriction sur une valeur spécifique) contiennent une valeur générique qui sont des valeurs null par défaut (voir dans la section Arguments ci-dessous comment ils peuvent être modifiés manuellement).

**Arguments (tous facultatifs)**

Tous les arguments sont facultatifs, mais doivent apparaître dans l’ordre suivant. Pour indiquer qu’une valeur par défaut doit être utilisée, utilisez le signe tilde ~ (voir les exemples ci-dessous).

* Seuil : 0,015 < *double* < 1 (valeur par défaut : 0,05) 
  
    Définit le taux minimal de lignes pouvant être considérées comme fréquentes (les modèles dont le taux est moins élevé ne seront pas retournés).
  
    Exemple : `T | evaluate basket(0.02)`
* Colonne de pondération *itemCount*
  
    Permet de prendre en compte l’échantillonnage et l’agrégation préliminaire métrique. Chaque ligne se voit attribuer la pondération spécifiée dans cette colonne. Par défaut, chaque ligne a une pondération de '1'. Cela prend en compte la création de compartiments ou l’agrégation des données déjà incorporées dans chaque ligne.
  
    Exemple : `T | evaluate basket('~', itemCount)`
* Dimensions max. : 1 < *int* (valeur par défaut : 5)
  
    Définit le nombre maximal de dimensions non corrélées par panier, limité par défaut pour réduire le temps d’exécution de la requête.

    Exemple : `T | evaluate basket('~', '~', 3)`
* Types génériques personnalisés : *n’importe quelle valeur par type*
  
    Définit la valeur de caractère générique pour un type spécifique dans la table de résultats qui indique que le modèle actuel ne présente pas de restriction sur cette colonne. La valeur par défaut est null, la chaîne par défaut est une chaîne vide. Si la valeur par défaut est une valeur viable dans les données, une valeur de caractère générique différente doit être utilisée (par exemple, *).

    Exemple : `T | evaluate basket_v2('~', '~', '~', '*', int(-1), double(-1), long(0), datetime(1900-1-1))`

**Exemple**

``` AIQL
requests 
| evaluate basket_v2(0.7, itemCount)
```

#### <a name="evaluate-diffpatterns"></a>evaluate diffpatterns
     requests | evaluate diffpatterns("split=success")

Diffpatterns identifie les différences entre deux jeux de données de la même structure, par exemple, le journal de la demande au moment d’un incident, et les journaux de demandes normaux. Diffpatterns a été développé pour analyser les échecs (par exemple, en comparant les échecs et l’absence d’échecs sur une période donnée), mais peut éventuellement rechercher les différences entre deux jeux de données quelconques de la même structure. 

**Syntaxe**

`T | evaluate diffpatterns("split=` *BinaryColumn* `" [, arguments] )`

**Retourne**

Diffpatterns retourne un jeu de modèles (généralement petit) qui capturent différentes parties des données dans les deux jeux (par exemple, un modèle qui capture un fort pourcentage des lignes dans le premier jeu de données et un faible pourcentage des lignes dans le deuxième jeu). Chaque modèle est représenté par une ligne dans les résultats.

Les quatre premières colonnes correspondent au nombre et au pourcentage de lignes de la requête d’origine qui sont capturées par le modèle dans chaque jeu ; la cinquième colonne correspond à la différence (en pourcentage absolu) entre les deux jeux. Les colonnes restantes sont issues de la requête d’origine et leur valeur est soit une valeur spécifique de la colonne soit *, qui correspond à des valeurs de variables. 

Notez que les modèles ne sont pas distincts : ils peuvent se chevaucher et ne couvrent généralement pas toutes les lignes d’origine. Certaines lignes peuvent n’appartenir à aucun modèle.

**Conseils**

* Utilisez where et project dans le canal d’entrée pour réduire les données uniquement à ce qui vous intéresse.
* Lorsque vous trouvez une ligne intéressante, vous pouvez l’explorer plus en détail en ajoutant ses valeurs spécifiques à votre filtre where.

**Arguments**

* `split=` *nom de la colonne* (obligatoire)
  
    La colonne doit avoir exactement deux valeurs. Si nécessaire, créez une colonne du type :
  
    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`
* `target=` *chaîne*
  
    Indique à l’algorithme de rechercher uniquement les modèles dont le pourcentage est plus élevé dans le jeu de données cible ; la cible doit correspondre à l’une des deux valeurs de la colonne fractionnée.
  
    `requests | evaluate diffpatterns("split=success", "target=false")`
* `threshold=` *0.015<double<1* (valeur par défaut : 0,05) 
  
    Définit la différence minimale de modèle (taux) entre les deux jeux.
  
    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`
* `output=minimize | all`
  
    Format des résultats. Les colonnes correspondant au nombre et au pourcentage apparaissent toujours dans les résultats. 
  
  * `minimize` : retire les colonnes indiquant « * » comme seul résultat.
  * `all` : toutes les colonnes de l’entrée sont générées.
* `weight_column=` *column_name*
  
    Considère chaque ligne de l’entrée en fonction de la pondération spécifiée (par défaut, chaque ligne a une pondération de « 1 »). Il est courant d’utiliser une colonne de pondération en prenant en compte l’échantillonnage ou la création de compartiments/l’agrégation des données déjà incorporées dans chaque ligne.
  
    `requests | evaluate autocluster("weight_column=itemCount")`

#### <a name="evaluate-extractcolumns"></a>evaluate extractcolumns
     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns est utilisé pour enrichir une table avec plusieurs colonnes simples qui sont dynamiquement extraites de colonnes (semi-) structurées en fonction de leur type. Actuellement, il prend uniquement en charge les colonnes json (dynamique et sérialisation des chaînes de documents JSON).

* `max_columns=` *int* (valeur par défaut : 10) 
  
    Le nombre de nouvelles colonnes ajoutées est dynamique et peut être très important (en fait, il s’agit du nombre de clés distinctes dans tous les enregistrements json) ; il convient donc de le limiter. Les nouvelles colonnes sont triées dans l’ordre décroissant de leur fréquence et on peut ajouter à la table jusqu’à max_columns.
  
    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`
* `min_percent=` *double* (valeur par défaut : 10) 
  
    Pour limiter les nouvelles colonnes, il est également possible d’ignorer les colonnes dont la fréquence est inférieure à min_percent.
  
    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`
* `add_prefix=` *bool* (valeur par défaut : true) 
  
    Si la valeur est true, le nom de la colonne complexe sera ajouté comme préfixe aux noms de colonnes extraites.
* `prefix_delimiter=` *string* (valeur par défaut : « _ ») 
  
    Si add_prefix=true, ce paramètre définit le délimiteur qui sera utilisé pour concaténer les noms des nouvelles colonnes.
  
    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`
* `keep_original=` *bool* (valeur par défaut : false) 
  
    Si la valeur est true, les colonnes (json) d’origine seront conservées dans la table de sortie.
* `output=query | table` 
  
    Format des résultats. 
  
  * `table` : la sortie est la même table que celle reçue, moins les colonnes d’entrée spécifiées, plus les nouvelles colonnes extraites des colonnes d’entrée.
  * `query` : la sortie est une chaîne représentant la requête que vous feriez pour obtenir le résultat sous forme de table. 

### <a name="extend-operator"></a>opérateur extend
     T | extend duration = stopTime - startTime

Ajoute une ou plusieurs colonnes calculées à une table. 

**Syntaxe**

    T | extend ColumnName = Expression [, ...]

**Arguments**

* *T :* la table d’entrée.
* *ColumnName :* nom de la colonne à ajouter. Les [noms](#names) respectent la casse et peuvent contenir des caractères alphabétiques, numériques ou des traits de soulignement (_). Utilisez `['...']` ou `["..."]` pour entourer les mots-clés ou les noms avec d’autres caractères.
* *Expression :* calcul sur les colonnes existantes.

**Retourne**

Une copie de la table d’entrée, avec les colonnes supplémentaires spécifiées.

**Conseils**

* Préférez [`project`](#project-operator) si vous souhaitez également supprimer ou renommer des colonnes.
* N’utilisez pas seulement `extend` si vous souhaitez obtenir un nom plus court à utiliser dans une expression longue. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 
  
    Les colonnes d’origine de la table ont été indexées. Votre nouveau nom définit une colonne supplémentaire qui n’est pas indexée, c’est pourquoi la requête risque de s’exécuter plus lentement.

**Exemple**

```AIQL
traces
| extend
    Age = now() - timestamp
```

### <a name="find-operator"></a>Opérateur find

    find in (Table1, Table2, Table3) where id=="a string"
    find in (Table1, Table2, Table3) where id=="a string" project column1, column2
    find in (Table1, Table2, Table3) where * has "a string"
    find in (Table1, Table2, Table3) where appName in ("string 1", "string 2", "string 3")

Rechercher les lignes qui correspondent à un prédicat dans un ensemble de tables.

**Syntaxe**

    find in (Table1, ...) 
    where Predicate 
    [project Column1, ...]

**Arguments**

* *Table1* Nom de table ou requête. Ce peut être une table définie par let, mais pas une fonction. Un nom de table est plus performant qu’une requête.
* *Prédicat* Expression booléenne évaluée pour chaque ligne dans les tables spécifiées.
 * Vous pouvez utiliser « * » à la place d’un nom de colonne dans les comparaisons de chaînes
* *Colonne1* L’option `project` vous permet de spécifier les colonnes qui doivent toujours apparaître dans la sortie. 

**Résultat**

Par défaut, la table de sortie contient :

* `source_` - Indicateur de la table source pour chaque ligne. Utilisez [as](#as-operator) à la fin de chaque expression de table si vous souhaitez spécifier le nom qui apparaît dans cette colonne.
* Colonnes explicitement mentionnées dans le prédicat
* Colonnes non vides communes à toutes les tables d’entrée.
* `pack_` - Un jeu de propriétés contenant les données des autres colonnes.

Notez que ce format peut varier en fonction des modifications apportées aux données d’entrée ou au prédicat. Pour spécifier un ensemble fixe de colonnes, utilisez `project`.

**Exemple**

Pour obtenir toutes les demandes et exceptions, à l’exception de celles des robots et des tests de disponibilité :

```AIQL

    find in (requests, exceptions) where isempty(operation_SyntheticSource)
```

Pour trouver toutes les demandes et exceptions émanant du Royaume-Uni, à l’exception de celles des robots et des tests de disponibilité :

```AIQL

    let requk = requests
    | where client_CountryOrRegion == "United Kingdom";
    let exuk = exceptions
    | where client_CountryOrRegion == "United Kingdom";
    find in (requk, exuk) where isempty(operation_SyntheticSource)
```

Recherchez les données de télémétrie les plus récentes, dans lesquelles un champ contient le terme « test » :

```AIQL

    find in (traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions) 
    where * has 'test' 
    | top 100 by timestamp desc
```

**Conseils sur les performances**

* Ajoutez des critères temporels au prédicat `where`.
* Utilisez des clauses `let` au lieu d’écrire des requêtes en ligne.

### <a name="getschema-operator"></a>opérateur getschema

   T | getschema
   
Génère une table qui affiche les noms de colonnes et les types de la table d’entrée.

```AIQL
requests
| project appId, appName, customDimensions, duration, iKey, itemCount, success, timestamp 
| getschema 
```

![Résultats de getschema](./media/app-insights-analytics-reference/getschema.png)

### <a name="join-operator"></a>opérateur join
    Table1 | join (Table2) on CommonColumn

Fusionne les lignes de deux tables en faisant correspondre les valeurs de la colonne spécifiée.

**Syntaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Arguments**

* *Table1* : « côté gauche » de la jointure.
* *Table2* : « côté droit » de la jointure. Il peut s’agir d’une expression de requête imbriquée générant une table.
* *CommonColumn* : colonne portant le même nom dans les deux tables.
* *Kind* : indique comment les lignes des deux tables doivent être mises en correspondance.

**Retourne**

Une table avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table, dont tous les champs `on` ont la même valeur qu’une ligne dans l’autre table. 
* `Kind` non spécifié ou `= innerunique`
  
    Une seule ligne du côté gauche correspond à chaque valeur de la clé `on` . La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.
* `kind=inner`
  
     La sortie contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit.
* `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)
  
     Outre les correspondances internes, on trouve une ligne pour chaque ligne du côté gauche (et/ou droit), même s’il n’y a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs null.
* `kind=leftanti`
  
     Retourne tous les enregistrements du côté gauche n’ayant pas de correspondance du côté droit. La table de résultats ne comporte que les colonnes du côté gauche. 
* `kind=leftsemi` (ou `leftantisemi`)

    Retourne une ligne de la table de gauche en cas de correspondance (ou pas) dans la table de droite. Le résultat n’inclut pas les données de la table de droite.


**Conseils**

Le tableau de résultats est limité à 64 Mo.

Pour un résultat optimal :

* Utilisez `where` et `project` pour réduire le nombre de lignes et de colonnes dans les tables d’entrée, avant le `join`. 
* Si une table est toujours plus petite que l’autre, utilisez-la pour le côté gauche de la jointure.
* Les colonnes de la correspondance de jointure doivent avoir le même nom. Utilisez l’opérateur project si nécessaire pour renommer une colonne dans l’une des tables.



**Exemple**

Obtenez les activités étendues d’un journal dans lequel certaines entrées marquent le début et la fin d’une activité. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
    | where Name == "Start"
    | project Name, City, ActivityId, StartTime=timestamp
    | join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
    | project City, ActivityId, StartTime, StopTime, Duration=StopTime-StartTime

```


### <a name="limit-operator"></a>opérateur limit
     T | limit 5

Retourne au maximum le nombre spécifié de lignes de la table d’entrée. Il n’est pas possible de savoir quels enregistrements vont être retournés. (Pour retourner des enregistrements spécifiques, utilisez [`top`](#top-operator).)

**Alias** `take`

**Syntaxe**

    T | limit NumberOfRows


**Conseils**

`Take` est un moyen simple et efficace d’afficher un échantillon de vos résultats quand vous travaillez de manière interactive. N’oubliez pas qu’il ne garantit pas de produire des lignes spécifiques ou de les afficher dans un ordre particulier.

Il existe une limite implicite quant au nombre de lignes renvoyées au client, même si vous n’utilisez pas `take`. Pour relever cette limite, utilisez l’option de requête client `notruncation` .

### <a name="make-series-operator"></a>opérateur make-series

Effectue une agrégation. Contrairement à [summarize](#summarize-operator), il existe une ligne de sortie pour chaque groupe. Dans les colonnes de résultats, les valeurs dans chaque groupe sont regroupées dans des tableaux. 

**Syntaxe**

    T | 
    make-series [Column =] Aggregation default = DefaultValue [, ...] 
    on AxisColumn in range(start, stop, step) 
    by [Column =] GroupExpression [, ...]


**Arguments**

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *DefaultValue :* s’il n’existe aucune ligne avec des valeurs spécifiques AxisColumn et GroupExpression, DefaultValue est attribué dans les résultats de l’élément correspondant du tableau. 
* *Aggregation :* expression numérique utilisant une [fonction d’agrégation](#aggregations). 
* *AxisColumn :* colonne sur laquelle la série est triée. Elle peut être considérée comme une chronologie, mais tous les types numériques sont acceptés.
*start, stop, step :* définit la liste des valeurs AxisColumn pour chaque ligne. Chaque autre colonne d’agrégation du résultat inclut un tableau de même longueur. 
* *GroupExpression :* expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. Il existe une ligne dans la sortie pour chaque valeur de GroupExpression. En règle générale, il s’agit d’un nom de colonne qui fournit déjà un ensemble limité de valeurs. 

**Conseil**

Les tableaux de résultats sont rendus dans un graphique Analytics de la même manière que l’opération summarize correspondante.

**Exemple**

```AIQL
requests
| make-series sum(itemCount) default=0, avg(duration) default=0
  on timestamp in range (ago(7d), now(), 1d)
  by client_City
```

![Résultats de make-series](./media/app-insights-analytics-reference/make-series.png)

### <a name="mvexpand-operator"></a>opérateur mvexpand
    T | mvexpand listColumn 

Développe une liste à partir d’une cellule de type dynamique (JSON) afin que chaque entrée comporte une ligne distincte. Toutes les autres cellules d’une ligne développée sont dupliquées. 

(Voir aussi [`summarize makelist`](#summarize-operator) qui effectue la fonction inverse.)

**Exemple**

En partant du principe que la table d’entrée est :

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |[0,1,"k","v"] |

    mvexpand D

Voici le résultat :

| A:int | B:string | D:dynamic |
| --- | --- | --- |
| 1 |"hello" |{"key":"value"} |
| 2 |"world" |0 |
| 2 |"world" |1 |
| 2 |"world" |"k" |
| 2 |"world" |"v" |

**Syntaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Arguments**

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* *Typename :* convertit l’expression développée en un type spécifique.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 128.

**Retourne**

Plusieurs lignes pour chacune des valeurs dans n’importe quel tableau dans la colonne nommée ou dans l’expression de tableau.

La colonne développée est toujours de type dynamique. Utilisez une conversion telle que `todatetime()` ou `toint()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :

* `bagexpansion=bag`: les conteneurs de propriétés sont développés en conteneurs de propriétés à entrée unique. Il s’agit du développement par défaut.
* `bagexpansion=array` : les conteneurs de propriétés sont développés en structures de tableau `[`*clé*`,`*valeur*`]` à deux éléments permettant un accès uniforme aux clés et valeurs (par exemple, pour effectuer également une agrégation de comptage des différents noms de propriété).

**Exemples**

    exceptions | take 1
    | mvexpand details[0]

Fractionne un enregistrement d’exception en plusieurs lignes pour chaque élément du champ de détails.

### <a name="parse-operator"></a>opérateur parse
    T | parse "I got 2 socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old"
    with * "got" counter:long " " present "for" * "was" year:long *

    T |  parse kind=regex "I got socks for my 63rd birthday"
    with "(I|She) got " present " for .*?" year:long *

Extrait les valeurs d’une chaîne. Peut utiliser une correspondance d’expression simple ou régulière.

**Syntaxe**

    T | parse [kind=regex|relaxed] SourceText
        with [Match | Column [: Type [*]] ]  ...

**Arguments**

* `T`: table d’entrée.
* `kind` : 
  * `simple` (par défaut) : les chaînes `Match` sont des chaînes de texte brut.
  * `relaxed` : si le texte n’est pas analysé comme le type d’une colonne, la colonne est définie sur Null et l’analyse continue. 
  * `regex` : les chaînes `Match` sont des expressions régulières.
* `Text`: colonne ou autre expression pouvant être convertie en chaîne ou en prendre la valeur.
* *Match :* fait correspondre la partie suivante de la chaîne et la rejette.
* *Column :* affecte la partie suivante de la chaîne à cette colonne. La colonne est créée si elle n’existe pas.
* *Type :* analyse la partie suivante de la chaîne comme le type spécifié (par exemple, int, date ou double). 

**Retourne**

La table d’entrée, étendue en fonction de la liste des colonnes.

Les éléments de la clause `with` sont ensuite mis en correspondance avec le texte source. Chaque élément traite un fragment du texte source : 

* Une chaîne littérale ou une expression régulière déplace le curseur correspondant de la longueur de la correspondance.
* Dans une analyse regex, une expression régulière peut utiliser l’opérateur de réduction « ? » pour passer dès que possible à la correspondance suivante.
* Un nom de colonne comportant un type analyse le texte en tant que type spécifié. Une analyse infructueuse invalide la correspondance avec le modèle entier, sauf si kind=relaxed.
* Un nom de colonne sans type ou comportant le type « string », copie le nombre minimal de caractères pour parvenir à la correspondance suivante.
* « * » ignore le nombre minimal de caractères pour parvenir à la correspondance suivante. Vous pouvez utiliser « * » au début et à la fin du modèle, ou après un type autre que chaîne, ou entre les correspondances de chaîne.

Dans un modèle d’analyse, tous les éléments doivent correspondre correctement ; dans le cas contraire, aucun résultat n’est produit. Il existe une exception à cette règle : lorsque kind=relaxed, si l’analyse d’une variable typée échoue, le reste de l’analyse continue.

**Exemples**

*Simple :*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

| x | counter | present | Year |
| --- | --- | --- | --- |
| 1 |2 |socks |63 |

*Relaxed :*

Lorsque l’entrée contient une correspondance correcte pour chaque colonne typée, une analyse assouplie (relaxed) produit les mêmes résultats qu’une analyse simple. Mais si l’une des colonnes typées n’analyse pas correctement, une analyse assouplie continue à traiter le reste du modèle, tandis qu’une analyse simple s’arrête et ne parvient pas à générer des résultats.

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


| x | present | Year |
| --- | --- | --- |
| 1 |socks |63 |

*Regex :*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 07:31, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 06:20:00 ) }" 
// Parse it:
| parse kind=regex s 
  with ".*?=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       @".*\)" *
| project-away x, s
```

| resource | slice | lock | release | previous |
| --- | --- | --- | --- | --- |
| Scheduler |16 |17/02/2016 07:31:00 |02/17/2016 08:41 |2016-02-17T06:20:00Z |

### <a name="project-operator"></a>opérateur project
    T | project cost=price*quantity, price

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérez les nouvelles colonnes calculées. L’ordre des colonnes dans le résultat est déterminé par l’ordre des arguments. Seules les colonnes spécifiées dans les arguments sont incluses dans le résultat : les autres sont supprimées.  (Voir aussi `extend`.)

**Syntaxe**

    T | project ColumnName [= Expression] [, ...]

**Arguments**

* *T :* la table d’entrée.
* *ColumnName :* nom d’une colonne à afficher dans la sortie. S’il n’y a aucune *Expression*, une colonne portant ce nom doit apparaître dans l’entrée. Les [noms](#names) respectent la casse et peuvent contenir des caractères alphabétiques, numériques ou des traits de soulignement (_). Utilisez `['...']` ou `["..."]` pour entourer les mots-clés ou les noms avec d’autres caractères.
* *Expression :* expression scalaire facultative faisant référence aux colonnes d’entrée. 
  
    Il est possible de retourner une nouvelle colonne calculée portant le même nom qu’une colonne figurant dans l’entrée.

**Retourne**

Une table contenant les colonnes nommées en tant qu’arguments, et autant de lignes que la table d’entrée.

**Exemple**

L’exemple suivant présente plusieurs types de manipulations pouvant être effectuées à l’aide de l’opérateur `project` . La table d’entrée `T` comporte trois colonnes de type `int` : `A`, `B` et `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>opérateur project-away
    T | project-away column1, column2, ...

Exclure les colonnes spécifiées. Le résultat contient toutes les colonnes d’entrée, sauf celles que vous nommez.

### <a name="range-operator"></a>opérateur range
    range LastWeek from ago(7d) to now() step 1d

Génère une table de valeurs à une seule colonne. Notez qu’elle ne comporte pas d’entrée de pipeline. 

| LastWeek |
| --- |
| 2015-12-05 09:10:04.627 |
| 2015-12-06 09:10:04.627 |
| ... |
| 2015-12-12 09:10:04.627 |

**Syntaxe**

    range ColumnName from Start to Stop step Step

**Arguments**

* *ColumnName :* nom de la seule colonne dans la table de sortie.
* *Start :* plus petite valeur de la sortie.
* *Stop :* valeur la plus élevée générée dans la sortie (ou limite de la valeur la plus élevée, si *step* dépasse cette valeur).
* *Step :* différence entre deux valeurs consécutives. 

Les arguments doivent être des valeurs de type numérique, date ou durée. Ils ne peuvent pas faire référence aux colonnes d’une table. (Si vous souhaitez calculer la plage d’après une table d’entrée, utilisez la [fonction *range*](#range), éventuellement avec [l’opérateur mvexpand](#mvexpand-operator)). 

**Retourne**

Une table comportant une seule colonne nommée *ColumnName*, dont les valeurs sont *Start*, *Start* + *Step*... jusqu’à *Stop* inclus.

**Exemple**  

```AIQL
range Steps from 1 to 8 step 3
```

Une table comportant une seule colonne nommée `Steps` dont le type est `long` et dont les valeurs sont `1`, `4` et `7`.

**Exemple**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Table des occurrences de minuit au cours des sept derniers jours. La fonction d’emplacement (floor) diminue à chaque fois au début de la journée.

**Exemple**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Montre comment utiliser l’opérateur `range` pour créer une petite table ad hoc de dimensions servant à intégrer des zéros là où les données sources ne contiennent aucune valeur.

### <a name="reduce-operator"></a>opérateur reduce
    exceptions | reduce by outerMessage

Tente de regrouper des enregistrements similaires. Pour chaque groupe, l’opérateur génère le `Pattern` décrivant le mieux ce groupe et le `Count` d’enregistrements dans ce groupe.

![](./media/app-insights-analytics-reference/reduce.png)

**Syntaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Arguments**

* *ColumnName :* colonne à examiner. Cette valeur doit être de type chaîne.
* *Threshold :* valeur dans la plage {0 à 1}. La valeur par défaut est 0,001. Pour les entrées volumineuses, le seuil doit être bas. 

**Retourne**

Deux colonnes, `Pattern` et `Count`. Dans de nombreux cas, le modèle est une valeur complète de la colonne. Dans certains cas, il peut identifier des termes courants et remplacer les parties variables avec '*'.

Par exemple, le résultat de `reduce by city` peut inclure : 

| Modèle | Nombre |
| --- | --- |
| San * |5182 |
| Saint * |2846 |
| Moscow |3726 |
| \* -on- \* |2730 |
| Paris |27163 |

### <a name="render-directive"></a>directive render
    T | render [ table | timechart  | barchart | piechart | areachart | scatterchart ] 
        [kind= default|stacked|stacked100|unstacked]

Render indique à la couche de présentation comment afficher la table. Il doit s’agir du dernier élément du canal. C’est une alternative pratique à l’utilisation des commandes affichées, ce qui vous permet d’enregistrer une requête avec une méthode de présentation particulière.

Pour certains types de graphiques, `kind` propose d’autres options. Par exemple, un graphique à barres `stacked` segmente chaque barre selon une dimension choisie, en affichant la contribution au total des différentes valeurs de la dimension. Dans un graphique `stacked100`, chaque barre a la même hauteur de 100 %, pour pouvoir comparer les contributions.


### <a name="restrict-clause"></a>clause restrict
Spécifie l’ensemble de noms de tables disponibles pour les opérateurs qui suivent. Par exemple :

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sample-operator"></a>opérateur d’échantillon

Renvoie des lignes aléatoires distribuées de manière uniforme à partir de la table d’entrée.


**Syntaxe**

    T | sample NumerOfRows

* *NumberOfRows :* le nombre de lignes à renvoyer dans l’échantillon.

**Conseil**

Utilisez `Take` lorsque vous n’avez pas besoin d’un échantillon distribué de manière uniforme.


### <a name="sample-distinct-operator"></a>opérateur sample-distinct

Renvoie une seule colonne qui contient le nombre spécifié de valeurs distinctes de la colonne demandée. Ne renvoie pas actuellement un échantillon distribué uniformément.

**Syntaxe**

    T | sample-distinct NumberOfValues of ColumnName

* *NumberOfValues* : la longueur de la table que vos souhaitez.
* *ColumnName :* colonne que vous souhaitez.

**Conseils**

Peut être pratique pour échantillonner une population en plaçant sample-distinct dans une instruction let et ultérieurement filtrer à l’aide de l’opérateur in (voir l’exemple).
 
Si vous souhaitez obtenir les valeurs principales, plutôt qu’un simple échantillon, vous pouvez utiliser l’opérateur top-hitters.

Si vous souhaitez échantillonner des lignes de données (au lieu des valeurs d’une colonne spécifique), reportez-vous à [l’opérateur sample](#sample-operator).

**Exemple**

Échantillonnez une population et faites davantage de calculs en sachant que la synthèse ne dépassera pas les limites de requête.

```AIQL
let sampleops = toscalar(requests | sample-distinct 10 of OperationName);
requests | where OperationName in (sampleops) | summarize total=count() by OperationName
```
### <a name="search-operator"></a>opérateur search

Recherchez des chaînes dans plusieurs tables et colonnes.

**Syntaxe**

    search [kind=case_sensitive] [in (TableName, ...)] SearchToken

    T | search [kind=case_sensitive] SearchToken

    search [kind=case_sensitive] [in (TableName, ...)] SearchPredicate

    T | search [kind=case_sensitive] SearchPredicate

Recherche des occurrences de la chaîne de jeton donnée dans n’importe quelle colonne d’une table.
 
* *TableName* : nom d’une table définie globalement (requêtes, exceptions, ...) ou par une [clause let](#let-clause). Vous pouvez utiliser des caractères génériques tels que r*.
* *SearchToken :* chaîne de jeton qui doit correspondre à un mot entier. Vous pouvez utiliser des caractères génériques de fin. « Amster* » correspond à « Amsterdam », mais pas « Amster ».
* *SearchPredicate :* expression booléenne sur les colonnes dans les tables. Vous pouvez utiliser « * » comme caractère générique dans les noms de colonnes.

**Exemples**

```AIQL
search "Amster*"  //All columns, all tables

search name has "home"  // one column

search * has "home"     // all columns

search in (requests, exceptions) "Amster*"  // two tables

requests | search "Amster*"

requests | search name has "home"

```




### <a name="sort-operator"></a>opérateur sort
    T | sort by country asc, price desc

Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

**Alias** `order`

**Syntaxe**

    T  | sort by Column [ asc | desc ] [ , ... ]

**Arguments**

* *T :* table d’entrée à trier.
* *Column :* colonne de *T* d’après laquelle trier. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` Tri par ordre croissant, de faible à élevé. La valeur par défaut est `desc`, par ordre décroissant allant d’élevé à faible.

**Exemple**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Toutes les lignes de la table Traces ayant un `ActivityId`spécifique, triées d’après leur horodatage.

### <a name="summarize-operator"></a>opérateur summarize
Génère une table qui agrège le contenu de la table d’entrée.

    requests
    | summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Une table qui indique le nombre, la durée moyenne des requêtes et un ensemble de villes dans chaque pays. La sortie comporte une ligne pour chaque pays. Les colonnes de sortie affichent le nombre, la durée moyenne, les villes et le pays. Toutes les autres colonnes d’entrée sont supprimées.

    T | summarize count() by price_range=bin(price, 10.0)

Une table indiquant le nombre d’éléments ayant un prix dans chaque intervalle [0,10.0], [10.0,20.0] et ainsi de suite. Cet exemple comprend une colonne pour le nombre et une autre pour la gamme de prix. Toutes les autres colonnes d’entrée sont supprimées.

**Syntaxe**

    T | summarize
         [  [ Column = ] Aggregation [ , ... ] ]
         [ by
            [ Column = ] GroupExpression [ , ... ] ]

**Arguments**

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression. Les [noms](#names) respectent la casse et peuvent contenir des caractères alphabétiques, numériques ou des traits de soulignement (_). Utilisez `['...']` ou `["..."]` pour entourer les mots-clés ou les noms avec d’autres caractères.
* *Aggregation :* appel d’une fonction d’agrégation telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir [Agrégations](#aggregations).
* *GroupExpression :* expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En général, il s’agit d’un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou de `bin()` avec une colonne numérique ou horaire en tant qu’argument. 

Si vous fournissez une expression numérique ou horaire sans utiliser `bin()`, Analytics l’applique automatiquement avec un intervalle de `1h` pour les heures ou de `1.0` pour les nombres.

Si vous ne fournissez pas une expression *GroupExpression* , la table est entièrement résumée dans une ligne de sortie unique.

**Retourne**

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs que les expressions `by` . Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by` et au moins une colonne pour chaque agrégation calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat contient autant de lignes qu’il existe de combinaisons de valeurs `by` . Si vous souhaitez générer une synthèse sur des plages de valeurs numériques, utilisez `bin()` pour limiter les plages aux valeurs discrètes.

> [!NOTE]
> Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou d’appliquer `bin()` à une colonne numérique.

### <a name="table-operator"></a>Opérateur table

    table('pageViews')

La table nommée dans la chaîne d’argument.

**Syntaxe**

    table(tableName)

**Arguments**

* *tableName :* chaîne. Le nom d’une table qui peut être statique ou le résultat d’une clause let.

**Exemples**

    table('requests');


    let size = (tableName: string) {
        table(tableName) | summarize sum(itemCount)
    };
    size('pageViews');



### <a name="take-operator"></a>opérateur take
Alias de [limit](#limit-operator)

### <a name="top-operator"></a>opérateur top
    T | top 5 by Name desc nulls first

Retourne les *N* premiers enregistrements triés d’après les colonnes spécifiées.

**Syntaxe**

    T | top NumberOfRows by Sort_expression [ asc | desc ] [nulls first|nulls last] [, ... ]

**Arguments**

* *NumberOfRows :* nombre de lignes de *T* à retourner.
* *Sort_expression :* expression selon laquelle trier les lignes. Il s’agit généralement juste d’un nom de colonne. Vous pouvez spécifier plusieurs expressions sort_expression.
* `asc` ou `desc` (valeur par défaut) peut s’afficher pour indiquer si la sélection provient du bas ou du haut de la plage.
* `nulls first` ou `nulls last` contrôles dans lesquelles des valeurs null apparaissent. `First` est la valeur par défaut pour `asc`, `last` est la valeur par défaut pour `desc`.

**Conseils**

`top 5 by name` équivaut approximativement à `sort by name | take 5`. Toutefois, cet opérateur s’exécute plus rapidement et retourne toujours des résultats triés, contrairement à `take` .

### <a name="top-nested-operator"></a>Opérateur top-nested
    requests
    | top-nested 5 of name by count()
    , top-nested 3 of performanceBucket by count()
    , top-nested 3 of client_CountryOrRegion by count()
    | render barchart

Produit des résultats hiérarchiques, où chaque niveau est inférieur au niveau précédent. Il est utile pour répondre à des questions comme « Quelles sont les 5 principales requêtes et, pour chacune d’elles, quels sont les 5 principaux compartiments de performances et, pour chacun d’eux, quels sont les 3 principaux pays dont proviennent les demandes? »

**Syntaxe**

   T | top-nested N of COLONNE by AGRÉGATION [, ...]

**Arguments**

* N:int : nombre de lignes à retourner ou à passer au niveau suivant. Dans une requête à trois niveaux où N est 5, 3 et 3, le nombre total de lignes sera 45.
* COLONNE : colonne d’après laquelle regrouper pour l’agrégation. 
* AGRÉGATION : [fonction d’agrégation](#aggregations) à appliquer à chaque groupe de lignes. Les résultats de ces agrégations déterminent les principaux groupes à afficher.

### <a name="union-operator"></a>opérateur union
     Table1 | union Table2, Table3

Prend deux tables ou plus et retourne les lignes de toutes les tables. 

**Syntaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Arguments**

* *Table1*, *Table2* ...
  * Nom d’une table, tel que `requests`, ou table définie dans une [clause let](#let-clause), ou
  * Expression de requête, telle que `(requests | where success=="True")`
  * Ensemble de tables spécifié par un caractère générique. Par exemple, `e*` unirait toutes les tables définies dans les clauses let précédentes, dont le nom commence par « e », ainsi que les tables « exceptions ».
* `kind`: 
  * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
  * `outer` : le résultat comporte toutes les colonnes qui apparaissent dans les entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée prennent la valeur `null`.
* `withsource=`*ColumnName :* si cet élément est spécifié, la sortie contient une colonne nommée *ColumnName*, dont la valeur indique la table source correspondant à chaque ligne. Utilisez [as](#as-operator) à la fin de chaque expression de table si vous souhaitez spécifier le nom qui apparaît dans cette colonne.

**Retourne**

Une table comportant autant de lignes que l’ensemble des tables d’entrée et autant de colonnes que de noms de colonne uniques dans les entrées.

Les lignes n’ont pas d’ordre garanti.


**Exemple**

Le nombre d’utilisateurs ayant produit un événement `exceptions` ou un événement `traces` au cours des 12 dernières heures. Dans le résultat, la colonne « SourceTable » indique des « exceptions » ou des « suivis » :

```AIQL
    
    union withsource=SourceTable kind=outer exceptions, traces
    | where timestamp > ago(12h)
    | summarize dcount(user_Id) by SourceTable
```

Cette version plus efficace génère le même résultat. Elle filtre chaque table avant la création de l’union :

```AIQL
    exceptions
    | where timestamp > ago(24h) | as exceptions
    | union withsource=SourceTable kind=outer (requests | where timestamp > ago(12h) | as traces)
    | summarize dcount(user_Id) by SourceTable 
```

Utilisez [as](#as-operator) pour spécifier le nom qui apparaîtra dans la colonne source.

#### <a name="forcing-an-order-of-results"></a>Forcer l’ordre des résultats

L’union ne garantit pas un ordre spécifique des lignes de résultats.
Pour obtenir le même ordre chaque fois que vous exécutez la requête, ajoutez une colonne de balises à chaque table d’entrée :

    let r1 = (traces | count | extend tag = 'r1');
    let r2 = (requests | count| extend tag = 'r2');
    let r3 = (pageViews | count | extend tag = 'r3');
    r1 | union r2,r3 | sort by tag

#### <a name="see-also"></a>Voir aussi

Pensez à l’[opérateur join](#join-operator) comme alternative.

### <a name="where-operator"></a>opérateur where
     requests | where resultCode=="200"

Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

**Alias** `filter`

**Syntaxe**

    T | where Predicate
    T | where * has Term

**Arguments**

* *T* : entrée tabulaire dont les enregistrements doivent être filtrés.
* *Predicate :* `boolean` [expression](#boolean) sur les colonnes de *T*. Elle est évaluée pour chaque ligne dans *T*.
* *Terme* : chaîne qui doit correspondre à la totalité d’un mot dans une colonne.

**Retourne**

Les lignes de *T* dont *Predicate* est `true`.

**Conseils**

Pour obtenir des performances optimales :

* **Utilisez des comparaisons simples** entre les noms de colonne et les constantes. (« Constante » s’entend dans le sens de constante au fil de la table, de telle sorte que `now()` et `ago()` soient OK, tout comme les valeurs scalaires affectées à l’aide d’une [`let`clause](#let-clause).)
  
    Par exemple, préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.
* **Simplest terms first** (termes les plus simples en premier) : si vous avez plusieurs clauses unies par `and`, insérez d’abord les clauses n’impliquant qu’une seule colonne. C’est pourquoi `Timestamp > ago(1d) and OpId == EventId` est plus adapté.

**Exemple**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Enregistrements datant de moins de 1 heure et provenant de la source nommée « Kuskus » et ayant deux colonnes de la même valeur. 

Notez que nous plaçons la comparaison entre deux colonnes à la fin, car elle ne peut pas utiliser l’index et force une analyse.



## <a name="aggregations"></a>Agrégations
Les agrégations sont des fonctions utilisées pour combiner des valeurs dans les groupes créés dans [l’opération de résumé](#summarize-operator). Par exemple, dans cette requête, dcount() est une fonction d’agrégation :

    requests | summarize dcount(name) by success

### <a name="any"></a>any
    any(Expression)

Sélectionne une ligne du groupe de façon aléatoire et retourne la valeur de l’expression spécifiée.

Cette fonction est utile, par exemple, quand une colonne a un grand nombre de valeurs similaires (par exemple, une colonne « texte d’erreur ») et que vous souhaitez échantillonner cette colonne une fois en fonction d’une valeur unique de la clé de groupe composée. 

**Exemple**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>

### <a name="argmin-argmax"></a>argmin, argmax
    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Recherche dans le groupe la ligne qui applique une valeur minimale/maximale de *ExprToMaximize* et retourne la valeur de *ExprToReturn* (ou `*` pour renvoyer la ligne entière).

**Conseil**: les colonnes analysées sont automatiquement renommées. Pour vérifier que vous utilisez les noms corrects, examinez les résultats à l’aide de `take 5` avant de les transmettre à un autre opérateur.

**Exemples**

Pour chaque nom de requête, afficher à quel moment la requête la plus longue s’est produite :

    requests | summarize argmax(duration, timestamp) by name

Afficher tous les détails de la requête la plus longue, pas seulement l’horodatage :

    requests | summarize argmax(duration, *) by name


Trouver la plus petite valeur de chaque métrique, ainsi que son horodatage et d’autres données :

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)

### <a name="avg"></a>avg
    avg(Expression)

Calcule la moyenne de *Expression* dans le groupe.

### <a name="buildschema"></a>buildschema
    buildschema(DynamicExpression)

Retourne le schéma minimal qui admet toutes les valeurs de *DynamicExpression*. 

Le type de la colonne de paramètre doit être `dynamic` (tableau ou conteneur de propriétés). 

**Exemple**

    exceptions | summarize buildschema(details)

Résultat :

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

`indexer` indique où vous devez utiliser un index numérique. Pour ce schéma, voici certains chemins valides (en supposant que ces exemples d’index appartiennent à la plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Exemple**

Supposons que la colonne d’entrée a trois valeurs dynamiques :

|  |
| --- |
| `{"x":1, "y":3.5}` |
| `{"x":"somevalue", "z":[1, 2, 3]}` |
| `{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}` |

Le schéma résultant serait :

    {
      "x":["int", "string"],
      "y":["double", {"w": "string"}],
      "z":{"indexer": ["int", "string"]},
      "t":{"indexer": "string"}
    }

Le schéma fournit les informations suivantes :

* L’objet racine est un conteneur avec quatre propriétés nommées x, y, z et t.
* La propriété appelée « x » peut être de type « int » ou « string ».
* La propriété appelée « y » peut être de type « double », ou un autre conteneur avec une propriété appelée « w » de type « string ».
* Le mot-clé ``indexer`` indique que « z » et « t » sont des tableaux.
* Chaque élément du tableau « z » est un entier ou une chaîne.
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement facultative et tout tableau peut être vide.

##### <a name="schema-model"></a>Modèle de schéma
La syntaxe du schéma retourné est la suivante :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"indexer"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Le schéma s’apparente à un sous-ensemble d’annotations de type TypeScript, encodées sous la forme d’une valeur dynamique. En Typescript, l’exemple de schéma serait le suivant :

    var someobject:
    {
      x?: (number | string),
      y?: (number | { w?: string}),
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string }
    }


### <a name="count"></a>count
    count([ Predicate ])

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`. Si *Predicate* n’est pas spécifié, retourne le nombre total d’enregistrements dans le groupe. 

**Conseil pour optimiser les performances** : utilisez `summarize count(filter)` à la place de `where filter | summarize count()`.

> [!NOTE]
> Évitez d'utiliser count() pour rechercher le nombre de demandes, d’exceptions ou autres événements qui se sont produits. Quand [l’échantillonnage](app-insights-sampling.md) est en cours, le nombre de points de données dans Application Insights est inférieur au nombre d’événements d’origine. Utilisez plutôt `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d'événements originaux qui sont représentés par chaque point de données conservé.
> 
> 

### <a name="countif"></a>countif
    countif(Predicate)

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

**Conseil pour optimiser les performances** : utilisez `summarize countif(filter)` à la place de `where filter | summarize count()`.

> [!NOTE]
> Évitez d’utiliser countif() pour rechercher le nombre de demandes, d’exceptions ou autres événements qui se sont produits. Quand [l’échantillonnage](app-insights-sampling.md) est en cours, le nombre de points de données est inférieur au nombre d’événements réels. Utilisez plutôt `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d'événements originaux qui sont représentés par chaque point de données conservé.
> 
> 

### <a name="dcount"></a>dcount
    dcount( Expression [ ,  Accuracy ])

Retourne une estimation du nombre de valeurs distinctes de *Expr* dans le groupe. (Pour afficher les valeurs distinctes, utilisez [`makeset`](#makeset).)

Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.

* `0` : calcul le moins précis, mais le plus rapide.
* `1` (valeur par défaut) : meilleur compromis entre précision et vitesse de calcul (environ 0,8 % de marge d’erreur).
* `2` : calcul le plus précis, mais le plus lent (environ 0,4 % de marge d’erreur).

**Exemple**

    pageViews 
    | summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)

### <a name="dcountif"></a>dcountif
    dcountif( Expression, Predicate [ ,  Accuracy ])

Retourne une estimation du nombre de valeurs distinctes de *l’Expr* des lignes du groupe pour lesquelles *Predicate* a la valeur true. (Pour afficher les valeurs distinctes, utilisez [`makeset`](#makeset).)

Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.

* `0` : calcul le moins précis, mais le plus rapide.
* `1` (valeur par défaut) : meilleur compromis entre précision et vitesse de calcul (environ 0,8 % de marge d’erreur).
* `2` : calcul le plus précis, mais le plus lent (environ 0,4 % de marge d’erreur).

**Exemple**

    pageViews 
    | summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist
    makelist(Expr [ ,  MaxListSize ] )

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe. 

* *MaxListSize* est une limite de nombre entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *128*).

### <a name="makeset"></a>makeset
    makeset(Expression [ , MaxSetSize ] )

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe. (Conseil : pour ne comptabiliser que les valeurs distinctes, utilisez [`dcount`](#dcount).)

* *MaxSetSize* est une limite de nombre entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *128*).

**Exemple**

    pageViews 
    | summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Consultez aussi [`mvexpand`](#mvexpand-operator) pour la fonction inverse.

### <a name="max-min"></a>max, min
    max(Expr)

Calcule la valeur maximale de *Expr*.

    min(Expr)

Calcule la valeur minimale de *Expr*.

**Conseil**: ces fonctions ne donnent que des valeurs maximales ou minimales, par exemple le prix le plus élevé ou le plus bas. Pour que la ligne contienne d’autres colonnes, par exemple le nom du fournisseur proposant le prix le plus bas, utilisez [argmin ou argmax](#argmin-argmax).

<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>

### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentile, percentiles, percentilew, percentilesw
    percentile(Expression, Percentile)

Retourne une estimation de *Expression* du centile spécifié dans le groupe. La précision dépend de la densité de population dans la région du centile.

    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Similaire à `percentile()`, mais calcule un nombre de valeurs de centile (opération plus rapide que le calcul de chaque centile individuellement).

    percentilew(Expression, WeightExpression, Percentile)

Centile pondéré. À utiliser pour les données pré-agrégées.  `WeightExpression` est un entier qui indique le nombre de lignes d’origine représentées par chaque ligne agrégée.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Similaire à `percentilew()`, mais calcule un nombre de valeurs de centile.

**Exemples**

Pour chaque nom de requête, la valeur de `duration` qui est supérieure à 95 % de l’échantillon et inférieure à 5 % de l’échantillon :

    request 
    | summarize percentile(duration, 95)
      by name

Omettez « by... » pour calculer la table entière.

Calculer simultanément plusieurs centiles pour différents noms de requête :

    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Les résultats montrent que pour la requête /Events/Index, 5 % des requêtes reçoivent une réponse en moins de 2,44 s, la moitié en 3,52 s et 5 % en plus de 6,85 s.

Calculer plusieurs statistiques :

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Centiles pondérés
Utilisez les fonctions de centile pondéré lorsque les données ont été pré-agrégées. 

Par exemple, supposons que votre application exécute des milliers d’opérations par seconde et que vous souhaitez connaître leur latence. La solution simple consisterait à générer une demande Application Insights ou un événement personnalisé pour chaque opération. Ceci créerait beaucoup de trafic, même si l’échantillonnage adaptatif permettait de le réduire. Mais vous optez pour une solution bien meilleure : écrire du code dans votre application pour agréger les données avant leur envoi à Application Insights. La synthèse agrégée est envoyée à intervalles réguliers, ce qui réduit le débit de données à quelques points par minute.

Votre code affiche un flux de mesures de latence de l’ordre de la milliseconde. Par exemple :

     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Il compte les mesures dans les emplacements suivants : `{ 10, 20, 30, 40, 50, 100 }`

Périodiquement, il effectue une série d’appels TrackEvent, un pour chaque compartiment, avec des valeurs personnalisées dans chaque appel : 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Dans Analytics, vous voyez un groupe d’événements comme celui-ci :

| `opCount` | `latency` | Signification |
| --- | --- | --- |
| 8 |10 |= 8 opérations dans l’emplacement de 10 ms |
| 6 |20 |= 6 opérations dans l’emplacement de 20 ms |
| 3 |30 |= 3 opérations dans l’emplacement de 30 ms |
| 1 |40 |= 1 opération dans l’emplacement de 40 ms |

Pour obtenir une image précise de la distribution d’origine des latences des événements, nous utilisons `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Les résultats sont les mêmes que si nous avions utilisé des `percentiles` bruts sur l’ensemble des mesures d’origine.

> [!NOTE]
> Les centiles pondérés ne s’appliquent pas aux [données échantillonnées](app-insights-sampling.md), où chaque ligne échantillonnée représente un échantillon aléatoire de lignes d’origine, plutôt qu’un emplacement. Les fonctions percentile conviennent pour les données échantillonnées.
> 
> 

#### <a name="estimation-error-in-percentiles"></a>Erreur d’estimation dans les centiles
L’agrégation de centiles fournit une valeur approximative au moyen de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Voici quelques points importants : 

* Les limites de l’erreur d’estimation dépendent de la valeur du centile demandé. Les extrémités de l’échelle [0..100] offrent la meilleure précision, les centiles 0 et 100 étant les valeurs minimale et maximale exactes de la distribution. La précision diminue progressivement vers le milieu de l’échelle. Elle est la plus dégradée à la valeur médiane et est limitée à 1 %. 
* Les limites d’erreur sont observées sur le classement, et non sur la valeur. Supposons que percentile(X, 50) retourne la valeur Xm. L’estimation garantit qu’au moins 49 % et au plus 51 % des valeurs de X sont inférieures à Xm. Il n’existe aucune limite théorique quant à la différence entre Xm et la valeur médiane réelle de X.

### <a name="stdev"></a>stdev
     stdev(Expr)

Retourne l’écart-type de *Expr* sur le groupe.

### <a name="variance"></a>variance
    variance(Expr)

Retourne la variance de *Expr* sur le groupe.

### <a name="sum"></a>sum
    sum(Expr)

Retourne la somme de *Expr* sur le groupe.                      

## <a name="scalars"></a>Valeurs scalaires
[casts](#casts) | [comparaisons](#scalar-comparisons)
<br/>
[gettype](#gettype) | [hash](#hash) | [iff](#iff) |  [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Les types pris en charge sont :

| Type | Nom(s) supplémentaire(s) | Type .NET équivalent |
| --- | --- | --- |
| `bool` |`boolean` |`System.Boolean` |
| `datetime` |`date` |`System.DateTime` |
| `dynamic` | |`System.Object` |
| `guid` |`uuid`, `uniqueid` |`System.Guid` |
| `int` | |`System.Int32` |
| `long` | |`System.Int64` |
| `double` |`real` |`System.Double` |
| `string` | |`System.String` |
| `timespan` |`time` |`System.TimeSpan` |

### <a name="casts"></a>Casts
Vous pouvez effectuer une conversion d’un type en un autre. En général, si la conversion a un sens, cela fonctionnera :

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Vérifier si une chaîne peut être convertie en un type spécifique :

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)







### <a name="scalar-comparisons"></a>Comparaisons scalaires
|  |  |
| --- | --- |
| `<` |Inférieur à |
| `<=` |Inférieur ou égal à |
| `>` |Supérieur à |
| `>=` |Supérieur ou égal à |
| `<>` |Non égal à |
| `!=` |Non égal à |
| `in` |L’opérande de droite est un tableau (dynamique) et l’opérande de gauche est égal à un de ses éléments. |
| `!in` |L’opérande de droite est un tableau (dynamique) et l’opérande de gauche n’est pas égal à un de ses éléments. |

### <a name="gettype"></a>gettype
**Retourne**

Une chaîne représentant le type de stockage sous-jacent de son argument unique. Cela est particulièrement utile pour les valeurs de type `dynamic` : dans ce cas, `gettype()` indique comment une valeur est encodée.

**Exemples**

|  |  |
| --- | --- |
| `gettype("a")` |`"string" ` |
| `gettype(111)` |`"long" ` |
| `gettype(1==1)` |`"int8"` |
| `gettype(now())` |`"datetime" ` |
| `gettype(1s)` |`"timespan" ` |
| `gettype(parsejson('1'))` |`"int" ` |
| `gettype(parsejson(' "abc" '))` |`"string" ` |
| `gettype(parsejson(' {"abc":1} '))` |`"dictionary"` |
| `gettype(parsejson(' [1, 2, 3] '))` |`"array"` |
| `gettype(123.45)` |`"real" ` |
| `gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` |
| `gettype(parsejson(''))` |`"null"` |
| `gettype(1.2)==real` |`true` |

### <a name="hash"></a>Hachage
**Syntaxe**

    hash(source [, mod])

**Arguments**

* *source*: valeur scalaire source à partir de laquelle le hachage est calculé.
* *mod*: valeur de modulo à appliquer au résultat de hachage.

**Retourne**

La valeur xxhash (long) de la valeur scalaire donnée, modulo, la valeur mod donnée (si spécifiée).

**Exemples**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>iff
La fonction `iff()` évalue le premier argument (prédicat) et retourne la valeur du deuxième ou troisième argument selon que le prédicat est `true` ou `false`. Les deuxième et troisième arguments doivent être de même type.

**Syntaxe**

    iff(predicate, ifTrue, ifFalse)


**Arguments**

* *predicate :* expression qui correspond à une valeur `boolean`.
* *ifTrue :* expression calculée, dont la valeur est retournée par la fonction si *predicate* a la valeur `true`.
* *ifFalse :* expression calculée, dont la valeur est retournée par la fonction si *predicate* a la valeur `false`.

**Retourne**

Cette fonction retourne la valeur de *ifTrue* si *predicate* a la valeur `true`,ou la valeur de *ifFalse* dans le cas contraire.

**Exemple**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>

### <a name="isnull-isnotnull-notnull"></a>isnull, isnotnull, notnull
    isnull(parsejson("")) == true

Accepte un argument unique et indique si la valeur est null.

**Syntaxe**

    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Retourne**

True ou false selon si la valeur est null ou not null.

| x | isnull(x) |
| --- | --- |
| "" |false |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**Exemple**

    T | where isnotnull(PossiblyNull) | count

Notez qu’il existe d’autres façons d’obtenir cet effet :

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar
Évalue une requête ou une expression et retourne le résultat sous la forme d’une valeur unique. Cette fonction est utile pour les calculs intermédiaires, par exemple pour calculer un nombre total d’événements puis l’utiliser comme ligne de base.

**Syntaxe**

    toscalar(query)
    toscalar(scalar)

**Retourne**

Argument évalué. Si l’argument est une table, retourne la première colonne de la première ligne. (Nous vous conseillons de faire en sorte que l’argument n’ait qu’une seule colonne et une seule ligne.)

**Exemple**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
    | extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Littéraux booléens
    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Opérateurs booléens
    and 
    or 

### <a name="convert-to-boolean"></a>Convertir en booléen

Si vous avez une chaîne `aStringBoolean` qui contient la valeur « true » ou « false », vous pouvez la convertir en valeur booléenne comme suit :

    booleanResult = aStringBoolean =~ "true"



## <a name="numbers"></a>Nombres
[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Littéraux numériques
|  |  |
| --- | --- |
| `42` |`long` |
| `42.0` |`real` |

### <a name="arithmetic-operators"></a>Opérateurs arithmétiques
|  |  |
| --- | --- |
| + |Ajouter |
| - |Soustraire |
| * |Multiplier |
| / |Diviser |
| % |Modulo |
| `<` |Inférieur à |
| `<=` |Inférieur ou égal à |
| `>` |Supérieur à |
| `>=` |Supérieur ou égal à |
| `<>` |Non égal à |
| `!=` |Non égal à |

### <a name="abs"></a>abs
**Syntaxe**

    abs(x)

**Arguments**

* x : entier, réel ou timespan

**Retourne**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>

### <a name="bin-floor"></a>bin, floor
Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée. Très utilisé dans la requête [`summarize by`](#summarize-operator) . Si vous avez un ensemble de valeurs dispersées, elles seront regroupées dans un plus petit ensemble de valeurs spécifiques.

Alias `floor`.

**Syntaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Arguments**

* *value :* nombre, date ou intervalle de temps. 
* *roundTo :* « taille de l’emplacement ». Nombre, date ou intervalle de temps qui divise *value*. 

**Retourne**

Multiple le plus proche de *roundTo*, inférieur à *value*.  

    (toint(value/roundTo)) * roundTo

**Exemples**

| Expression | Résultat |
| --- | --- |
| `bin(4.5, 1)` |`4.0` |
| `bin(time(16d), 7d)` |`14d` |
| `bin(datetime(1953-04-15 22:25:07), 1d)` |`datetime(1953-04-15)` |

L’expression suivante calcule un histogramme de durées, avec une taille de compartiment de 1 seconde :

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>exp
    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>floor
Alias de [`bin()`](#bin).

### <a name="gamma"></a>gamma
L’opérateur [Fonction gamma](https://en.wikipedia.org/wiki/Gamma_function)

**Syntaxe**

    gamma(x)

**Arguments**

* *x :* nombre réel.

Pour les entiers positifs, `gamma(x) == (x-1)!`. Par exemple, `gamma(5) == 4 * 3 * 2 * 1`.

Voir aussi [loggamma](#loggamma).

### <a name="log"></a>log
    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v` doit être un nombre réel > 0. Sinon, la valeur null est retournée.

### <a name="loggamma"></a>loggamma
Logarithme népérien de la valeur absolue de la [fonction gamma](#gamma).

**Syntaxe**

    loggamma(x)

**Arguments**

* *x :* nombre réel.

### <a name="rand"></a>rand
Générateur de nombres aléatoires.

* `rand()` : nombre réel compris entre 0,0 et 1,0
* `rand(n)` : entier compris entre 0 et n-1

### <a name="sqrt"></a>sqrt
Fonction racine carrée.  

**Syntaxe**

    sqrt(x)

**Arguments**

* *x :* nombre réel >= 0.

**Retourne**

* Nombre positif tel que `sqrt(x) * sqrt(x) == x`
* `null` si l’argument est négatif ou s’il ne peut pas être converti en une valeur `real`. 

### <a name="toint"></a>toint
    toint(100)        // cast from long
    toint(20.7) == 20 // nearest int below double
    toint(20.4) == 20 // nearest int below double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble
    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic


### <a name="tolong"></a>tolong
    tolong(20.7) == 20 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic

## <a name="numeric-series"></a>Série numérique

[series_fir](#seriesfir) | [series\_fit\_line](#seriesfitline) | [series\_fit\_2lines](#seriesfit2lines) | [series_iir](#seriesiir) |  [series_periods](#seriesperiods) | [series_stats](#seriesstats)  

### <a name="seriesfir"></a>series_fir
La fonction series_fir() applique un filtre [Finite Impulse Response](https://wikipedia.org/wiki/Finite_impulse_response) (filtre à réponse impulsionnelle finie) sur une série (qui est représentée par une colonne dynamique contenant le tableau numérique).

En spécifiant les coefficients de filtre, il peut être utilisé pour le calcul de moyenne mobile, le lissage, la détection de modifications et de nombreux autres cas d’utilisation.

La fonction accepte comme entrée la colonne qui contient le tableau dynamique et un tableau statique et dynamique des coefficients du filtre, et applique le filtre sur la colonne. Il génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée. 

**Syntaxe**

`series_fir(x, filter [, normalize[, center]])`

**Arguments**

* *x :* cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs [make-series](#make-series-operator) ou [makelist](#makelist-operator).
* *filter :* une valeur booléenne facultative indiquant si les coefficients de filtre doivent être normalisés (c.-à-d. divisés par la somme). Par défaut, normalize a la valeur true. Si le filtre contient des valeurs négatives, la normalisation automatique ne peut pas être effectuée, et normalize doit être explicitement défini sur false.
* *normalize :* valeur booléenne facultative indiquant si le filtre doit être normalisé. Par défaut, normalize a la valeur true. Si le filtre contient des valeurs négatives, normalize doit être spécifié comme false. 
* *center :* une valeur booléenne facultative indiquant si le filtre est appliqué symétriquement sur un intervalle de temps avant et après le point actuel, ou sur une fenêtre de temps à partir du point actif vers l’arrière. Par défaut, center est défini sur false, ce qui correspond au scénario de diffusion en continu de données, où nous pouvons uniquement appliquer le filtre sur les points actuels et anciens. Toutefois, pour un traitement ad hoc, vous pouvez le définir sur true, et maintenir la synchronisation avec la série chronologique (voir les exemples ci-dessous). Techniquement parlant, ce paramètre contrôle le retard de groupe du filtre.

**Exemples**

Le calcul d’une moyenne mobile de 5 points peut être effectué en définissant les valeurs comme suit : filter=[1,1,1,1,1] et normalize=true (valeur par défaut). Notez l’effet de center=false (valeur défaut) par rapport à la valeur true :

```AIQL
range t from bin(now(), 1h)-23h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS', val=dynamic([0,0,0,0,0,0,0,0,0,10,20,40,100,40,20,10,0,0,0,0,0,0,0,0]), t
| extend 5h_MovingAvg=series_fir(val, dynamic([1,1,1,1,1])),
         5h_MovingAvg_centered=series_fir(val, dynamic([1,1,1,1,1]), true, true)
| render timechart
```

Cette requête renvoie :

* *5h_MovingAvg :* filtre de moyenne mobile de 5 points. Le pic est lissé et son point culminant est déplacé de (5-1)/2 = 2 h.
* *5h_MovingAvg_centered :* identique mais avec le paramètre center=true, le point culminant reste à son emplacement d’origine.

![Résultats de la requête](./media/app-insights-analytics-reference/series-fir-1.png) (Pour afficher plusieurs lignes, décochez l’option « Fractionner » dans les contrôles du graphique.)

Le calcul de la différence entre un point et le point précédent peut être effectué en définissant filter=[1,-1 ]:

```AIQL
range t from bin(now(), 1h)-11h to bin(now(), 1h) step 1h
| summarize t=makelist(t)
| project id='TS',t,value=dynamic([0,0,0,0,2,2,2,2,3,3,3,3])
| extend diff=series_fir(value, dynamic([1,-1]), false, false)
| render timechart
```


![Résultats de la requête](./media/app-insights-analytics-reference/series-fir-2.png)


### <a name="seriesfitline"></a>series\_fit\_line
La fonction series_fit_line() prend une colonne contenant un tableau numérique dynamique en tant qu’entrée, et effectue la régression linéaire afin de trouver la ligne qui lui convient mieux. Cette fonction doit être utilisée sur des tableaux de séries chronologiques, pour correspondre à la sortie de l’opérateur make-series. Il génère une colonne dynamique contenant les champs suivants :

* *slope :* pente de la ligne approximative (il s’agit de `a` dans `y=ax+b`).
* *interception :* interception de la ligne approximative (il s’agit de `b` dans `y=ax+b`).
* *rsquare :* r-square est une mesure standard de qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne.
* *variance :* variance des données d’entrée.
* *rvariance :* variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives.
* *line_fit :* tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.

La méthode la plus pratique pour utiliser cette fonction est son application aux résultats de l’opérateur make-series.

**Syntaxe**
    
    series_fit_line(x)

**Arguments**

* `x:` Tableau dynamique de valeurs numériques. Notez que la fonction attend que toutes les lignes aient le même nombre d’éléments de tableau. Autrement, des résultats vides seront renvoyés. 

**Exemples**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([2,5,6,8,11,15,17,18,25,26,30,30])
| extend (s,i,rs,v,rv,LineFit)=series_fit_line(y)
| render timechart 
```

![Résultats de la requête](./media/app-insights-analytics-reference/series-fit-line.png)

|Slope|Interception|RSquare|Variance|RVariance|LineFit|
|---|---|---|---|---|---|
|0.982|2.730|98.628|1.686|-1.666| 1.064, 3.7945, 6.526, 9.256, 11.987, 14.718, 17.449, 20.180, 22.910, 25.641, 28.371, |

### <a name="seriesfit2lines"></a>series\_fit\_2lines

La fonction series_fit_2lines() s’applique à la régression linéaire de deux segments d’une série (chronologique) afin d’identifier et de quantifier la modification de tendance dans une série. La fonction effectue une itération sur les index de la série. Dans chaque itération, elle fractionne la série 2 parties, correspond à une ligne distincte (à l’aide de series_fit_line()) de chaque partie et calcule la valeur r-square totale. La meilleure séparation est celle qui optimise la valeur r-square ; la fonction renvoie ses paramètres :

* *rsquare :* r-square est une mesure standard de qualité adaptée. Il s’agit d’un nombre dans la plage [0-1], où 1 est la meilleure correspondance possible, et 0 signifie que les données sont totalement désordonnées et ne correspondent à aucune ligne
* *split_idx :* l’index du point de rupture de 2 segments (basés sur zéro)
* *variance :* variance des données d’entrée
* *rvariance :* variance résiduelle qui est l’écart entre les valeurs des données d’entrée et les valeurs approximatives (selon les 2 segments linéaires).
* *line_fit :* tableau numérique contenant une série de valeurs de la ligne la mieux adaptée. La longueur de la série est égale à la longueur du tableau d’entrée. Elle est principalement utilisée pour les graphiques.
* *right_rsquare :* r-square de la ligne située à droite du fractionnement, voir series_fit_line()
* *right_slope :* pente de la ligne droite approximative (il s’agit de a dans y=ax+b)
* *right_interception :* interception de la ligne droite approximative (il s’agit de b dans y=ax+b)
* *right_variance :* variance des données d’entrée sur le côté droit du fractionnement
* *right_rvariance :* variance résiduelle des données d’entrée sur le côté droit du fractionnement
* *left_rsquare :* r-square de la ligne située à gauche du fractionnement, voir series_fit_line()
* *left_slope :* pente de la ligne gauche approximative (il s’agit de a dans y=ax+b)
* *left_interception :* interception de la ligne gauche approximative (il s’agit de b dans y=ax+b)
* *left_variance :* variance des données d’entrée sur le côté gauche du fractionnement
* *left_rvariance :* variance résiduelle des données d’entrée sur le côté gauche du fractionnement

Notez que cette fonction renvoie plusieurs colonnes, elle ne peut donc pas être utilisé en tant qu’argument pour une autre fonction.

**Syntaxe**

    project series_fit_2lines(x)

Renverra toutes les colonnes mentionnées avec les noms suivants :`series_fit_2lines_x_rsquare, series_fit_2lines_x_split_idx and etc.`

    project (rs, si, v)=series_fit_2lines(x)

Renvoie les colonnes suivantes : `rs (r-square), si (split index), v (variance)` et le reste ressemblera à : `series_fit_2lines_x_rvariance, series_fit_2lines_x_line_fit`, etc.

    extend (rs, si, v)=series_fit_2lines(x)

Renvoie uniquement : rs (r-square), si (split index) et v (variance).

**Arguments**

* *x :* tableau dynamique de valeurs numériques. 

La méthode la plus pratique pour utiliser cette fonction est son application aux résultats de l’opérateur make-series.

**Exemples**

```AIQL
range x from 1 to 1 step 1
| project id=' ', x=range(bin(now(), 1h)-11h, bin(now(), 1h), 1h), y=dynamic([1,2.2, 2.5, 4.7, 5.0, 12, 10.3, 10.3, 9, 8.3, 6.2])
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(y), (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(y)
| project id, x, y, LineFit, LineFit2
| render timechart
```


![Résultats de la requête](./media/app-insights-analytics-reference/series-fit-2lines.png)

### <a name="seriesiir"></a>series_iir

La fonction series_iir() applique un filtre Infinite Impulse Response (filtre à réponse impulsionnelle infinie) sur une série (qui est représentée par une colonne dynamique contenant le tableau numérique).

Si les coefficients de filtre sont spécifiés, il peut être utilisé par exemple pour calculer la somme cumulée de la série, pour appliquer des opérations de lissage, ainsi que diverses filtres passe-haut, passe-bande et passe-bas.

La fonction accepte comme entrée la colonne qui contient le tableau dynamique et deux tableaux statiques et dynamiques des coefficients a et b du filtre, et applique le filtre sur la colonne. Il génère une nouvelle colonne de tableau dynamique, qui contient la sortie filtrée. 

**Syntaxe**

    series_iir(x, b , a)

**Arguments**


* *x :* cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs make-series ou makelist.
* *b :* une expression constante qui contient les coefficients numérateurs du filtre (stockés sous forme de tableau dynamique des valeurs numériques).
* *a :* une expression constante, comme b. Contient les coefficients dénominateurs du filtre.

    Le premier élément de a (c’est-à-dire a[0]) ne doit pas être égal à zéro (pour éviter une division par 0 ; voir la formule ci-dessous).

**En savoir plus sur la formule récursive du filtre**

Soit un tableau d’entrée X et des tableaux de coefficients a, b de longueurs `n_a` et `n_b` respectivement, la fonction de transfert du filtre, générant le tableau de sortie Y, est définie par (voir également Wikipédia) : 

    Y[i] = 1/a[0] * ( b[0]*X[i] + b[1]*X[i-1] + … 
                 + b[n_b-1]*X[i-n_b-1] — a[1]*Y[i-1] – a[2]*Y[i-2] – …
                 – a[n_a-1]*Y[i-n_a-1] )


**Exemples**

Le calcul de la somme cumulée peut être effectué par le filtre iir avec des coefficients a=[1,-1] et b=[1] : 

```AIQL
let x = range(1.0, 10, 1);
range t from 1 to 1 step 1
| project x=x, y = series_iir(x, dynamic([1]), dynamic([1,-1]))
| mvexpand x, y
```

|x|y|
|---|---|
|1.0|1.0|
|2.0|3.0|
|3.0|6.0|
|4.0|10.0|


### <a name="seriesperiods"></a>series_periods

La fonction series_periods() recherche les périodes les plus significatives qui existent dans une série chronologique.

Par exemple, très souvent une mesure déterminant le trafic d’une application se caractérise par deux cycles significatifs : un hebdomadaire et un quotidien. Étant donné cette série chronologique, series_periods() doit détecter ces 2 périodes dominantes.

La fonction accepte en entrée une colonne contenant un tableau dynamique de série chronologique (généralement, le résultat d’un opérateur make-series), deux nombres réels définissant l’intervalle minimum et maximum (par exemple, le nombre d’emplacements, par exemple, pour un emplacement de 1 h la taille d’une période quotidienne serait 24) à rechercher, et un nombre long définissant le nombre total de périodes à rechercher par la fonction. La sortie est un tableau dynamique qui contient la taille des périodes qui ont été trouvées, classées par importance des périodes dans les données.

**Syntaxe**

    series_periods(x, min_period, max_period, num_periods)`

**Arguments**

* *x :* cellule de tableau dynamique qui est un tableau de valeurs numériques, généralement le résultat des opérateurs make-series ou makelist.
* *min_period :* un nombre réel spécifiant la période minimale à rechercher.
* *max_period :* un nombre réel spécifiant la période maximale à rechercher.
* *num_periods :* un nombre long spécifiant le nombre de périodes maximal requis. Il s’agit de la longueur du tableau dynamique de sortie.

**Remarques importantes**

* L’algorithme derrière series\_periods() requiert au moins 4 points dans une période pour la détection. Par conséquent, la valeur minimale de min_period est 4. Si min_period est défini sur une valeur inférieure, la fonction remplace cette valeur par 4.
* La valeur maximale de max_period est la moitié de la longueur de la série d’entrée. Si max_period est défini sur une valeur supérieure, la fonction l’ajuste à cette valeur.
* Comme mentionné ci-dessus, les périodes qui en résultent sont dans des unités de placement, par exemple, si la série d’origine a une période quotidienne et a été agrégée par emplacements horaires, une période quotidienne de la sortie sera 24. Si les données sont agrégées par minute, la sortie sera de 60 × 24 = 1 440.
* Vous devez définir min\_period un peu ci-dessous et max\_period un peu au-dessus des périodes que vous vous attendez à trouver dans la série chronologique. Par exemple, si vous disposez d’un signal agrégé horaire, et que vous recherchez des périodes quotidiennes et hebdomadaires (qui seraient 24 et 168 respectivement), vous pouvez définir min\_period=0,824, *max\_period=1,2*168.
* La longueur du tableau dynamique de sortie est num\_of\_periods ; si la fonction a trouvé moins de num\_of\_periods périodes significatives, le reste des entrées du tableau sera défini sur 0.
* La série chronologique d’entrée doit être régulière, c’est-à-dire agrégé en emplacements constants (ce qui est toujours le cas si elle a été créée à l’aide de make-series). Dans le cas contraire, le résultat n’est pas significatif.

**Exemple**

Par exemple, la requête suivante incorpore un instantané d’un mois du trafic d’une application, agrégé deux fois par jour (c.-à-d., toutes les 12 heures).

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| render linechart
```

![Résultats de la requête](./media/app-insights-analytics-reference/series-periods1.png)

L’exécution de series_periods() sur cette série dans la période hebdomadaire (longue de 14 points) :

```AIQL
range x from 1 to 1 step 1
| project y=dynamic([80,139,87,110,68,54,50,51,53,133,86,141,97,156,94,149,95,140,77,61,50,54,47,133,72,152,94,148,105,162,101,160,87,63,53,55,54,151,103,189,108,183,113,175,113,178,90,71,62,62,65,165,109,181,115,182,121,178,114,170])
| project x=range(1, arraylength(y), 1), y  
| project periods = series_periods(y, 4.0, 50.0, 2)
```

|periods|
|---|
|[14.0, 0.0]|

### <a name="seriesstats"></a>series_stats

La fonction series_stats() prend une colonne contenant un tableau numérique dynamique comme entrée, et calcule les colonnes suivantes :

* *min :* valeur minimale dans le tableau d’entrée
* *min_idx :* valeur maximale dans le tableau d’entrée
* *max :* valeur maximale dans le tableau d’entrée
* *max_idx :* valeur maximale dans le tableau d’entrée
* *moyenne :* valeur moyenne du tableau d’entrée
* *variance :* exemple de variance du tableau d’entrée
* *stdev :* exemple d’écart standard du tableau d’entrée

Notez que cette fonction renvoie plusieurs colonnes, elle ne peut donc pas être utilisé en tant qu’argument pour une autre fonction.

**Syntaxe**

    project series_stats(x)

Renvoie toutes les colonnes mentionnées ci-dessus portant les noms suivants : serie\_stats\_x\_min, series\_stats\_x\_min\_idx, etc.

    project (m, mi)=series_stats(x)

Renvoie les colonnes suivantes : `m (min), mi (min_idx)` et le reste ressemblera à `series_stats_x_max, series_stats_x_line_max_idx`, etc.

    extend (m, mi)=series_stats(x)

Renvoie uniquement : m (min) et mi (min_idx).

**Arguments**

* *x :* cellule de tableau dynamique qui est un tableau de valeurs numériques. 

**Exemples**

Pour l’entrée suivante :

` [1,6,11,16,21,26,31,36,41,46,51,56,61,66,71,76,81,86,91,96]`

series_stats() renvoie :

|min|min\_idx|max|max\_idx|average|variance|stdev|
|---|---|---|---|---|---|---|
|1.0|1|96.0|19|48.5|29.58039891549808|875.0|


## <a name="date-and-time"></a>Date et heure
[ago](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [now](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

Une valeur timespan représente un intervalle de temps, comme 3 heures ou 1 an.

Une valeur datetime représente une date/heure de calendrier/horloge au format UTC.

Il n’existe pas de type distinct « date ». Pour supprimer l’heure d’une valeur datetime, utilisez une expression comme `bin(timestamp, 1d)`.

### <a name="date-and-time-literals"></a>Littéraux de date et d’heure
|  |  |
| --- | --- |
| **datetime** | |
| `datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")` |Les heures sont toujours exprimées en UTC. L’omission de la date entraîne la sélection de la date du jour. |
| `now()` |L’heure actuelle. |
| `now(`-*timespan*`)` |`now()-`*timespan* |
| `ago(`*timespan*`)` |`now()-`*timespan* |
| **timespan** | |
| `2d` |2 jours |
| `1.5h` |1,5 heure |
| `30m` |30 minutes |
| `10s` |10 secondes |
| `0.1s` |0,1 seconde |
| `100ms` |100 millisecondes |
| `10microsecond` | |
| `1tick` |100ns |
| `time("15 seconds")` | |
| `time("2")` |2 jours |
| `time("0.12:34:56.7")` |`0d+12h+34m+56.7s` |

### <a name="date-and-time-expressions"></a>Expressions de date et d’heure
| Expression | Résultat |Résultat|
| --- | --- |---|
| `datetime("2015-01-02") - datetime("2015-01-01")` |`1d` | Différence de temps|
| `datetime("2015-01-01") + 1d` |`datetime("2015-01-02")` | Ajouter des jours |
| `datetime("2015-01-01") - 1d` |`datetime("2014-12-31")` | Soustraire des jours|
| `2h * 24` |`2d` |Multiples d’intervalle de temps|
| `2d` / `2h` |`24` |Division de l’intervalle de temps|
| `datetime("2015-04-15T22:33") % 1d` |`timespan("22:33")` |Heure à partir d’une valeur datetime|
| `bin(datetime("2015-04-15T22:33"), 1d)` |`datetime("2015-04-15T00:00")` |Date à partir d’une valeur datetime|
|  | ||
| `<` ||Inférieur à |
| `<=` ||Inférieur ou égal à |
| `>` ||Supérieur à |
| `>=` ||Supérieur ou égal à |
| `<>` ||Non égal à |
| `!=` ||Non égal à |

### <a name="ago"></a>ago
Soustrait l’intervalle de temps donné de l’heure UTC actuelle. Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure UTC référencée est la même pour toutes les instanciations.

**Syntaxe**

    ago(a_timespan)

**Arguments**

* *a_timespan* : intervalle à soustraire de l’heure UTC actuelle (`now()`).

**Retourne**

    now() - a_timespan

**Exemple**

Toutes les lignes de l’horodatage de la dernière heure :

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>datepart
    datepart("Day", datetime(2015-12-14)) == 14

Extrait une partie spécifiée d’une date sous forme d’entier.

**Syntaxe**

    datepart(part, datetime)

**Arguments**

* `part:String` - {"Year", "Month", "Day", "Hour", "Minute", "Second", "Millisecond", "Microsecond", "Nanosecond"}
* `datetime`

**Retourne**

Valeur longue représentant la partie spécifiée.

### <a name="dayofmonth"></a>dayofmonth
    dayofmonth(datetime("2016-05-15")) == 15 

Numéro ordinal du jour du mois.

**Syntaxe**

    dayofmonth(a_date)

**Arguments**

* `a_date` : une `datetime`.

### <a name="dayofweek"></a>dayofweek
    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Nombre entier de jours écoulés depuis le dimanche précédent, en tant que `timespan`.

**Syntaxe**

    dayofweek(a_date)

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`timespan` depuis minuit au début du dimanche précédent, arrondi au nombre entier inférieur de jours.

**Exemples**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>dayofyear
    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

Numéro ordinal du jour de l’année.

**Syntaxe**

    dayofyear(a_date)

**Arguments**

* `a_date` : une `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>

### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear
    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getmonth
Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.

**Exemple**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear
Obtient l’année à partir d’une valeur datetime.

**Exemple**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>now
    now()
    now(-2d)

L’heure UTC actuelle, avec un décalage éventuel selon un intervalle de temps donné. Cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure référencée est la même pour toutes les instances.

**Syntaxe**

    now([offset])

**Arguments**

* *offset :* `timespan` ajouté à l’heure UTC actuelle. Valeur par défaut : 0.

**Retourne**

Heure UTC actuelle, en tant que `datetime`.

    now() + offset

**Exemple**

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>

### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear
    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>todatetime
Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Vérifier si une chaîne est une date valide :

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>totimespan
Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear
    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

Le résultat entier représente le numéro de semaine conformément à la norme ISO 8601. Le premier jour de la semaine est le dimanche et la première semaine de l’année est la semaine qui contient le premier jeudi de cette année. (Les derniers jours d’une année peuvent donc contenir certains jours de la semaine 1 de l’année suivante, ou les premiers jours peuvent contenir certains jours de la semaine 52 ou 53 de l’année précédente.)

## <a name="string"></a>String
[countof](#countof) | [extract](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)

### <a name="string-literals"></a>Littéraux de chaîne
Les règles sont les mêmes que dans JavaScript.

Les chaînes peuvent être placées entre guillemets simples ou doubles. 

La barre oblique inverse (`\`) est utilisée pour les caractères d’échappement tels que `\t` (tabulation), `\n` (renvoi à la ligne) et les instances de guillemets.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Littéraux de chaîne masqués
Les littéraux de chaîne masqués sont des chaînes qu’Analytics masque lors de la sortie de la chaîne (par exemple, lors d’un suivi). Le processus de masquage remplace tous les caractères masqués par un caractère de début (`*`).

Pour créer un littéral de chaîne masqué, ajoutez `h` ou « H ». Par exemple :

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparaisons de chaînes
| opérateur | Description | Respecte la casse | Exemple vrai |
| --- | --- | --- | --- |
| `==` |Égal à |Oui |`"aBc" == "aBc"` |
| `<>` `!=` |Non égal à |Oui |`"abc" <> "ABC"` |
| `=~` |Égal à |Non |`"abc" =~ "ABC"` <br/>`boolAsString =~ "true"` |
| `!~` |Non égal à |Non |`"aBc" !~ "xyz"` |
| `has` |Le terme de droite est un terme entier dans le terme de gauche |Non |`"North America" has "america"` |
| `!has` |Le terme de droite n’est pas un terme entier dans le terme de gauche |Non |`"North America" !has "amer"` |
| `hasprefix` |RHS est un préfixe d’un terme dans l’attribut LHS |Non |`"North America" hasprefix "ame"` |
| `!hasprefix` |Le terme de droite n’est pas un préfixe d’un terme de gauche |Non |`"North America" !hasprefix "mer"` |
| `hassuffix` |RHS est un suffixe d’un terme dans l’attribut LHS |Non |`"North America" hassuffix "rth"` |
| `!hassuffix` |Le terme de droite n’est pas un suffixe d’un terme de gauche |Non |`"North America" !hassuffix "mer"` |
| `contains` |Le terme de droite est une sous-chaîne du terme de gauche |Non |`"FabriKam" contains "BRik"` |
| `!contains` |Le terme de droite n’est pas une sous-séquence du terme de gauche |Non |`"Fabrikam" !contains "xyz"` |
| `containscs` |Le terme de droite est une sous-chaîne du terme de gauche |Oui |`"FabriKam" contains "Kam"` |
| `!containscs` |Le terme de droite n’est pas une sous-séquence du terme de gauche |Oui |`"Fabrikam" !contains "Kam"` |
| `startswith` |Le terme de droite est une sous-chaîne initiale du terme de gauche. |Non |`"Fabrikam" startswith "fab"` |
| `!startswith` |Le terme de droite n’est pas une sous-chaîne initiale du terme de gauche. |Non |`"Fabrikam" !startswith "abr"` |
| `endswith` |Le terme de droite est une sous-chaîne terminale du terme de gauche. |Non |`"Fabrikam" endswith "kam"` |
| `!endswith` |Le terme de droite n’est pas une sous-chaîne terminale du terme de gauche. |Non |`"Fabrikam" !endswith "ka"` |
| `matches regex` |Le terme de gauche contient une correspondance du terme de droite |Oui |`"Fabrikam" matches regex "b.*k"` |
| [`in`](#in) |Est égal à un des éléments |Oui |`"abc" in ("123", "345", "abc")` |
| [`!in`](#in) |N’est égal à aucun des éléments |Oui |`"bc" !in ("123", "345", "abc")` |

Utilisez `has` ou `in` si vous testez la présence d’un terme lexical complet, c’est-à-dire d’un symbole ou d’un mot alphanumérique délimité par des caractères non alphanumériques ou le début ou la fin d’un champ. `has` effectue la recherche plus rapidement que `contains`, `startswith` ou `endswith`. La première de ces requêtes s’exécute plus rapidement :

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof
    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

**Syntaxe**

    countof(text, search [, kind])

**Arguments**

* *text :* chaîne.
* *search :* chaîne simple ou expression régulière à faire correspondre au *text*.
* *kind:* `"normal"|"regex"` (valeur par défaut : `normal`). 

**Retourne**

Le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

**Exemples**

|  |  |
| --- | --- |
| `countof("aaa", "a")` |3 |
| `countof("aaaa", "aa")` |3 (pas 2 !) |
| `countof("ababa", "ab", "normal")` |2 |
| `countof("ababa", "aba")` |2 |
| `countof("ababa", "aba", "regex")` |1 |
| `countof("abcabc", "a.c", "regex")` |2 |

### <a name="extract"></a>extract
    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtient une correspondance pour une [expression régulière](#regular-expressions) à partir d’une chaîne de texte. Éventuellement, il convertit ensuite la sous-chaîne extraite dans le type indiqué.

**Syntaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Arguments**

* *regex :*[expression régulière](#regular-expressions).
* *captureGroup :* constante `int` positive identifiant le groupe de capture à extraire. Les valeurs sont 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
* *text :*  `string` à rechercher.
* *typeLiteral :* littéral de type facultatif (par exemple, `typeof(long)`). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type. 

**Retourne**

Si *regex* trouve une correspondance dans *text* : sous-chaîne correspondant au *captureGroup* indiqué, éventuellement convertie en *typeLiteral*.

Si aucune correspondance n’est trouvée ou si la conversion de type échoue : `null`. 

**Exemples**

Une définition de `Duration` est recherchée dans l’exemple de chaîne `Trace`. La correspondance est convertie en `real`, puis multipliée par une constante de temps (`1s`) pour que `Duration` soit de type `timespan`. Dans cet exemple, elle est égale à 123,45 secondes :

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Cet exemple est équivalent à `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>



### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty
    isempty("") == true

True si l’argument est une chaîne vide ou s’il a la valeur null.
Voir aussi [isnull](#isnull).

**Syntaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Retourne**

Indique si l’argument est une chaîne vide ou s’il a la valeur isnull.

| x | isempty(x) |
| --- | --- |
| "" |true |
| "x" |false |
| parsejson("") |true |
| parsejson("[]") |false |
| parsejson("{}") |false |

**Exemple**

    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl
Fractionnez une URL en plusieurs parties.

**Syntaxe**

    parseurl(urlstring)

**Arguments**

* *urlstring :* URL.

**Retourne**

Objet qui contient les parties sous forme de chaînes.

**Exemple**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>replace
Remplace toutes les correspondances d’expression régulière par une autre chaîne.

**Syntaxe**

    replace(regex, rewrite, text)

**Arguments**

* *regex :* [expression régulière](https://github.com/google/re2/wiki/Syntax) utilisée pour rechercher *text*. Elle peut contenir des groupes de capture entre '('parenthèses')'. 
* *rewrite :* expression régulière de remplacement pour toute correspondance trouvée par *matchingRegex*. Utilisez `\0` pour faire référence à la correspondance complète, `\1` pour le premier groupe de capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *text :* chaîne.

**Retourne**

*text* après le remplacement de toutes les correspondances de *regex* par les évaluations de *rewrite*. Les correspondances ne se chevauchent pas.

**Exemple**

L’instruction suivante :

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Donne les résultats suivants :

| x | str | replaced |
| --- | --- | --- |
| 1 |Le nombre est 1.000000 |Le nombre était : 1.000000 |
| 2 |Le nombre est 2.000000 |Le nombre était : 2.000000 |
| 3 |Le nombre est 3.000000 |Le nombre était : 3.000000 |
| 4 |Le nombre est 4.000000 |Le nombre était : 4.000000 |
| 5 |Le nombre est 5.000000 |Le nombre était : 5.000000 |

### <a name="split"></a>split
    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Fractionne une chaîne donnée en fonction d’un délimiteur donné et retourne un tableau de chaînes avec les sous-chaînes contenues. Éventuellement, une sous-chaîne spécifique peut être retournée si elle existe.

**Syntaxe**

    split(source, delimiter [, requestedIndex])

**Arguments**

* *source*: chaîne source à fractionner en fonction du délimiteur donné.
* *delimiter*: délimiteur utilisé pour fractionner la chaîne source.
* *requestedIndex* : index de base zéro facultatif `int`. S’il est fourni, le tableau de chaînes retourné contient la sous-chaîne demandée si elle existe. 

**Retourne**

Un tableau de chaînes qui contient les sous-chaînes de la chaîne source donnée séparées par le délimiteur donné.

**Exemples**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat
    strcat("hello", " ", "world")

Concatène entre 1 et 16 arguments, qui doivent être des chaînes.

### <a name="strlen"></a>strlen
    strlen("hello") == 5

Longueur d’une chaîne.

### <a name="substring"></a>substring
    substring("abcdefg", 1, 2) == "bc"

Extrait une sous-chaîne d’une chaîne source donnée à partir d’un index donné. Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

**Syntaxe**

    substring(source, startingIndex [, length])

**Arguments**

* *source :* chaîne source dont la sous-chaîne est extraite.
* *startingIndex :* position du caractère de départ (base zéro) de la sous-chaîne demandée.
* *length :* paramètre facultatif qui permet de spécifier le nombre de caractères demandés dans la sous-chaîne. 

**Retourne**

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

**Exemples**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower
    tolower("HELLO") == "hello"

Convertit une chaîne en minuscules.

### <a name="toupper"></a>toupper
    toupper("hello") == "HELLO"

Convertit une chaîne en majuscules.

### <a name="guids"></a>GUID
    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Tableaux, objets et dynamique
[littéraux](#dynamic-literals) | [conversion](#casting-dynamic-objects) | [opérateurs](#operators) | [clauses let](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [zip](#zip)

Voici le résultat d’une requête sur une exception d’Application Insights. La valeur de `details` est un tableau.

![](./media/app-insights-analytics-reference/310.png)

**Indexing :** tableaux d’index et objets comme en JavaScript :

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mais utilisez `arraylength` et d’autres fonctions Analytics (pas « .length » !)

**Conversion** Parfois, il est nécessaire de convertir un élément que vous extrayez d’un objet, car son type pourrait varier. Par exemple, `summarize...to` a besoin d’un type spécifique :

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions
    | summarize count()
      by tostring(details[0].parsedStack[0].assembly)

**Littéraux** Pour créer un tableau explicite ou un objet conteneur de propriétés, écrivez-le en tant que chaîne JSON et effectuez la conversion :

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand :** pour décomposer les propriétés d’un objet en plusieurs lignes, utilisez mvexpand :

    exceptions | take 1
    | mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)

**treepath :** pour rechercher tous les chemins dans un objet complexe :

    exceptions | take 1 | project timestamp, details
    | extend path = treepath(details)
    | mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema :** pour trouver le schéma minimal qui admet toutes les valeurs de l’expression dans la table :

    exceptions | summarize buildschema(details)

Résultat :

    { "indexer":
     {"id":"string",
       "parsedStack":
       { "indexer":
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

`indexer` indique où vous devez utiliser un index numérique. Pour ce schéma, voici certains chemins valides (en supposant que ces exemples d’index appartiennent à la plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Littéraux de tableau et d’objet
Pour créer un littéral dynamique, utilisez `parsejson` (alias `todynamic`) avec un argument de chaîne JSON :

* `parsejson('[43, 21, 65]')` : tableau de nombres
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`
* `parsejson('21')` : valeur unique de type dynamique qui contient un nombre
* `parsejson('"21"')` : valeur unique de type dynamique qui contient une chaîne

> ![REMARQUE] Vous devez utiliser des guillemets doubles (`"`) pour encadrer les valeurs d’étiquettes et de chaînes dans JSON. Ainsi, il est généralement plus facile de placer les littéraux de chaîne en langage JSON entre des apostrophes (`'`).
> 

Cet exemple crée une valeur dynamique, puis utilise ses champs :

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Fonctions de l’objet dynamique
|  |  |
| --- | --- |
| [*value* `in` *array*](#in) |*array* contains *value* |
| [*value* `!in` *array*](#in) |*array* does not contain *value* |
| [`arraylength(`array`)`](#arraylength) |Null si ce n’est pas un tableau |
| [`extractjson(`path,object`)`](#extractjson) |Utilise le chemin pour accéder à l’objet. |
| [`parsejson(`source`)`](#parsejson) |Convertit une chaîne JSON en un objet dynamique. |
| [`range(`from,to,step`)`](#range) |Tableau de valeurs |
| [`mvexpand` listColumn](#mvexpand-operator) |Réplique une ligne pour chaque valeur d’une liste dans une cellule spécifiée. |
| [`summarize buildschema(`column`)`](#buildschema) |Déduit le schéma du type à partir du contenu de la colonne |
| [`summarize makelist(`column`)` ](#makelist) |Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau. |
| [`summarize makeset(`column`)`](#makeset) |Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication. |

### <a name="dynamic-objects-in-let-clauses"></a>Objets dynamiques dans les clauses let
Les [clauses let](#let-clause) stockent des valeurs dynamiques sous forme de chaînes, pour que ces deux clauses soient équivalentes. De plus, toutes deux doivent avoir `parsejson` (ou `todynamic`) pour être utilisées :

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a


### <a name="in"></a>commencer
    value in (listExpression)
    value !in (listExpression)

Détermine s’il existe (ou non) un élément dans la liste qui est égal à la valeur. Sensible à la casse, où la valeur est une chaîne.

**Arguments**

* `value` : est une expression scalaire.
* `listExpression`... : Une liste d’expressions scalaires, ou une expression qui correspond à une liste. 

Un tableau imbriqué est aplani en une seule liste - par exemple, `where x in (dynamic([1,[2,3]]))` devient `where x in (1,2,3)`.  

**Exemples**

```AIQL
    requests | where client_City in ("London", "Paris", "Rome")
```

```AIQL
let cities = dynamic(['Dublin','Redmond','Amsterdam']);
requests | where client_City in (cities) 
|  summarize count() by client_City
```

Liste calculée :

```AIQL
let topCities =  toscalar ( // convert single column to value
   requests
   | summarize count() by client_City 
   | top 4 by count_ 
   | summarize makeset(client_City)) ;
requests
| where client_City in (topCities) 
| summarize count() by client_City;
```

Utilisation d’un appel de fonction en tant qu’expression de liste :

```AIQL
let topCities =  (n:int) {toscalar (
   requests
   | summarize count() by client_City 
   | top n by count_ 
   | summarize makeset(client_City)) };
requests
| where client_City in (topCities(3)) 
| summarize count() by client_City;
```
 

### <a name="arraylength"></a>arraylength
Nombre d’éléments dans un tableau dynamique.

**Syntaxe**

    arraylength(array)

**Arguments**

* *array :* valeur `dynamic`.

**Retourne**

Nombre d’éléments dans *array*, ou `null` si *array* n’est pas un tableau.

**Exemples**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson
    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin. Convertit éventuellement la chaîne extraite un type spécifique.

**Syntaxe**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Retourne**

Cette fonction effectue une requête JsonPath dans dataSource qui contient une chaîne JSON valide, convertissant éventuellement cette valeur en un autre type selon le troisième argument.

**Exemple**

La notation entre [crochets] et la notation sous forme de points sont équivalentes :

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Conseils sur les performances**

* Appliquez les clauses where avant d’utiliser `extractjson()`
* Utilisez plutôt une correspondance d’expression régulière avec [extract](#extract) . L’exécution peut être beaucoup plus rapide, et elle est efficace si le JSON est généré à partir d’un modèle.
* Utilisez `parsejson()` si vous devez extraire plusieurs valeurs de JSON.
* Envisagez d’analyser le JSON lors de l’ingestion en déclarant le type de la colonne comme étant dynamique.

### <a name="json-path-expressions"></a>Expressions de chemin JSON
|  |  |
| --- | --- |
| `$` |Objet racine |
| `@` |Objet en cours |
| `[0]` |Indice de tableau |
| `.` ou `[0]` |Enfant |

*(Actuellement, nous n’implémentons pas les caractères génériques, la récursivité, l’union ou les tranches.)*

### <a name="parsejson"></a>parsejson
Interprète une `string` comme une [valeur JSON](http://json.org/) et retourne la valeur en tant que `dynamic`. Son efficacité est supérieure à celle de `extractjson()` si vous devez extraire plusieurs éléments d’un objet composite JSON.

**Syntaxe**

    parsejson(json)

**Arguments**

* *json :* document JSON.

**Retourne**

Un objet de type `dynamic` spécifié par *json*.

**Exemple**

Dans l’exemple suivant, `customDimensions.person` est un `string` qui ressemble à ceci : 

```
"\"addresses\":[{\"postcode\":\"C789\",\"street\":\"high st\",\"town\":\"Cardigan\"},{\"postcode\":\"J456\",\"street\":\"low st\",\"town\":\"Jumper\"}],\"name\":\"Ada\""
```

Le fragment suivant récupère la valeur de l’emplacement `duration` dans l’objet et, grâce à cette valeur, récupère deux emplacements, `duration.value` et  `duration.min` (`118.0` et `110.0`, respectivement).

```AIQL
customEvents
| where name == "newMember"
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

> ![REMARQUE] Vous devez utiliser des guillemets doubles pour encadrer les valeurs d’étiquettes et de chaînes dans JSON. 
>


### <a name="range"></a>range
La fonction `range()` (à ne pas confondre avec l’opérateur `range`) génère un tableau dynamique contenant une série de valeurs espacées de façon égale.

**Syntaxe**

    range(start, stop, step)

**Arguments**

* *start :* valeur du premier élément dans le tableau obtenu. 
* *stop :* valeur du dernier élément dans le tableau obtenu ou valeur minimale supérieure au dernier élément du tableau obtenu et correspondant à un multiple entier de *step* à partir de *start*.
* *step :* différence entre deux éléments consécutifs du tableau.

**Exemples**

L’exemple suivant retourne `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

L’exemple suivant retourne un tableau contenant tous les jours de l’année 2015 :

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic
    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convertit une chaîne en une valeur dynamique.

### <a name="treepath"></a>treepath
    treepath(dynamic_object)

Énumère toutes les expressions de chemin qui identifient les feuilles dans un objet dynamique. 

**Retourne**

Un tableau d’expressions de chemin.

**Exemples**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Notez que « [0] » indique la présence d’un tableau, mais ne spécifie pas l’index utilisé par un chemin spécifique.

### <a name="zip"></a>zip
    zip(list1, list2, ...)

Regroupe un ensemble de listes dans une liste de tuples.

* `list1...`: liste de valeurs.

**Exemples**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]


    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>noms
Les noms peuvent comprendre jusqu’à 1 024 caractères. Ils respectent la casse et peuvent contenir des lettres, des chiffres et des traits de soulignement (`_`). 

Entourez de guillemets un nom à l’aide de ['... '] ou ["..."] pour inclure d’autres caractères, ou utilisez un mot-clé en tant que nom. Par exemple :

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|  |  |
| --- | --- |
| ['chemon d’accès\\fichier\n\'x\''] |Utilisez \ pour échapper les caractères |
| ["d-e.=/f#\n"] | |
| [@'path\file'] |Aucun caractère d’échappement - \ est littéral |
| [@"\now & then\"] | |
| [where] |Utilisation d’un mot-clé de langue comme nom |

[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


