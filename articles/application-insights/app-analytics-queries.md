<properties 
	pageTitle="Opérateurs et requêtes dans Application Insights Analytics" 
	description="Référence des opérateurs utilisés pour effectuer des requêtes dans Application Insights Analytics, le puissant outil de recherche d’Application Insights." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>



# Syntaxe des requêtes dans Application Insights Analytics


[Application Insights Analytics](app-analytics.md) est un puissant moteur de recherche pour vos données de télémétrie [Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Application Insights Analytics (AIQL).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

Une requête sur vos données de télémétrie est constituée d’une référence à un flux source, suivie d’un pipeline de filtres. Par exemple :


```AIQL
requests
| where client_City == "London" and timestamp > ago(3d)
| count
```
    
Chaque filtre précédé de la barre verticale `|` est une instance d’un *opérateur*, assortie de certains paramètres. L’entrée de l’opérateur est la table résultant du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](app-analytics-scalars.md) sur les colonnes de l’entrée. Dans certains cas, les paramètres correspondent aux noms des colonnes d’entrée et, parfois, le paramètre est une seconde table. Le résultat d’une requête est toujours une table, même si elle ne contient qu’une colonne et qu’une ligne.

Une requête peut être précédée d’une ou plusieurs [clauses Let](#let-clause), qui définissent les valeurs scalaires, les tables ou les fonctions pouvant être utilisées dans la requête.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T` est utilisé dans les exemples de requête ci-dessous pour désigner la table source ou le pipeline précédent.

## opérateur count

L’opérateur `count` retourne le nombre d’enregistrements (lignes) dans le jeu d’enregistrements d’entrée.

**Syntaxe**

    T | count

**Arguments**

* *T* : les données tabulaires dont les enregistrements doivent être comptabilisés.

**Retourne**

Cette fonction retourne une table contenant un seul enregistrement et une colonne de type `long`. La valeur de l’unique cellule correspond au nombre d’enregistrements dans *T*.

**Exemple**

```AIQL
requests | count
```



## opérateur extend

     T | extend duration = stopTime - startTime

Ajoute une ou plusieurs colonnes calculées à une table.


**Syntaxe**

    T | extend ColumnName = Expression [, ...]

**Arguments**

* *T :* la table d’entrée.
* *ColumnName :* nom de la colonne à ajouter. 
* *Expression :* calcul sur les colonnes existantes.

**Retourne**

Une copie de la table d’entrée, avec les colonnes supplémentaires spécifiées.

**Conseils**

* Utilisez plutôt [`project`](#project-operator), si vous souhaitez également supprimer ou renommer des colonnes.
* N’utilisez pas `extend` si vous souhaitez simplement obtenir un nom plus court à utiliser dans une expression longue. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    Les colonnes d’origine de la table ont été indexées. Votre nouveau nom définit une colonne supplémentaire qui n’est pas indexée, c’est pourquoi la requête risque de s’exécuter plus lentement.

**Exemple**

```AIQL
traces
| extend
    Age = now() - timestamp
```


## opérateur join

    Table1 | join (Table2) on CommonColumn

Fusionne les lignes de deux tables en faisant correspondre les valeurs de la colonne spécifiée.


**Syntaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Arguments**

* *Table1* - le « côté gauche » de la jointure.
* *Table2* - le « côté droit » de la jointure. Il peut s’agir d’une expression de requête imbriquée générant une table.
* *CommonColumn* - colonne portant le même nom dans les deux tables.
* *Kind* - spécifie la façon dont les lignes des deux tables doivent être mises en correspondance.

**Retourne**

Une table avec :

* Une colonne pour chaque colonne dans chacune des deux tables, y compris les clés correspondantes. Les colonnes du côté droit seront automatiquement renommées en cas de conflit de nom.
* Une ligne pour chaque correspondance entre les tables d’entrée. Une correspondance est une ligne sélectionnée dans une table ayant la même valeur pour tous les champs `on` qu’une ligne dans l’autre table. 

* `Kind` non spécifié

    Une seule ligne du côté gauche correspond à chaque valeur de la clé `on`. La sortie contient une ligne pour chaque correspondance de cette ligne avec des lignes du côté droit.

* `Kind=inner`
 
     La sortie contient une ligne pour chaque combinaison de lignes correspondantes des côtés gauche et droit.

* `kind=leftouter` (ou `kind=rightouter` ou `kind=fullouter`)

     Outre les correspondances internes, on trouve une ligne pour chaque ligne du côté gauche (et/ou droit), même s’il n’y a aucune correspondance. Dans ce cas, les cellules de sortie sans correspondance contiennent des valeurs null.

* `kind=leftanti`

     Retourne tous les enregistrements du côté gauche n’ayant pas de correspondance du côté droit. La table de résultats ne comporte que les colonnes du côté gauche.
 
Si plusieurs lignes comportent les mêmes valeurs pour ces champs, des lignes s’affichent pour toutes les combinaisons.

**Conseils**

Pour un résultat optimal :

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
    | project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```

[À propos des variantes de jointure](app-analytics-samples.md#join-flavors).

## clause let

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

Une clause let associe un nom à un résultat tabulaire, une valeur scalaire ou une fonction. La clause est un préfixe d’une requête tandis que la portée de la liaison correspond à cette requête. (La clause let ne permet pas de nommer des objets que vous pourrez utiliser ultérieurement dans votre session.)

**Syntaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

* *type :* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`, [`dynamic`](app-analytics-scalars.md#dynamic-type)
* *plain\_query :* requête non précédée d’une clause let.

**Exemples**




    let rows(n:long) = range steps from 1 to n step 1;
    rows(10) | ...


Jointure réflexive :

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
    | project start = timestamp, session_id
    | join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
    | extend duration = stop - start 

## opérateur limit

     T | limit 5

Retourne au maximum le nombre spécifié de lignes de la table d’entrée. Il n’est pas possible de savoir quels enregistrements vont être retournés. (Pour retourner des enregistrements spécifiques, utilisez [`top`](#top-operator).)

**Alias** `take`

**Syntaxe**

    T | limit NumberOfRows


**Conseils**

`Take` est un moyen simple et efficace d’afficher un aperçu des résultats lorsque vous travaillez de manière interactive. N’oubliez pas qu’il ne garantit pas de produire des lignes spécifiques ou de les afficher dans un ordre particulier.

Il existe une limite implicite quant au nombre de lignes retournées au client, même si vous n’utilisez pas `take`. Pour lever cette limite, utilisez l’option de requête client `notruncation`.



## opérateur mvexpand

    T | mvexpand listColumn 

Développe une liste à partir d’une cellule de type dynamique (JSON) afin que chaque entrée comporte une ligne distincte. Toutes les autres cellules d’une ligne développée sont dupliquées.

(Voir aussi [`summarize makelist`](#summarize-operator) qui effectue la fonction inverse.)

**Exemple**

En partant du principe que la table d’entrée est :

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|[0,1,"k","v"]|

    mvexpand D

Voici le résultat :

|A:int|B:string|D:dynamic|
|---|---|---|
|1|"hello"|{"key":"value"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**Syntaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Arguments**

* *ColumnName :* dans le résultat, les tableaux dans la colonne nommée sont développés en plusieurs lignes. 
* *ArrayExpression :* expression produisant un tableau. Si ce formulaire est utilisé, une nouvelle colonne est ajoutée et la colonne existante est conservée.
* *Name :* nom de la nouvelle colonne.
* *Typename :* applique l’expression développée à un type spécifique.
* *RowLimit :* nombre maximal de lignes générées à partir de chaque ligne d’origine. La valeur par défaut est 128.

**Retourne**

Plusieurs lignes pour chacune des valeurs dans n’importe quel tableau dans la colonne nommée ou dans l’expression de tableau.

La colonne développée est toujours de type dynamique. Utilisez un transtypage tel que `todatetime()` ou `toint()` si vous souhaitez calculer ou agréger des valeurs.

Deux modes de développement de conteneurs de propriétés sont pris en charge :

* `bagexpansion=bag` : les conteneurs de propriétés sont développés en conteneurs de propriété à entrée unique. Il s’agit du développement par défaut.
* `bagexpansion=array` : les conteneurs de propriétés sont développés dans des structures de tableau `[`*clé*`,`*valeur*`]` à deux éléments, ce qui permet un accès uniforme aux clés et valeurs (comme, par exemple, effectuer une agrégation de comptage de valeurs sur les noms de propriété). 

**Exemples**


    exceptions | take 1 
    | mvexpand details[0]

Fractionne un enregistrement d’exception en plusieurs lignes pour chaque élément du champ de détails.

Voir [Graphique du nombre d’activités en direct au fil du temps](app-analytics-samples.md#concurrent-activities).


## opérateur parse

    T | parse "I am 63 next birthday" with "I am" Year:int "next birthday"

    T | parse kind=regex "My 62nd birthday" 
        with "My" Year:regex("[0..9]+") regex("..") "birthday"

Extrait les valeurs d’une chaîne. Peut utiliser une correspondance d’expression simple ou régulière.

Les éléments dans la clause `with` sont ensuite mis en correspondance avec la chaîne source. Chaque élément traite un fragment du texte source. S’il s’agit d’une chaîne simple, le curseur correspondant se déplace aussi loin que la correspondance. S’il s’agit d’une colonne avec un nom de type, le curseur se déplace suffisamment loin pour analyser le type spécifié. (Les correspondances de chaîne se déplacent jusqu’à ce qu’une correspondance pour l’élément suivant soit identifiée.) S’il s’agit d’une expression régulière, elle est mis en correspondance (et la colonne en résultant a toujours le type chaîne).

**Syntaxe**

    T | parse StringExpression with [SimpleMatch | Column:Type] ...

    T | parse kind=regex StringExpression 
        with [SimpleMatch | Column : regex("Regex")] ...

**Arguments**

* *T :* la table d’entrée.
* *kind :* simple ou expression régulière. La valeur par défaut est simple.
* *StringExpression :* expression pouvant être convertie en chaîne ou en prendre la valeur.
* *SimpleMatch :* chaîne correspondant à la partie suivante du texte.
* *Column :* spécifie la nouvelle colonne à laquelle attribuer une correspondance.
* *Type :* détermine comment analyser la partie suivante de la chaîne source.
* *Regex :* expression régulière pour faire correspondre la partie suivante de la chaîne. 

**Retourne**

La table d’entrée, étendue en fonction de la liste des colonnes.


**Exemples**

L’opérateur `parse` offre un moyen simple de `extend` une table en utilisant plusieurs applications `extract` sur la même expression `string`. Il est particulièrement utile lorsque la table comporte une colonne `string` contenant plusieurs valeurs à répartir en différentes colonnes, par exemple une colonne qui a été générée par une instruction (« `printf` »/« `Console.WriteLine` ») de trace de développeur.

Dans l’exemple suivant, partons du principe que la colonne `EventNarrative` de la table `StormEvents` contient des chaînes sous la forme `{0} at {1} crested at {2} feet around {3} on {4} {5}`. L’opération ci-dessous va étendre la table avec deux colonnes : `SwathSize` et `FellLocation`.


|EventNarrative|
|---|
|La rivière Green River à Brownsville est montée à 18,8 pieds aux alentours de 9 h 30 EST le 12 décembre. À Brownsville, la côte d’inondation est fixée à 18 pieds. À ce niveau, on parle d’une inondation mineure. La rivière sort de son lit et inonde une partie des berges basses ainsi que certaines terres agricoles.|
|La rivière Rolling Fork River à Boston est montée à 39,3 pieds aux alentours de 17 h 00 EST le 12 décembre. À Boston, la côte d’inondation est fixée à 35 pieds. À ce niveau, on parle d’une inondation mineure et certaines terres agricoles commencent à être immergées.|
|La rivière Green River à Woodbury est montée à 36,7 pieds aux alentours de 6 h 00 EST le 16 décembre. À Woodbury, la côte d’inondation est fixée à 33 pieds. À ce niveau, on parle d’une inondation mineure et certaines plaines autour de la ville de Woodbury commencent à être immergées.|
|La rivière Ohio River à Tell City est montée à 39,0 pieds aux alentours de 7 h 00 EST le 18 décembre. À Tell City, la côte d’inondation est fixée à 38 pieds. À ce niveau, la rivière commence à déborder et à inonder ses berges. L’autoroute Indiana Highway 66 est inondée entre Rome et Derby.|

```AIQL

StormEvents 
|  parse EventNarrative 
   with RiverName:string 
        "at" 
        Location:string 
        "crested at" 
        Height:double  
        "feet around" 
        Time:string 
        "on" 
        Month:string 
        " " 
        Day:long 
        "." 
        notImportant:string
| project RiverName , Location , Height , Time , Month , Day

```

|RiverName|Emplacement|Hauteur|Time|Mois|jour|
|---|---|---|---|---|---|
|Green River | Woodbury |36,7| 6 h 00 EST | Décembre|16|
|Rolling Fork River | Boston |39,3| 17 h 00 EST | Décembre|12|
|Green River | Brownsville |18,8| 9 h 30 EST | Décembre|12|
|Ohio River | Tell City |39| 7 h 00 EST | Décembre|18|

Il est également possible d’établir une correspondance à l’aide d’expressions régulières. Le résultat est le même mais toutes les colonnes de résultat ont le type chaîne :

```AIQL

StormEvents
| parse kind=regex EventNarrative 
  with RiverName:regex("(\\s?[a-zA-Z]+\\s?)+") 
  "at" Location:regex(".*") 
  "crested at " Height:regex("\\d+\\.\\d+") 
  " feet around" Time:regex(".*") 
  "on " Month:regex("(December|November|October)") 
   " " Day:regex("\\d+") 
   "." notImportant:regex(".*")
| project RiverName , Location , Height , Time , Month , Day
```


## opérateur project

    T | project cost=price*quantity, price

Sélectionnez les colonnes à inclure, renommer ou supprimer, puis insérez les nouvelles colonnes calculées. L’ordre des colonnes dans le résultat est déterminé par l’ordre des arguments. Seules les colonnes spécifiées dans les arguments sont incluses dans le résultat : les autres sont supprimées. (Voir aussi `extend`.)


**Syntaxe**

    T | project ColumnName [= Expression] [, ...]

**Arguments**

* *T :* la table d’entrée.
* *ColumnName :* nom d’une colonne à afficher dans la sortie. S’il n’y a aucune *Expression*, une colonne portant ce nom doit apparaître dans l’entrée. 
* *Expression :* expression scalaire facultative faisant référence aux colonnes de l’entrée. 

    Il est possible de retourner une nouvelle colonne calculée portant le même nom qu’une colonne figurant dans l’entrée.

**Retourne**

Une table contenant les colonnes nommées en tant qu’arguments, et autant de lignes que la table d’entrée.

**Exemple**

L’exemple suivant présente plusieurs types de manipulations pouvant être effectuées à l’aide de l’opérateur `project`. La table d’entrée `T` comporte trois colonnes de type `int` : `A`, `B` et `C`.

```AIQL
T
| project
    X=C,                       // Rename column C to X
    A=2*B,                     // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B                      // Calculate a new column B from the old B
```


[Exemples supplémentaires](app-analytics-samples.md#activities).


## opérateur range

    range LastWeek from ago(7d) to now() step 1d

Génère une table de valeurs à une seule colonne. Notez qu’elle ne comporte pas d’entrée de pipeline.

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|



**Syntaxe**

    range ColumnName from Start to Stop step Step

**Arguments**

* *ColumnName :* nom de la colonne unique dans la table de sortie.
* *Start :* la plus petite valeur de la sortie.
* *Stop :* la valeur la plus élevée générée dans la sortie (ou une limite pour la valeur la plus élevée, si l’*étape* dépasse cette valeur).
* *Step :* différence entre deux valeurs consécutives. 

Les arguments doivent être des valeurs de type numérique, date ou durée. Ils ne peuvent pas faire référence aux colonnes d’une table. (Si vous souhaitez calculer la plage d’après une table d’entrée, utilisez la [fonction *range*](app-analytics-scalars.md#range), éventuellement avec l’[opérateur mvexpand](#mvexpand-operator).)

**Retourne**

Une table comportant une seule colonne appelée *ColumnName*, dont les valeurs sont*Start*, *Start* + *Step*, ... jusqu’à *Stop* inclus.

**Exemple**

```AIQL
range Steps from 1 to 8 step 3
```

Une table comportant une seule colonne appelée `Steps` dont le type est `long` et dont les valeurs sont `1`, `4` et `7`.

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

Montre comment l’opérateur `range` peut être utilisé pour créer une petite table de dimension ad hoc qui est ensuite utilisée pour intégrer des zéros là où les données source ne contiennent aucune valeur.

## opérateur reduce

    exceptions | reduce by outerMessage

Tente de regrouper des enregistrements similaires. Pour chaque groupe, l’opérateur génère le `Pattern` décrivant le mieux ce groupe et le `Count` d’enregistrements dans ce groupe.


![](./media/app-analytics-queries/reduce.png)

**Syntaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Arguments**

* *ColumnName :* colonne à examiner. Cette valeur doit être de type chaîne.
* *Threshold :* valeur dans la plage {0..1}. La valeur par défaut est 0,001. Pour les entrées volumineuses, le seuil doit être bas. 

**Retourne**

Deux colonnes, `Pattern` et `Count`. Dans de nombreux cas, le modèle est une valeur complète de la colonne. Dans certains cas, il peut identifier des termes courants et remplacer les parties variables avec '*'.

Par exemple, le résultat de `reduce by city` peut inclure :

|Modèle | Nombre |
|---|---|
| San * | 5182 |
| Saint * | 2846 |
| Moscow | 3726 |
| * -on- * | 2730 |
| Paris | 27163 |


## directive render

    T | render [ table | timechart  | barchart | piechart ]

Render indique à la couche de présentation comment afficher la table. Il doit s’agir du dernier élément du canal. C’est une alternative pratique à l’utilisation des commandes affichées, ce qui vous permet d’enregistrer une requête avec une méthode de présentation particulière.


## opérateur sort 

    T | sort by country asc, price desc

Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

**Alias** `order`

**Syntaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Arguments**

* *T :* table d’entrée à trier.
* *Column :* colonne de *T* à laquelle appliquer le tri. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` Tri par ordre croissant, de faible à élevé. La valeur par défaut est `desc`, par ordre décroissant allant d’élevé à faible.

**Exemple**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Toutes les lignes dans la table Traces ayant un `ActivityId` spécifique, triées d’après leur horodatage.

## opérateur summarize

Génère une table qui agrège le contenu de la table d’entrée.
 
    requests
	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Une table qui indique le nombre, la durée moyenne des requêtes et un ensemble de villes dans chaque pays. La sortie comporte une ligne pour chaque pays. Les colonnes de sortie affichent le nombre, la durée moyenne, les villes et le pays. Toutes les autres colonnes d’entrée sont supprimées.


    T | summarize count() by price_range=bin(price, 10.0)

Une table indiquant le nombre d’éléments ayant un prix dans chaque intervalle [0,10.0], [10.0,20.0] et ainsi de suite. Cet exemple comprend une colonne pour le nombre et une autre pour la gamme de prix. Toutes les autres colonnes d’entrée sont supprimées.

[Exemples supplémentaires](app-analytics-aggregations.md).



**Syntaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Arguments**

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *Aggregation :* appel d’une [fonction d’agrégation](app-analytics-aggregations.md) telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir la [liste des fonctions d’agrégation](app-analytics-aggregations.md).
* *GroupExpression :* expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En général, il s’agit d’un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou de `bin()` avec une colonne numérique ou de temps en tant qu’argument. 

Si vous fournissez une expression numérique ou de temps sans utiliser `bin()`, AI Analytics l’applique automatiquement avec un intervalle de `1h` pour les heures ou `1.0` pour les nombres.

Si vous ne fournissez pas une *GroupExpression*, la table entière est résumée dans une ligne de sortie unique.



**Retourne**

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs que les expressions `by`. Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by` et au moins une colonne pour chaque agrégation calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat contient autant de lignes qu’il existe de combinaisons de valeurs `by`. Si vous souhaitez générer une synthèse sur des plages de valeurs numériques, utilisez `bin()` afin de limiter les plages à des valeurs discrètes.

**Remarque :**

Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou d’appliquer `bin()` à une colonne numérique.



## opérateur take

Alias de [limite](#limit-operator)


## opérateur top

    T | top 5 by Name desc

Retourne les *N* premiers enregistrements triés d’après les colonnes spécifiées.


**Syntaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [, ... ]

**Arguments**

* *NumberOfRows :* nombre de lignes de *T* à retourner.
* *Sort\_expression :* expression utilisée pour trier les lignes. Il s’agit généralement juste d’un nom de colonne. Vous pouvez spécifier plusieurs expressions sort\_expression.
* `asc` ou `desc` (la valeur par défaut) peut permettre d’indiquer si la sélection est effectuée à partir du « bas » ou du « haut » de la plage.


**Conseils**

`top 5 by name` équivaut approximativement à `sort by name | take 5`. Toutefois, cet opérateur s’exécute plus rapidement et retourne toujours les résultats triés, contrairement à `take`.


## opérateur union

     Table1 | union Table2, Table3

Prend deux tables ou plus et retourne les lignes de toutes les tables.

**Syntaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Arguments**

* *Table1*, *Table2* ...
 *  Nom d’une table, tel que `events` ; ou
 *  Expression de requête, telle que `(events | where id==42)`
 *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` assure l’union de toutes les tables dans la base de données dont le nom commence par `E`.
* `kind` : 
 * `inner` -Le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
 * `outer` -Le résultat comporte toutes les colonnes qui apparaissent dans les entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée sont définies sur `null`.
* `withsource=`*ColumnName :* si spécifiée, la sortie va inclure une colonne appelée *ColumnName* dont la valeur indique la table source correspondant à chaque ligne.

**Retourne**

Une table contenant autant de lignes que dans l’ensemble des tables d’entrée.

**Exemple**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
Union de toutes les tables dont le nom commence par « tt ».


**Exemple**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
Le nombre d’utilisateurs ayant produit un événement `exceptions` ou un événement `traces` au cours de la journée précédente. Dans le résultat, la colonne ’SourceTable’ indique « Requête » ou « Commande ».

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Cette version plus efficace génère le même résultat. Il filtre chaque table avant la création de l’union.

## opérateur where

     T | where fruit=="apple"

Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

**Alias** `filter`

**Syntaxe**

    T | where Predicate

**Arguments**

* *T* : entrée tabulaire dont les enregistrements doivent être filtrés.
* *Predicate :* `boolean`[expression](app-analytics-scalars.md#boolean) sur les colonnes de *T*. Elle est évaluée pour chaque ligne dans *T*.

**Retourne**

Les lignes de *T* dont le *prédicat* est `true`.

**Conseils**

Pour obtenir des performances optimales :

* **Utilisez des comparaisons simples** entre les noms de colonne et les constantes. (« Constante » s’entend dans le sens de constante au fil de la table, de telle sorte que `now()` et `ago()` soient OK, tout comme les valeurs scalaires affectées à l’aide d’une [instruction `let`](app-analytics-syntax.md#let-statements).)

    Par exemple, préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest terms first** : si vous avez plusieurs clauses unies avec `and`, insérez d’abord les clauses n’impliquant qu’une seule colonne. C’est pourquoi `Timestamp > ago(1d) and OpId == EventId` est plus adapté.


**Exemple**

```AIQL
Traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Enregistrements datant de moins de 1 heure et provenant de la source nommée « Kuskus » et ayant deux colonnes de la même valeur.

Notez que nous plaçons la comparaison entre deux colonnes à la fin, car elle ne peut pas utiliser l’index et force une analyse.





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0316_2016-->