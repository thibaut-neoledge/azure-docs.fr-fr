<properties 
	pageTitle="Expressions scalaires dans Application Insights Analytics" 
	description="Nombres, chaînes, expressions dynamiques et types dans Application Insights Analytics, outil de recherche puissant pour Application Insights." 
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
	ms.date="03/05/2016" 
	ms.author="awills"/>


 
# Expressions scalaires dans Application Insights Analytics


[Application Insights Analytics](app-analytics.md) est un puissant moteur de recherche pour vos données de télémétrie [Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Application Insights Analytics (AIQL).

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

---

[ago](#ago) | [arraylength](#arraylength) | [bin](#bin) [countof](#countof) | [dayofweek](#dayofweek) | [extract](#extract) | [extractjson](#extractjson) | [floor](#floor) <br/>[getmonth](#getmonth) | [gettype](#gettype) [getyear](#getyear) | [hash](#hash) | [iff](#iff) | [isempty](#isempty) | [isnotempty](#isnotempty) | [isnull](#isnull) | [isnotnull](#isnotnull) <br/> [now](#now) | [notempty](#notempty) | [notnull](#notnull) | [parsejson](#parsejson)| [rand](#rand) | [range](#range) | [replace](#replace) | [split](#split) | [sqrt](#sqrt) <br/>[startofmonth](#startofmonth) | [startofyear](#startofyear) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [toupper](#toupper) | [treepath](#treepath)

---



« Scalaire » indique des valeurs comme des nombres ou des chaînes qui peuvent occuper une cellule unique dans une table AIQL. Les expressions scalaires sont créées à partir d’opérateurs et de fonctions scalaires et donnent des valeurs scalaires. `sqrt(score)/100 > target+2` est une expression scalaire.

« Scalaire » inclut également des tableaux et des objets composites qui peuvent également être stockés dans une cellule unique de la base de données.

Les expressions scalaires sont distinctes des [requêtes](app-analytics-queries.md), dont les résultats sont des tables.

## Valeurs scalaires

[casts](#casts) | [comparisons](#scalar-comparisons) <br/> [gettype](#gettype) | [hash](#hash) | [iff](#iff)| [isnull](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull)

Les types pris en charge sont :

| Type | Nom(s) supplémentaire(s) | Type .NET équivalent |
| --------- | -------------------- | -------------------- |
| `bool` | `boolean` | `System.Boolean` |
| `datetime`| `date` | `System.DateTime` |
| `dynamic` | | `System.Object` |
| `guid` | `uuid`, `uniqueid` | `System.Guid` |
| `int` | | `System.Int32` |
| `long` | | `System.Int64` |
| `double` | `real` | `System.Double` |
| `string` | | `System.String` |
| `timespan`| `time` | `System.TimeSpan` |

### Casts

Vous pouvez effectuer une conversion d’un type en un autre. En général, si la conversion a un sens, cela fonctionnera :

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
	toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

### Comparaisons scalaires

||
---|---
`<` |Inférieur à
`<=`|Inférieur ou égal à
`>` |Supérieur à
`>=`|Supérieur ou égal à
`<>`|Non égal à
`!=`|Non égal à 
`in`| L’opérande de droite est un tableau (dynamique) et l’opérande de gauche est égal à un de ses éléments.
`!in`| L’opérande de droite est un tableau (dynamique) et l’opérande de gauche n’est pas égal à un de ses éléments.




### gettype

**Retourne**

Une chaîne représentant le type de stockage sous-jacent de son argument unique. C’est particulièrement utile avec des valeurs de type `dynamic` : dans ce cas `gettype()` indique comment une valeur est codée.

**Exemples**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8" (*) `
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`



### Hachage

**Syntaxe**

    hash(source [, mod])

**Arguments**

* *source* : source scalaire à partir de laquelle le hachage est calculé.
* *mod* : valeur modulo à appliquer au résultat de hachage.

**Retourne**

La valeur xxhash (long) de la valeur scalaire donnée, modulo, la valeur mod donnée (si spécifiée).

**Exemples**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### iff

La fonction `iff()` évalue le premier argument (le prédicat) et retourne la valeur du deuxième ou troisième argument selon que le prédicat est `true` ou `false`. Les deuxième et troisième arguments doivent être de même type.

**Syntaxe**

    iff(predicate, ifTrue, ifFalse)


**Arguments**

* *predicate :* expression qui correspond à une valeur `boolean`.
* *ifTrue :* expression qui est évaluée et sa valeur retournée par la fonction si *predicate* prend la valeur `true`.
* *ifFalse :* expression qui est évaluée et sa valeur retournée par la fonction si *predicate* prend la valeur `false`.

**Retourne**

Cette fonction retourne la valeur de *ifTrue* si *predicate* prend la valeur `true`, ou la valeur de *ifFalse* dans le cas contraire.

**Exemple**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a> <a name="isnotnull"/></a> <a name="notnull"/></a>
### isnull, isnotnull, notnull

    isnull(parsejson("")) == true

Accepte un argument unique et indique si la valeur est null.

**Syntaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Retourne**

True ou false selon si la valeur est null ou not null.


|x|isnull(x)
|---|---
| "" | false
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false

**Exemple**

    T | where isnotnull(PossiblyNull) | count

Notez qu’il existe d’autres façons d’obtenir cet effet :

    T | summarize count(PossiblyNull)




## Boolean 

### Littéraux booléens

	true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### Opérateurs booléens

	and 
    or 

    

## Nombres

[bin](#bin) | [floor](#floor) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### Littéraux numériques

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### Opérateurs arithmétiques

|| |
|---|-------------|
| + | Ajouter |
| - | Soustraire || * | Multiplier || / | Diviser || % | Modulo | || |`<` |Inférieur à |`<=`|Inférieur ou égal à |`>` |Supérieur à |`>=`|Supérieur ou égal à |`<>`|Non égal à |`!=`|Non égal à




### bin

Arrondit les valeurs à l’entier inférieur multiple d’une taille bin donnée. Très utilisé dans la requête [`summarize by`](app-analytics-queries.md#summarize-operator). Si vous avez un ensemble de valeurs dispersées, elles seront regroupées dans un plus petit ensemble de valeurs spécifiques.

Alias `floor`.

**Syntaxe**

     bin(value, roundTo)

**Arguments**

* *value:* nombre, date ou intervalle de temps. 
* *roundTo:* la « taille bin ». Nombre, date ou intervalle de temps qui divise *value*. 

**Retourne**

Le multiple le plus proche de *roundTo* en dessous de *value*.
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemples**

Expression | Résultat
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`| `datetime(1953-04-15)`


L’expression suivante calcule un histogramme de durées, avec une taille de compartiment de 1 seconde :

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### floor

Alias pour [`bin()`](#bin).


### rand

Générateur de nombres aléatoires.

* `rand()` - Nombre réel compris entre 0,0 et 1,0
* `rand(n)` - Entier compris entre 0 et n-1




### sqrt

Fonction racine carrée.

**Syntaxe**

    sqrt(x)

**Arguments**

* *x :* nombre réel > = 0.

**Retourne**

* Un nombre positif tel que `sqrt(x) * sqrt(x) == x`
* `null` si l’argument est négatif ou s’il ne peut pas être converti en une valeur `real`. 




### toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## Date et heure


[ago](#ago) | [dayofweek](#dayofweek) | [getmonth](#getmonth)| [getyear](#getyear) | [now](#now) | [startofmonth](#startofmonth) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan)

### Littéraux de date et d’heure

|||
---|---
**datetime**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Les heures sont toujours exprimées en UTC. L’omission de la date entraîne la sélection de la date du jour.
`now()`|L’heure actuelle.
`now(`-*timespan*`)`|`now()-`*timespan*
`ago(`*timespan*`)`|`now()-`*timespan*
**timespan**|
`2d`|2 jours
`1.5h`|1,5 heure 
`30m`|30 minutes
`10s`|10 secondes
`0.1s`|0,1 seconde
`100ms`| 100 millisecondes
`10microsecond`|
`1tick`|100ns
`time("15 seconds")`|
`time("2")`| 2 jours
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### Expressions de date et d’heure

Expression |Résultat
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d`/`2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Inférieur à
`<=`|Inférieur ou égal à
`>` |Supérieur à
`>=`|Supérieur ou égal à
`<>`|Non égal à
`!=`|Non égal à 




### ago

Soustrait l’intervalle de temps donné de l’heure UTC actuelle. Comme `now()`, cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure UTC référencée est la même pour toutes les instanciations.

**Syntaxe**

    ago(a_timespan)

**Arguments**

* *a\_timespan* : intervalle à soustraire de l’heure UTC actuelle (`now()`).

**Retourne**

    now() - a_timespan

**Exemple**

Toutes les lignes de l’horodatage de la dernière heure :

```AIQL

    T | where timestamp > ago(1h)
```



### dayofweek

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

Nombre entier de jours écoulés depuis le précédent dimanche, en tant que `timespan`.

**Syntaxe**

    dayofweek(a_date)

**Arguments**

* `a_date` : un `datetime`.

**Retourne**

Le `timespan` depuis minuit au début du précédent dimanche, arrondi au nombre entier inférieur de jours.

**Exemples**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### getmonth

Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.

**Exemple**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### getyear

Obtient l’année à partir d’une valeur datetime.

**Exemple**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### now

    now()
    now(-2d)

L’heure UTC actuelle, avec un décalage éventuel selon un intervalle de temps donné. Cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure référencée est la même pour toutes les instances.

**Syntaxe**

    now([offset])

**Arguments**

* *offset :* un `timespan` ajouté à l’heure UTC actuelle. Valeur par défaut : 0.

**Retourne**

L’heure UTC actuelle en tant que `datetime`.

    now() + offset

**Exemple**

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

### startofmonth

    startofmonth(date)

Début du mois qui contient la date.

### startofyear

    startofyear(date)

Début de l’année qui contient la date.


### todatetime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0])  // cast a dynamic type
     todatetime(b.c)   // cast a dynamic type

### totimespan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)


## Chaîne

[countof](#countof) | [extract](#extract) | [extractjson](#extractjson) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [replace](#replace) | [split](#split) | [strcat](#strcat) | [strlen](#strlen) | [substring](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### Littéraux de chaîne

Les règles sont les mêmes que dans JavaScript.

Les chaînes peuvent être placées entre guillemets simples ou doubles.

La barre oblique inverse (``) est utilisée pour échapper les caractères tels que `\t` (tabulation), `\n` (renvoi à la ligne) et les instances de guillemets englobants.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double " quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### Littéraux de chaîne masqués

Les littéraux de chaîne masqués sont des chaînes qu’AI Analytics masquera lors de la sortie de la chaîne (par exemple, lors d’un suivi). Le processus de masquage remplace tous les caractères masqués par un caractère de démarrage (`*`).

Pour créer un littéral de chaîne masqué, ajouter `h` ou « H ». Par exemple :

```
h'hello'
h@'world' 
h"hello"
```

### Comparaisons de chaînes

Opérateur|Description|Respecte la casse|Exemple vrai
---|---|---|---
`==`|Égal à |Oui| `"aBc" == "aBc"`
`<>`|Non égal à|Oui| `"abc" <> "ABC"`
`=~`|Égal à |Non| `"abc" =~ "ABC"`
`!~`|Non égal à |Non| `"aBc" !~ "xyz"`
`has`|Le terme de droite est un terme entier dans le terme de gauche|Non| `"North America" has "america"`
`!has`|Le terme de droite n’est pas un terme entier dans le terme de gauche|Non|`"North America" !has "amer"` 
`contains` | Le terme de droite est une sous-séquence du terme de gauche|Non| `"FabriKam" contains "BRik"`
`!contains`| Le terme de droite n’est pas une sous-séquence du terme de gauche|Non| `"Fabrikam" !contains "xyz"`
`containscs` | Le terme de droite est une sous-séquence du terme de gauche|Oui| `"FabriKam" contains "Kam"`
`!containscs`| Le terme de droite n’est pas une sous-séquence du terme de gauche|Oui| `"Fabrikam" !contains "Kam"`
`startswith`|Le terme de droite est une sous-séquence initiale du terme de gauche|Non|`"Fabrikam" startswith "fab"`
`matches regex`|Le terme de gauche contient une correspondance du terme de droite|Oui| `"Fabrikam" matches regex "b.*k"`


Utilisez `has` ou `in` si vous testez la présence d’un terme lexical entier, c’est-à-dire un symbole ou un mot alphanumérique délimité par des caractères non alphanumériques ou le début ou la fin de champ. `has` effectue la recherche plus rapidement que `contains` ou `startswith`. La première de ces requêtes s’exécute plus rapidement :

    EventLog | where continent has "North" | count;
	EventLog | where continent contains "nor" | count





### countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Compte les occurrences d’une sous-chaîne dans une chaîne. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

**Syntaxe**

    countof(text, search [, kind])

**Arguments**

* *text :* une chaîne.
* *search :* chaîne simple ou [expression régulière](app-analytics-reference.md#regular-expressions) à faire correspondre au *text*.
* *kind :* `"normal"|"regex"`. Valeur par défaut : `normal`. 

**Retourne**

Le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

**Exemples**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (pas 2 !)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### extract

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obtient une correspondance pour une [expression régulière](app-analytics-reference.md#regular-expressions) à partir d’une chaîne de texte. Éventuellement, il convertit ensuite la sous-chaîne extraite dans le type indiqué.

**Syntaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Arguments**

* *regex :* une [expression régulière](app-analytics-reference.md#regular-expressions).
* *captureGroup :* constante `int` positive qui indique le groupe de capture à extraire. Les valeurs sont 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
* *text :* une `string` à rechercher.
* *typeLiteral :* un littéral de type facultatif (par exemple, `typeof(long)`). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type. 

**Retourne**

Si *regex* trouve une correspondance dans *text* : la sous-chaîne mise en correspondance avec le groupe de capture indiqué *captureGroup*, éventuellement converti en *typeLiteral*.

Si aucune correspondance n’est trouvée, ou si la conversion de type échoue : `null`.

**Exemples**

L’exemple de chaîne `Trace` est recherché dans une définition de `Duration`. La correspondance est convertie en `real`, qui est ensuite multiplié par une constante de temps (`1s`) afin que `Duration` soit de type `timespan`. Dans cet exemple, elle est égale à 123,45 secondes :

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Cet exemple est équivalent à `substring(Text, 2, 4)` :

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a> <a name="isnotempty"></a> <a name="isempty"></a>
### isempty, isnotempty, notempty

    isempty("") == true

True si l’argument est une chaîne vide ou s’il a la valeur null. Voir aussi [isnull](#isnull).


**Syntaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Retourne**

Indique si l’argument est une chaîne vide ou s’il a la valeur isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("")|false
|parsejson("{}")|false


**Exemple**


    T | where isempty(fieldName) | count




### replace

Remplace toutes les correspondances d’expression régulière par une autre chaîne.

**Syntaxe**

    replace(regex, rewrite, text)

**Arguments**

* *regex :* l’[expression régulière](https://github.com/google/re2/wiki/Syntax) pour rechercher le *text*. Elle peut contenir des groupes de capture entre '('parenthèses')'. 
* *rewrite :* l’expression régulière de remplacement pour toute correspondance trouvée par *matchingRegex*. Utilisez `\0` pour faire référence à la correspondance complète, `\1` pour le premier groupe de capture, `\2` et ainsi de suite pour les groupes de capture suivants.
* *text :* une chaîne.

**Retourne**

*text* après le remplacement de toutes les correspondances de *regex* par les évaluations de *rewrite*. Les correspondances ne se chevauchent pas.

**Exemple**

L’instruction suivante :

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Donne les résultats suivants :

| x | str | replaced|
|---|---|---|
| 1 | Le nombre est 1.000000 | Le nombre était : 1.000000|
| 2 | Le nombre est 2.000000 | Le nombre était : 2.000000|
| 3 | Le nombre est 3.000000 | Le nombre était : 3.000000|
| 4 | Le nombre est 4.000000 | Le nombre était : 4.000000|
| 5 | Le nombre est 5.000000 | Le nombre était : 5.000000|
 



### split

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Fractionne une chaîne donnée en fonction d’un délimiteur donné et retourne un tableau de chaînes avec les sous-chaînes contenues. Éventuellement, une sous-chaîne spécifique peut être retournée si elle existe.

**Syntaxe**

    split(source, delimiter [, requestedIndex])

**Arguments**

* *source* : chaîne source qui sera fractionnée en fonction du délimiteur donné.
* *delimiter* : délimiteur qui sera utilisé pour fractionner la chaîne source.
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




### strcat

    strcat("hello", " ", "world")

Concatène entre 1 et 16 arguments, qui doivent être des chaînes.

### strlen

    strlen("hello") == 5

Longueur d’une chaîne.

### substring

    substring("abcdefg", 1, 2) == "bc"

Extrait une sous-chaîne d’une chaîne source donnée à partir d’un index donné. Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

**Syntaxe**

    substring(source, startingIndex [, length])

**Arguments**

* *source :* chaîne source dont la sous-chaîne est extraite.
* *startingIndex :* position du caractère de départ de base zéro de la sous-chaîne demandée.
* *length :* paramètre facultatif qui peut être utilisé pour spécifier le nombre de caractères demandé dans la sous-chaîne. 

**Retourne**

Une sous-chaîne de la chaîne donnée. La sous-chaîne commence à la position de caractère startingIndex (de base zéro) et continue jusqu’à la fin de la chaîne ou des caractères de longueur si elle est spécifiée.

**Exemples**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### tolower

    tolower("HELLO") == "hello"

Convertit une chaîne en minuscules.

### toupper

    toupper("hello") == "HELLO"

Convertit une chaîne en majuscules.



## GUID

    guid(00000000-1111-2222-3333-055567f333de)


## Tableaux et objets - types dynamiques

[literals](#dynamic-literals) | [casting](#casting-dynamic-objects) | [operators](#operators) | [let clauses](#dynamic-objects-in-let-clauses) <br/> [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [range](#range) | [treepath](#treepath) | [todynamic](#todynamic)


Voici le résultat d’une requête sur une exception d’Application Insights. La valeur de `details` est un tableau.

![](./media/app-analytics-scalars/310.png)

**Indexing :** tableaux d’index et objets comme dans JavaScript :

    exceptions | take 1
    | extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mais utilise `arraylength` et d’autres fonctions AIQL (pas « .length » !)

**Conversion** Dans certains cas, il est nécessaire de convertir un élément que vous extrayez à partir d’un objet, car son type peut varier. Par exemple, `summarize...to` a besoin d’un type spécifique :

    exceptions 
    | summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
    | summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Littéraux** Pour créer un tableau explicite ou un objet conteneur de propriétés, écrivez-le en tant que chaîne JSON et effectuez la conversion :

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand :** pour décomposer les propriétés d’un objet dans des lignes distinctes, utilisez mvexpand :

    exceptions | take 1 
    | mvexpand details[0].parsedStack[0]


![](./media/app-analytics-scalars/410.png)


**treepath :** pour rechercher tous les chemins dans un objet complexe :

    exceptions | take 1 | project timestamp, details 
    | extend path = treepath(details) 
    | mvexpand path


![](./media/app-analytics-scalars/420.png)

**buildschema :** pour trouver le schéma minimum qui admet toutes les valeurs de l’expression dans la table :

    exceptions | summarize buildschema(details)

Résultat :

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
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

`indexer` indique où vous devez utiliser un index numérique. Pour ce schéma, voici certains chemins valides (en supposant que ces exemples d’index appartiennent à la plage) :

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### Littéraux de tableau et d’objet

Pour créer un littéral dynamique, utilisez `parsejson` (alias `todynamic`) avec un argument de chaîne JSON :

* `parsejson('[43, 21, 65]')` - tableau de nombres
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')` - valeur unique de type dynamique qui contient un nombre
* `parsejson('"21"')` - valeur unique de type dynamique qui contient une chaîne

Notez que, contrairement à JavaScript, JSON impose l’utilisation de guillemets doubles (`"`) autour des chaînes. Par conséquent, il est généralement plus facile de placer les littéraux de chaîne codés JSON à l’aide de guillemets simples (`'`).

Cet exemple crée une valeur dynamique, puis utilise ses champs :

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


<a name="operators"></a>
### Opérateurs et fonctions sur des types dynamiques

|||
|---|---|
| *value* `in` *array*| True s’il existe un élément de *array* qui == *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True s’il n’existe aucun élément de *array* qui == *value*
|[`arraylength(`array`)`](#arraylength)| Null si ce n’est pas un tableau
|[`extractjson(`path,object`)`](#extractjson)|Utilise le chemin pour accéder à l’objet.
|[`parsejson(`source`)`](#parsejson)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`from,to,step`)`](#range)| Tableau de valeurs
|[`mvexpand` listColumn](app-analytics-queries.md#mvexpand-operator) | Réplique une ligne pour chaque valeur d’une liste dans une cellule spécifiée.
|[`summarize buildschema(`column`)`](app-analytics-queries.md#summarize-operator) |Déduit le schéma du type à partir du contenu de la colonne
|[`summarize makelist(`column`)` ](app-analytics-queries.md#summarize-operator)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize makeset(`column`)`](app-analytics-queries.md#summarize-operator) | Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication.

### Objets dynamiques dans les clauses let


Les [clauses let](app-analytics-queries.md#let-clause) stockent des valeurs dynamiques sous forme de chaînes, afin que ces deux clauses soient équivalentes, et elles nécessitent toutes deux `parsejson` (ou `todynamic`) avant d’être utilisées :

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### arraylength

Nombre d’éléments dans un tableau dynamique.

**Syntaxe**

    arraylength(array)

**Arguments**

* *array :* une `dynamic` valeur.

**Retourne**

Le nombre d’éléments dans *array*, ou `null` si *array* n’est pas un tableau.

**Exemples**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obtient un élément spécifié à partir d’un texte JSON à l’aide d’une expression de chemin. Convertit éventuellement la chaîne extraite un type spécifique.


**Syntaxe**

```

    string extractjson(jsonPath, dataSource)​​ 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))​​
```


**Retourne**

Cette fonction effectue une requête JsonPath dans dataSource qui contient une chaîne JSON valide, convertissant éventuellement cette valeur en un autre type selon le troisième argument.



**Exemple**

La notation entre [crochets] et la notation sous forme de points sont équivalentes :

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...

#### Expressions de chemin JSON

|||
|---|---|
|`$`|Objet racine|
|`@`|Objet en cours|
|`.` ou `[ ]` | Enfant|
|`[ ]`|Indice de tableau|

*(Actuellement, nous n’implémentons pas les caractères génériques, la récursivité, l’union ou les tranches.)*


**Conseils sur les performances**

* Appliquez les clauses where avant d’utiliser `extractjson()`
* Envisagez d’utiliser une correspondance d’expression régulière avec [extract](#extract) à la place. L’exécution peut être beaucoup plus rapide, et elle est efficace si le JSON est généré à partir d’un modèle.
* Utilisez `parsejson()` si vous avez besoin d’extraire plusieurs valeurs à partir du JSON.
* Envisagez d’analyser le JSON lors de l’ingestion en déclarant le type de la colonne comme étant dynamique.



### parsejson

Interprète une `string` comme une [valeur JSON](http://json.org/) et retourne la valeur en tant que `dynamic`. Son efficacité est supérieure à l’utilisation de `extractjson()` lorsque vous devez extraire plusieurs éléments d’un objet composé JSON.

**Syntaxe**

    parsejson(json)

**Arguments**

* *json :* document JSON.

**Retourne**

Un objet de type `dynamic` spécifié par *json*.

**Exemple**

Dans l’exemple suivant, lorsque `context_custom_metrics` est une `string`, le résultat ressemble à ceci :

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

ensuite le fragment AIQL suivant récupère la valeur de l’emplacement `duration` dans l’objet et à partir de cette valeur, il récupère deux emplacements, `duration.value` et `duration.min` (`118.0` et `110.0`, respectivement).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### range

La fonction `range()` (à ne pas confondre avec l’opérateur `range`) génère un tableau dynamique contenant une série de valeurs espacées de façon égale.

**Syntaxe**

    range(start, stop, step)

**Arguments**

* *start :* valeur du premier élément dans le tableau résultant. 
* *stop :* valeur du dernier élément dans le tableau résultant, ou valeur minimale qui est supérieure au dernier élément du tableau obtenu et dans un multiple entier de *step* à partir de *start*.
* *step :* différence entre deux éléments consécutifs du tableau.

**Exemples**

L’exemple suivant retourne `[1, 4, 7]` :

```AIQL
range(1, 8, 3)
```

L’exemple suivant retourne un tableau contenant tous les jours de l’année 2015 :

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Convertit une chaîne en une valeur dynamique.

### treepath

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

Notez que « [0] » indique la présence d’un tableau, mais ne spécifie pas l’index utilisé par un chemin spécifique.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!------HONumber=AcomDC_0309_2016-->