---
title: "Référence de recherche Azure Log Analytics | Microsoft Docs"
description: "La référence de recherche Log Analytics décrit le langage de recherche et fournit la syntaxe de requête générale que vous pouvez utiliser lorsque vous recherchez des données et que vous filtrez des expressions pour affiner votre recherche."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: f819992125f77897545ce3194870b1eadf400852
ms.lasthandoff: 04/07/2017


---
# <a name="log-analytics-search-reference"></a>Référence de recherche Log Analytics
La section d’informations de référence suivante sur le langage de recherche décrit les options de syntaxe de requête générales que vous pouvez utiliser lorsque vous recherchez des données et que vous filtrez des expressions pour affiner votre recherche. Elle décrit également les commandes que vous pouvez utiliser pour effectuer une opération sur les données récupérées.

Vous pouvez consulter la section [Référence de champ et de facette de recherche](#search-field-and-facet-reference) pour en savoir plus sur les champs retournés dans les recherches et sur les facettes qui vous aident à explorer des catégories de données similaires.

## <a name="general-query-syntax"></a>Syntaxe de requête générale
La syntaxe d’interrogation générale est la suivante :

```
filterExpression | command1 | command2 …
```

L’expression de filtre (`filterExpression`) définit la condition « where » pour la requête. Les commandes s'appliquent aux résultats retournés par la requête. Plusieurs commandes doivent être séparées par la barre verticale (|).

### <a name="general-syntax-examples"></a>Exemples de syntaxe générale
Exemples :

```
system
```

Cette requête retourne les résultats qui contiennent le mot *système* dans un champ qui a été indexé pour la recherche de termes ou en texte intégral.

> [!NOTE]
> Tous les champs ne sont pas indexés de cette façon, mais les champs textuels les plus courants (tels que les descriptions et les noms) le sont généralement.
>
>

```
system error
```

Cette requête retourne des résultats qui contiennent les mots *système* et *erreur*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

Cette requête retourne des résultats qui contiennent les mots *système* et *erreur*. Elle trie ensuite les résultats en fonction du champ *ManagementGroupName* (dans l’ordre croissant), puis du champ *TimeGenerated* (dans l’ordre décroissant). Seuls les 10 premiers résultats sont nécessaires.

> [!IMPORTANT]
> Tous les noms de champ et les valeurs pour les champs de texte et de chaîne respectent la casse.
>
>

## <a name="filter-expressions"></a>Expressions de filtre
Les sous-sections suivantes expliquent les expressions de filtre.

### <a name="string-literals"></a>Littéraux de chaîne
Un littéral de chaîne est une chaîne qui n'est pas reconnue par l'analyseur comme un mot clé ou un type de données prédéfini (par exemple un nombre ou une date).

Exemples :

```
These all are string literals
```

Cette requête recherche des résultats qui contiennent les occurrences des cinq mots. Pour effectuer une recherche de chaîne complexe, placez le littéral de chaîne entre guillemets. Par exemple :

```
"Windows Server"
```

Cela renvoie uniquement les résultats avec des correspondances exactes pour *Windows Server*.

### <a name="numbers"></a>Nombres
L'analyseur prend en charge l'entier décimal et une syntaxe de nombre à virgule flottante pour les champs numériques.

Exemples :

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>Dates et heures
Chaque élément de données dans le système a une propriété *TimeGenerated* qui représente la date et l'heure d’origine de l'enregistrement. Certains types de données peuvent avoir davantage de champs Date/Heure (par exemple, *LastModified*).

Le sélecteur de **graphique/heure** de chronologie dans Azure Log Analytics montre une répartition des résultats au fil du temps (en fonction de la requête en cours d’exécution). Elle repose sur le champ *TimeGenerated*. Les champs Date/Heure ont un format de chaîne spécifique qui peut être utilisé dans des requêtes pour limiter ces dernières à une période particulière. Vous pouvez également utiliser la syntaxe pour faire référence à des intervalles de temps relatifs (par exemple, « entre il y a 3 jours et il y a 2 heures »).

Les éléments suivants sont des formes de syntaxe valides pour les dates et les heures :

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Par exemple :

```
TimeGenerated:2013-10-01T12:20
```

La commande précédente retourne uniquement les enregistrements avec une valeur *TimeGenerated* indiquant exactement 12:20 le 1er octobre 2013.

L'analyseur prend également en charge la valeur Date/Heure mnémonique NOW. (Il est peu probable que cela génère des résultats, car les données ne traversent pas le système si rapidement.)

Ces exemples sont des blocs de construction à utiliser pour des dates relatives et absolues. Dans les trois sous-sections suivantes, vous découvrirez comment les utiliser dans des filtres plus avancés avec des exemples qui utilisent des plages de dates relatives.

### <a name="datetime-math"></a>Mathématique de Date/Heure
Utilisez les opérateurs mathématiques de Date/Heure pour décaler ou arrondir la valeur de Date/Heure en utilisant de simples calculs de Date/Heure.

Syntaxe :

```
datetime/unit
```

```
datetime[+|-]count unit
```

| Opérateur  | Description |
| --- | --- |
| / |Arrondit la valeur Date/Heure à l'unité spécifiée. Par exemple, NOW/DAY arrondit la valeur Date/Heure actuelle à minuit pour le jour en cours. |
| + ou - |Décale la valeur Date/Heure du nombre d’unités spécifié. Par exemple, NOW+1HOUR avance la valeur Date/Heure actuelle d’une heure. 2013-10-01T12:00-10DAYS recule la valeur Date de 10 jours. |

Vous pouvez chaîner les opérateurs mathématiques Date/Heure. Par exemple :

```
NOW+1HOUR-10MONTHS/MINUTE
```

Le tableau suivant répertorie les unités Date/Heure prises en charge.

| Unité Date/Heure | Description |
| --- | --- |
| YEAR, YEARS |Arrondit à l'année en cours ou décale du nombre d'années spécifié. |
| MONTH, MONTHS |Arrondit au mois en cours ou décale du nombre de mois spécifié. |
| DAY, DAYS, DATE |Arrondit au jour du mois en cours ou décale du nombre de jours spécifié. |
| HOUR, HOURS |Arrondit à l'heure en cours ou décale du nombre d'heures spécifié. |
| MINUTE, MINUTES |Arrondit à la minute en cours ou décale du nombre de minutes spécifié. |
| SECOND, SECONDS |Arrondit à la seconde en cours ou décale du nombre de secondes spécifié. |
| MILLISECOND, MILLISECONDS, MILLI, MILLIS |Arrondit à la milliseconde en cours ou décale du nombre de millisecondes spécifié. |

### <a name="field-facets"></a>Facettes de champ
À l’aide des facettes de champ, vous pouvez spécifier la condition de recherche pour des champs spécifiques et leurs valeurs exactes. Cela diffère de l’écriture de requêtes de « texte libre » pour différents termes dans l’index. Vous avez déjà vu cette technique dans plusieurs des exemples précédents. Voici des exemples plus complexes.

**Syntaxe**

```
field:value
```

```
field=value
```

**Description**

Recherche la valeur spécifique dans le champ. La valeur peut être un littéral de chaîne, un nombre ou une Date/Heure.

Par exemple :

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Syntaxe**

*champ>valeur*

*champ<valeur*

*champ>=valeur*

*champ<=valeur*

*champ!=valeur*

**Description**

Fournit des comparaisons.

Par exemple :

```
TimeGenerated>NOW+2HOURS
```

**Syntaxe**

```
field:[from..to]
```

**Description**

Fournit des facettes de plage.

Par exemple :

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>IN
Le mot-clé **IN** vous permet de sélectionner un élément dans une liste de valeurs. En fonction de la syntaxe utilisée, il peut s’agir d’une liste simple de valeurs que vous fournissez ou d’une liste de valeurs provenant d’une agrégation.

Syntaxe 1 :

```
field IN {value1,value2,value3,...}
```

Cette syntaxe vous permet d’inclure toutes les valeurs dans une liste simple.



Exemples :

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

Syntaxe 2 :

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Cette syntaxe vous permet de créer une agrégation. Vous pouvez ensuite transmettre la liste de valeurs à partir de cette agrégation dans une autre recherche externe (principale) qui recherche les événements avec ces valeurs. Pour cela, incluez la recherche interne dans des accolades et indiquez ses résultats en tant que valeurs possibles pour un champ dans la recherche externe à l’aide de l’opérateur IN.

Exemple de requête interne : *ordinateurs ne disposant pas actuellement des mises à jour de sécurité* avec la requête d’agrégation suivante :

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

La dernière requête portant sur *tous les événements Windows pour les ordinateurs ne disposant pas actuellement des mises à jour de sécurité* prend la forme suivante :

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contient
Le mot clé **Contient** vous permet de filtrer les enregistrements avec un champ contenant une chaîne spécifique. Il respecte la casse, fonctionne uniquement avec des champs de type chaîne et ne peut inclure de caractère d’échappement.

Syntaxe :

```
field:contains("string")
```

Exemple :

```
Type:contains("Event")
```

Cela renvoie les enregistrements avec un type contenant la chaîne « Event ». Les exemples incluent **Event**, **SecurityEvent** et **ServiceFabricOperationEvent**.



### <a name="regular-expressions"></a>Expressions régulières
Vous pouvez spécifier les conditions de recherche pour un champ avec une expression régulière en utilisant le mot clé **Regex**. Pour obtenir une description complète de la syntaxe que vous pouvez utiliser dans les expressions régulières, reportez-vous à l’article [Utilisation d’expressions régulières pour filtrer les recherches dans les journaux Log Analytics](log-analytics-log-searches-regex.md).

Syntaxe :

```
field:Regex("Regular Expression")
```

Exemple :

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>Opérateurs logiques
Les langages de requête prennent en charge les opérateurs logiques (*AND*, *OR* et *NOT*) et leurs alias de style C (*&&*, *||* et *!*). Vous pouvez utiliser des parenthèses pour regrouper ces opérateurs.

Exemples :

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

Vous pouvez omettre l'opérateur logique pour les arguments de filtre de niveau supérieur. Dans ce cas, l'opérateur AND est supposé.

| Expression de filtre | Équivalent à |
| --- | --- |
| erreur système |système AND erreur |
| système « Windows Server » OR Gravité:1 |système AND (« Windows Server » OR Gravité:1) |

### <a name="wildcarding"></a>Utilisation des caractères génériques
Le langage de requête prend en charge l’utilisation du caractère ( \* ) pour représenter un ou plusieurs caractères pour une valeur dans une requête.

Exemple :

 Recherchez tous les ordinateurs dont le nom contient « SQL », par exemple « Redmond-SQL ».

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> Pour l’instant, les caractères génériques ne peuvent pas être utilisés entre guillemets. Par exemple, le message `"*This text*"` considère (\*) comme un caractère (\*) littéral.


## <a name="commands"></a>Commandes


Les commandes s'appliquent aux résultats qui sont retournés par la requête. Utilisez la barre verticale (|) pour appliquer une commande aux résultats récupérés. Les commandes multiples doivent être séparées par la barre verticale (|).

> [!NOTE]
> Les noms de commande peuvent être écrits en majuscules ou en minuscules, contrairement aux noms de champ et aux données.
>
>

### <a name="sort"></a>Trier
Syntaxe :

    sort field1 asc|desc, field2 asc|desc, …

Trie les résultats en fonction de champs particuliers. Le suffixe asc/desc pour trier les résultats dans l’ordre croissant ou décroissant est facultatif. S’il est omis, l’ordre de tri *asc* est supposé. Pour le champ **TimeGenerated**, l’ordre de tri *desc* est supposé, ce qui renvoie les résultats les plus récents en premier par défaut.

### <a name="toplimit"></a>Top/Limit
Syntaxe :

    top number


    limit number
Limite la réponse aux N premiers résultats.

Exemple :

    Type:Alert errors detected | top 10

Retourne les 10 premiers résultats de correspondance.

### <a name="skip"></a>Skip
Syntaxe :

    skip number

Ignore le nombre de résultats répertoriés.

Exemple :

    Type:Alert errors detected | top 10 | skip 200

Retourne les 10 premiers résultats correspondants en commençant au résultat 200.

### <a name="select"></a>Sélectionnez
Syntaxe :

    select field1, field2, ...

Limite les résultats aux champs que vous choisissez.

Exemple :

    Type:Alert errors detected | select Name, Severity

Limite les champs de résultats retournés à *Name* and *Severity*pour en savoir plus sur les champs retournés dans les recherches et sur les facettes qui vous aident à explorer des catégories de données similaires.

### <a name="measure"></a>Measure
La commande *measure* est utilisée pour appliquer des fonctions statistiques pour les résultats de recherche bruts. Cela est très utile pour obtenir un affichage *groupé* des données. Lorsque vous utilisez la commande measure, Log Analytics affiche une table contenant des résultats agrégés.

**Syntaxe :**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Agrège les résultats par valeur de *groupField*, puis calcule les valeurs de measure agrégées à l’aide de *aggregatedField*.

| Fonction statistique de mesure | Description |
| --- | --- |
| *aggregateFunction* |Nom de la fonction d’agrégation (non sensible à la casse). Les fonctions d’agrégation suivantes sont prises en charge : COUNT, MAX, MIN, SUM, AVG, STDDEV, COUNTDISTINCT, PERCENTILE## ou PCT## (## étant un nombre compris entre 1 et 99). |
| *aggregatedField* |Champ en cours d’agrégation. Ce champ est facultatif pour la fonction d’agrégation COUNT, mais il doit s’agir d’un champ numérique existant pour les fonctions SUM, MAX, MIN, AVG, STDDEV, PERCENTILE## ou PCT## (## étant un nombre compris entre 1 et 99). aggregatedField peut être également n’importe laquelle des fonctions **Extend** prises en charge. |
| *fieldAlias* |Alias (facultatif) de la valeur agrégée calculée. S’il n’est pas spécifié, le nom du champ sera **AggregatedValue**. |
| *groupField* |Nom du champ qui regroupe le jeu de résultats. |
| *Intervalle* |Intervalle de temps, au format **nnnNAME**. **nnn** est le nombre entier positif. **NAME** est le nom de l’intervalle. Les noms d’intervalle pris en charge sont sensibles à la casse et incluent : MILLISECOND[S], SECOND[S], MINUTE[S], HOUR[S], DAY[S], MONTH[S] et YEAR[S]. |

L'option d'intervalle ne peut être utilisée que dans des champs de groupe Date/Heure (tels que *TimeGenerated* and *TimeCreated*). Actuellement, elle n’est pas appliquée par le service, mais un champ sans la valeur Date/Heure qui est transmis au serveur principal entraîne une erreur d’exécution. Lorsque le schéma de validation est implémenté, l’API de service rejette les requêtes qui utilisent des champs sans la valeur Date/Heure pour l’agrégation d’intervalles. L’implémentation actuelle de *Measure* prend en charge le regroupement d’intervalles pour n’importe quelle fonction d’agrégation.

Si la clause BY est omise alors qu’un intervalle est spécifié (comme seconde syntaxe), le champ *TimeGenerated* est supposé par défaut.

Exemples :

**Exemple 1**

    Type:Alert | measure count() as Count by ObjectId

Regroupe les alertes par *ObjectID* et calcule le nombre d’alertes pour chaque groupe. La valeur d'agrégation est retournée en tant que champ (alias) *Count* .

**Exemple 2**

    Type:Alert | measure count() interval 1HOUR

Groupe les alertes par intervalles d'une heure à l'aide du champ *TimeGenerated* et retourne le nombre d'alertes dans chaque intervalle.

**Exemple 3**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

Comme pour l'exemple précédent, mais avec un alias de champ agrégé (*AlertsPerHour*).

**Exemple 4**

    * | measure count() by TimeCreated interval 5DAYS

Groupe les résultats par intervalles de 5 jours à l'aide du champ *TimeCreated* et retourne le nombre de résultats dans chaque intervalle.

**Exemple 5**

    Type:Alert | measure max(Severity) by WorkflowName

Groupe les alertes par nom de la charge de travail et retourne la valeur de gravité d'alerte maximum pour chaque flux de travail.

**Exemple 6**

    Type:Alert | measure min(Severity) by WorkflowName

Identique à l’exemple précédent, mais avec la fonction agrégée *min*.

**Exemple 7**

    Type:Perf | measure avg(CounterValue) by Computer

Regroupe les performances par ordinateur et calcule la moyenne (avg).

**Exemple 8**

    Type:Perf | measure sum(CounterValue) by Computer

Identique à l’exemple précédent, mais utilise *sum*.

**Exemple 9**

    Type:Perf | measure stddev(CounterValue) by Computer

Identique à l’exemple précédent, mais utilise *stddev*.

**Exemple 10**

    Type:Perf | measure percentile70(CounterValue) by Computer

Identique à l’exemple précédent, mais utilise *percentile70*.

**Exemple 11**

    Type:Perf | measure pct70(CounterValue) by Computer

Identique à l’exemple précédent, mais utilise *pct70*. Notez que *PCT##* est uniquement un alias de la fonction *PERCENTILE##*.

**Exemple 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

Regroupe Perf d’abord par ordinateur, puis CounterName, puis calcule la moyenne (avg).

**Exemple 13**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

Obtient les cinq premiers flux de travail avec le nombre maximal d'alertes.

**Exemple 14**

    * | measure countdistinct(Computer) by Type

Compte le nombre d’ordinateurs uniques générant des rapports pour chaque Type.

**Exemple 15**

    * | measure countdistinct(Computer) Interval 1HOUR

Compte le nombre d’ordinateurs uniques générant des rapports pour chaque heure.

**Exemple 16**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

Regroupe le % de temps processeur par ordinateur et retourne la moyenne pour chaque heure.

**Exemple 17**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

Groupe W3CIISLog par méthode et retourne la valeur maximale pour chaque tranche de 5 minutes.

**Exemple 18**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

Regroupe le % de temps processeur par ordinateur et retourne la valeur minimale, la moyenne, le 75e centile et la valeur maximale pour chaque heure.

**Exemple 19**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

Regroupe le % de temps processeur d’abord par ordinateur, puis par nom d’instance, puis retourne la valeur minimale, la moyenne, le 75e centile et la valeur maximale pour chaque heure.

**Exemple 20**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

Calcule le nombre maximal d’écritures disque par minute pour chaque disque sur votre ordinateur.

### <a name="where"></a>Where
Syntaxe :

```
**where** AggregatedValue>20
```

Peut être utilisée uniquement après une commande *Measure* pour filtrer davantage les résultats agrégés que la fonction d’agrégation *Measure* a produits.

Exemples :

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>Dedup
Syntaxe :

    Dedup FieldName

Renvoie le premier document trouvé pour chaque valeur unique du champ donné.

Exemple :

    Type=Event | Dedup EventID | sort TimeGenerated DESC

Cet exemple renvoie un événement (le dernier événement) par EventID.


### <a name="extend"></a>Extend
Vous permet de créer des champs d’exécution dans les requêtes. Vous pouvez également utiliser la commande measure après la commande extend si vous souhaitez effectuer l’agrégation.

**Exemple 1**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Affiche le score de recommandation pondéré pour les recommandations d’évaluation SQL.

**Exemple 2**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
Affiche la valeur de compteur en Ko au lieu d’octets.

**Exemple 3**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Met à l’échelle la valeur de WireData TotalBytes, de sorte que tous les résultats soient compris entre 0 et 100.

**Exemple 4**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
Les valeurs Tag Perf Counter inférieures à 50 % sont définies comme LOW, et les autres comme HIGH.

**Exemple 5**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Calcule le nombre maximal d’écritures disque par minute pour chaque disque sur votre ordinateur.

**Fonctions prises en charge**

| Fonction | Description | Exemples de syntaxe |
| --- | --- | --- |
| abs |Retourne la valeur absolue de la valeur ou de la fonction spécifiée. |`abs(x)` <br> `abs(-5)` |
| acos |Retourne l’arc cosinus d’une valeur ou d’une fonction. |`acos(x)` |
| and |Retourne la valeur true si et seulement si tous les opérandes ont la valeur true. |`and(not(exists(popularity)),exists(price))` |
| asin |Retourne l’arc sinus d’une valeur ou d’une fonction. |`asin(x)` |
| atan |Retourne l’arc tangente d’une valeur ou d’une fonction. |`atan(x)` |
| atan2 |Retourne l’angle résultant de la conversion de coordonnées rectangulaires x,y en coordonnées polaires. |`atan2(x,y)` |
| cbrt |Racine cubique. |`cbrt(x)` |
| ceil |Arrondit à un entier. |`ceil(x)`  <br> `ceil(5.6)` Retourne 6 |
| cos |Retourne le cosinus d’un angle. |`cos(x)` |
| cosh |Retourne le cosinus hyperbolique d’un angle. |`cosh(x)` |
| def |Abréviation de valeur par défaut. Retourne la valeur du champ « field ». Si le champ n’existe pas, renvoie la valeur par défaut spécifiée et retourne la première valeur où : `exists()==true`. |`def(rating,5)`. Cette fonction def() retourne l’évaluation ou, si aucune évaluation n’est spécifiée dans le document, retourne 5. <br> `def(myfield, 1.0)` équivaut à `if(exists(myfield),myfield,1.0)`. |
| deg |Convertit les radians en degrés. |`deg(x)` |
| div |`div(x,y)` divise x par y. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| dist |Retourne la distance entre deux vecteurs, (points) dans un espace à n dimensions. Prend la puissance, ainsi que deux instances ValueSource ou plus, et calcule les distances entre les deux vecteurs. Chaque ValueSource doit être un nombre. Un nombre pair d’instances ValueSource doit être transféré et la méthode suppose que la première moitié représente le premier vecteur et que la seconde moitié représente le second vecteur. |`dist(2, x, y, 0, 0)` Calcule la distance euclidienne entre (0,0,0) et (x,y) pour chaque document. <br> `dist(1, x, y, 0, 0)` Calcule la distance de Manhattan (taxi) entre (0,0) et (x,y) pour chaque document. <br> `dist(2,,x,y,z,0,0,0)` Calcule la distance euclidienne entre (0,0,0) et (x,y,z) pour chaque document.<br>`dist(1,x,y,z,e,f,g)` Calcule la distance de Manhattan entre (x,y,z) et (e,f,g), où chaque lettre est un nom de champ. |
| exists |Retourne TRUE si un membre du champ existe. |`exists(author)` Retourne TRUE pour tout document contenant une valeur dans le champ « author ».<br>`exists(query(price:5.00))` Retourne TRUE si la valeur de « price » est égale à « 5.00 ». |
| exp |Retourne le nombre d’Euler élevé à la puissance x. |`exp(x)` |
| floor |Arrondit à l’entier inférieur. |`floor(x)`  <br> `floor(5.6)` Retourne 5 |
| hypo |Retourne Sqrt(sum(pow(x,2),pow(y,2))) sans dépassement positif ou négatif intermédiaire. |`hypo(x,y)`  <br> ` |
| if |Autorise les requêtes de fonction conditionnelle. Dans `if(test,value1,value2)`, test est ou fait référence à une valeur logique ou à une expression qui retourne une valeur logique (TRUE ou FALSE). `value1` est la valeur retournée par la fonction si le test renvoie la valeur TRUE. `value2` est la valeur retournée par la fonction si le test renvoie la valeur FALSE. Une expression peut être n’importe quelle fonction qui génère des valeurs booléennes. Elle peut également être une fonction qui retourne des valeurs numériques, auquel cas la valeur 0 est interprétée comme false, ou qui retourne des chaînes, auquel cas une chaîne vide est interprétée comme false. |`if(termfreq(cat,'electronics'),popularity,42)` Cette fonction contrôle chaque document pour vérifier s’il contient le terme « electronics » dans le champ cat. Si tel est le cas, la valeur du champ popularity est renvoyée. Sinon, la valeur 42 est retournée. |
| linear |Implémente `m*x+c`, où m et c sont des constantes et x est une fonction arbitraire. Cette requête équivaut à `sum(product(m,x),c)`, mais est un peu plus efficace puisqu’elle est implémentée comme une seule fonction. |`linear(x,m,c) linear(x,2,4)` retourne `2*x+4` |
| ln |Retourne le logarithme naturel de la fonction spécifiée. |`ln(x)` |
| log |Retourne la base logarithmique 10 de la fonction spécifiée. |`log(x)   log(sum(x,100))` |
| map |Mappe les valeurs d’une fonction d’entrée x comprises entre min et max (inclus) à la cible spécifiée. Les arguments min et max doivent être des constantes. Les valeurs cible et par défaut des arguments peuvent être des constantes ou des fonctions. Si la valeur de x n’est pas comprise entre min et max, la valeur de x est retournée, ou une valeur par défaut est retournée si elle est spécifiée comme 5e argument. |`map(x,min,max,target) map(x,0,0,1)` Remplace toutes les valeurs de 0 par 1. Cela peut être utile pour le traitement des valeurs par défaut 0.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)` Remplace toutes les valeurs comprises entre 0 et 100 par 1, et toutes les autres valeurs par -1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))` Remplace toutes les valeurs comprises entre 0 et 100 par x+599 et toutes les autres valeurs par la fréquence du terme « solr » dans le texte du champ. |
| max |Retourne la valeur numérique maximale de plusieurs fonctions ou constantes imbriquées, lesquelles sont spécifiées en tant qu’arguments : `max(x,y,...)`. La fonction max peut également être utile pour réduire une autre fonction ou un autre champ à une constante spécifiée.  Utilisez la syntaxe `field(myfield,max)` pour sélectionner la valeur maximale d’un seul champ à valeurs multiples. |`max(myfield,myotherfield,0)` |
| Min |Retourne la valeur numérique minimale de plusieurs fonctions ou constantes imbriquées, lesquelles sont spécifiées en tant qu’arguments : `min(x,y,...)`. La fonction min peut également être utile pour définir une « limite supérieure » sur une fonction à l’aide d’une constante. Utilisez la syntaxe `field(myfield,min)` pour sélectionner la valeur minimale d’un seul champ à valeurs multiples. |`min(myfield,myotherfield,0)` |
| mod |Calcule le modulo de la fonction x par la fonction y. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| ms |Retourne la différence en millisecondes entre ses arguments. Les dates se rapportent à l’époque Unix ou POSIX, à minuit, le 1er janvier 1970 (UTC). Les arguments peuvent prendre le nom d’un TrieDateField indexé ou d’une mathématique de date basée sur une date constante ou sur NOW. `ms()` est équivalent à `ms(NOW)`, nombre de millisecondes depuis l’époque spécifiée. `ms(a)` : retourne le nombre de millisecondes depuis l’époque représentée par l’argument. `ms(a,b)` retourne le nombre de millisecondes avant a de la survenance de b, qui est `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| not |Valeur de négation logique de la fonction encapsulée. |`not(exists(author))` TRUE uniquement lorsque `exists(author)` a la valeur false. |
| ou |Une disjonction logique. |`or(value1,value2)` TRUE si value1 ou value2 a la valeur true. |
| pow |Élève la base spécifiée à la puissance spécifiée. `pow(x,y)` élève x à la puissance de y. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)` Identique à sqrt. |
| product |Retourne le produit de plusieurs valeurs ou fonctions spécifiées dans une liste séparée par des virgules. `mul(...)` peut également être utilisé comme alias pour cette fonction. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Exécute une fonction réciproque avec `recip(x,m,a,b)` implémentant `a/(m*x+b)`, où m, a et b sont des constantes, et x toute fonction arbitrairement complexe. Lorsque a et b sont égaux et que x>=0, cette fonction a une valeur maximale de 1 qui diminue à mesure que x augmente. Le fait d’augmenter simultanément la valeur de a et de b a pour effet de déplacer l’ensemble de la fonction vers une partie plus plane de la courbe. Ces propriétés peuvent en faire une fonction idéale pour améliorer les documents plus récents lorsque x est `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| rad |Convertit les degrés en radians. |`rad(x)` |
| rint |Arrondit à l’entier le plus proche. |`rint(x)`  <br> `rint(5.6)` Retourne 6 |
| sin |Retourne le sinus d’un angle. |`sin(x)` |
| sinh |Retourne le sinus hyperbolique d’un angle. |`sinh(x)` |
| scale |Met à l’échelle les valeurs de la fonction x de sorte qu’elles soient comprises entre les valeurs minTarget et maxTarget spécifiées (incluses). L’implémentation actuelle parcourt toutes les valeurs de la fonction pour obtenir les valeurs min et max afin de pouvoir récupérer l’échelle adéquate. L’implémentation actuelle ne peut pas déterminer lorsque des documents ont été supprimés ou lorsqu’ils ne comportent aucune valeur. Elle utilise les valeurs 0.0 dans ces cas. Cela signifie que si les valeurs sont normalement toutes supérieures à 0.0, l’une d’elles peut finir par une valeur min de 0.0 à partir de laquelle sera effectué le mappage. Dans ces cas, une fonction `map()` appropriée peut être utilisée comme solution de contournement pour remplacer 0.0 par une valeur comprise dans la plage réelle, comme indiqué ici : `scale(map(x,0,0,5),1,2)`. |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)` Met à l’échelle les valeurs de x de telle sorte que toutes les valeurs sont comprises entre 1 et 2 inclus. |
| sqrt |Retourne la racine carrée de la valeur ou fonction spécifiée. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Calcule la distance entre deux chaînes. Utilise l’interface StringDistance du vérificateur orthographique Lucene et prend en charge toutes les implémentations disponibles dans ce package. Permet également aux applications de se connecter d’elles-mêmes à l’aide des fonctionnalités de chargement de la ressource de Solr. strdist prend les valeurs `(string1, string2, distance measure)`. Les valeurs possibles pour la mesure de distance sont les suivantes :<ul><li>jw : Jaro-Winkler</li><li>edit : distance de Levenstein ou d’édition</li><li>ngram : la distance n-grammes, si elle est spécifiée, peut éventuellement exprimer la taille. La valeur par défaut est 2.</li><li>FQN : nomplet nom de la classe pour une implémentation de l’interface StringDistance. Doit avoir un constructeur non arg.</li></ul> |`strdist("SOLR",id,edit)` |
| sub |Retourne x-y à partir de `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |Retourne la somme de plusieurs valeurs ou fonctions spécifiées dans une liste séparée par des virgules. `add(...)` peut également être utilisé comme alias pour cette fonction. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |Retourne le nombre de fois où que le terme apparaît dans le champ de ce document. |termfreq(text,’memory’) |
| tan |Retourne la tangente d’un angle. |`tan(x)` |
| tanh |Retourne la tangente hyperbolique d’un angle. |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Référence de champ et de facette de recherche
Lorsque vous utilisez log Search pour rechercher des données, les résultats affichent différents champs et facettes. Certaines informations ne sont pas très descriptives. Utilisez les informations suivantes pour mieux comprendre les résultats.

| Champ | Type de recherche | Description |
| --- | --- | --- |
| TenantId |Tout |Utilisé pour partitionner les données. |
| TimeGenerated |Tout |Il est utilisé pour piloter la chronologie et des sélections d’heure (dans la recherche et dans d'autres écrans). Il indique le moment où l'élément de données a été généré (en général sur l'agent). L’heure est exprimée au format ISO et toujours en UTC. Dans le cas des types basés sur une instrumentation existante (événements dans un journal), il s’agit généralement de l’heure réelle à laquelle l’entrée/la ligne/l’enregistrement du journal a été enregistré. Pour certains des autres types produits par le biais de packs d’administration ou dans le cloud (par exemple, recommandations ou alertes), l’heure représente autre chose. Il s’agit de l’heure à laquelle ce nouvel élément de données avec capture instantanée d’une configuration quelconque a été collecté, ou à laquelle une recommandation/alerte a été générée à partir de ce dernier. |
| EventID |Événement |ID d’événement dans le journal des événements Windows. |
| EventLog |Événement |Journal des événements dans lequel l’événement a été enregistré par Windows. |
| EventLevelName |Événement |Critique, avertissement, information ou réussite |
| EventLevel |Événement |Valeur numérique indiquant le niveau d’événement : critique, avertissement, information ou réussite (utilisez la valeur EventLevelName pour des requêtes plus faciles et mieux lisibles). |
| SourceSystem |Tout |Provenance des données (en termes de mode d’attachement au service). Entre autres, on peut citer Microsoft System Center Operations Manager et le stockage Azure. |
| ObjectName |PerfHourly |Nom d’objet de performances Windows. |
| InstanceName |PerfHourly |Nom d’instance du compteur de performances Windows. |
| CounteName |PerfHourly |Nom du compteur de performances Windows. |
| ObjectDisplayName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nom d’affiche de l’objet ciblé par une règle de collecte des performances dans Operations Manager. Peut également être le nom d’affichage de l’objet découvert par Operational Insights, ou pour lequel l’alerte a été générée. |
| RootObjectName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |Nom d’affichage du parent du parent (dans une relation d’hébergement double) de l’objet ciblé par une règle de collecte des performances dans Operations Manager. Peut également être le nom d’affichage de l’objet découvert par Operational Insights, ou pour lequel l’alerte a été générée. |
| Ordinateur |La plupart des types |Nom de l’ordinateur auquel appartiennent les données. |
| DeviceName |ProtectionStatus |Nom de l’ordinateur auquel appartiennent les données (identique à « Computer »). |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |Le classement d’état de la menace est une représentation numérique de l’état de la menace. Comme pour les codes de réponse HTTP, nous avons laissé des intervalles entre les nombres (c’est pourquoi le niveau qui indique aucune menace est 150, et non 100 ou 0). Cela nous permet d’avoir de l’espace pour ajouter de nouveaux états. En cas de cumul de l’état de la menace et de l’état de la protection, l’intention consiste à montrer l’état le plus grave qu’a connu l’ordinateur sur une période sélectionnée. Les numéros permettent de classer les différents états afin que vous puissiez rechercher l’enregistrement avec le nombre le plus élevé. |
| ThreatStatus |ProtectionStatus |Description de ThreatStatus, mappe 1:1 avec ThreatStatusRank. |
| TypeofProtection |ProtectionStatus |Produit anti-programme malveillant qui est détecté sur l’ordinateur : aucun, outil de suppression de logiciels malveillants de Microsoft, Forefront, etc. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus, RequiredUpdate |ID du service de contrôle d’intégrité de l’agent de cet ordinateur. |
| HealthServiceId |La plupart des types |ID du service de contrôle d’intégrité de l’agent de cet ordinateur. |
| ManagementGroupName |La plupart des types |Nom du groupe d’administration pour les agents liés à Operations Manager. Dans le cas contraire, il sera Null/vide. |
| ObjectType |ConfigurationObject |Type (comme « type » ou « classe » dans le pack d’administration d’Operations Manager) pour cet objet découvert par l’évaluation de configuration Log Analytics. |
| UpdateTitle |RequiredUpdate |Nom de la mise à jour qui a été trouvée mais pas installée. |
| PublishDate |RequiredUpdate |Date à laquelle la mise à jour a été publiée sur Microsoft Update. |
| Serveur |RequiredUpdate |Nom de l’ordinateur auquel appartiennent les données (identique à « Computer »). |
| Produit |RequiredUpdate |Produit auquel s’applique la mise à jour. |
| UpdateClassification |RequiredUpdate |Type de mise à jour (par exemple, correctif cumulatif ou service pack). |
| KBID |RequiredUpdate |ID d’article de la base de connaissances qui décrit cette meilleure pratique ou cette mise à jour. |
| WorkflowName |ConfigurationAlert |Nom de la règle ou du moniteur qui a généré l’alerte. |
| Severity |ConfigurationAlert |Gravité de l’alerte. |
| Priorité |ConfigurationAlert |Priorité de l’alerte. |
| IsMonitorAlert |ConfigurationAlert |Cette alerte est-elle générée par une analyse (true) ou une règle (false) ? |
| AlertParameters |ConfigurationAlert |XML avec les paramètres de l’alerte Log Analytics. |
| Context |ConfigurationAlert |XML avec le contexte de l’alerte Log Analytics. |
| Charge de travail |ConfigurationAlert |Technologie ou charge de travail à laquelle l’alerte fait référence. |
| AdvisorWorkload |Recommandation |Technologie ou charge de travail à laquelle la recommandation fait référence. |
| Description |ConfigurationAlert |Description de l’alerte (en bref). |
| DaysSinceLastUpdate |UpdateAgent |Nombre de jours (par rapport à la valeur TimeGenerated de cet enregistrement) auquel remonte la dernière installation par l’agent d’une mise à jour quelconque provenant de Windows Server Update Service (WSUS) ou de Microsoft Update. |
| DaysSinceLastUpdateBucket |UpdateAgent |Catégorisation, basée sur la valeur DaysSinceLastUpdate, en intervalles de planification du temps écoulé depuis qu’un ordinateur a installé pour la dernière fois une mise à jour quelconque provenant de WSUS ou de Microsoft Update. |
| AutomaticUpdateEnabled |UpdateAgent |Activation ou désactivation de la vérification de mise à jour automatique sur cet agent. |
| AutomaticUpdateValue |UpdateAgent |Configuration de la vérification de mise à jour automatique : téléchargement et installation, téléchargement uniquement ou vérification uniquement. |
| WindowsUpdateAgentVersion |UpdateAgent |Numéro de version de l’agent Microsoft Update. |
| WSUSServer |UpdateAgent |Quel serveur WSUS cet agent de mise à jour cible-t-il ? |
| OSVersion: |UpdateAgent |Version du système d’exploitation sur laquelle l’agent de cette mise à jour est exécuté. |
| Nom |Recommendation, ConfigurationObjectProperty |Nom ou titre de la recommandation, ou nom de la propriété de l’évaluation de configuration de Log Analytics. |
| Valeur |ConfigurationObjectProperty |Valeur d’une propriété de l’évaluation de configuration de Log Analytics. |
| KBLink |Recommandation |URL menant à l’article de la base de connaissances qui décrit cette meilleure pratique ou mise à jour. |
| RecommendationStatus |Recommandation |Les recommandations font partie des quelques types dont les enregistrements sont mis à jour au lieu d’être simplement ajoutés à l’index de recherche. Cet état change si la recommandation est active/ouverte ou si Log Analytics détecte qu’elle a été résolue. |
| RenderedDescription |Événement |Description rendue (texte réutilisé avec des paramètres remplis) d’un événement Windows. |
| ParameterXml |Événement |XML avec les paramètres de la section données d’un événement Windows (comme indiqué dans l’observateur d’événements). |
| EventData |Événement |XML avec toute la section données d’un événement Windows (comme indiqué dans l’observateur d’événements). |
| Source |Événement |Source du journal des événements ayant généré l’événement. |
| EventCategory |Événement |Catégorie de l’événement, extraite directement du journal des événements Windows. |
| Nom d’utilisateur |Événement |Nom d’utilisateur de l’événement Windows (en général, NT AUTHORITY\LOCALSYSTEM). |
| SampleValue |PerfHourly |Valeur moyenne de l’agrégation de toutes les heures d’un compteur de performances. |
| Min |PerfHourly |Valeur minimale de l’intervalle horaire d’un agrégat de toutes les heures du compteur de performances. |
| max |PerfHourly |Valeur maximale de l’intervalle horaire d’un agrégat de toutes les heures du compteur de performances. |
| Percentile95 |PerfHourly |Valeur du 95e centile de l’intervalle horaire d’un agrégat de toutes les heures du compteur de performances. |
| SampleCount |PerfHourly |Nombre d’exemples de compteurs de performances bruts utilisés pour produire cet enregistrement d’agrégation toutes les heures. |
| Menace |ProtectionStatus |Nom du programme malveillant trouvé. |
| StorageAccount |W3CIISLog |Compte de stockage Azure à partir duquel le journal a été lu. |
| AzureDeploymentID |W3CIISLog |ID de déploiement Azure du service cloud auquel appartient le journal. |
| Rôle |W3CIISLog |Rôle du service cloud Azure auquel appartient le journal. |
| RoleInstance |W3CIISLog |Instance du rôle Azure auquel appartient le journal. |
| sSiteName |W3CIISLog |Site web IIS auquel appartient le journal (notation métabase) ; champ s-sitename dans le journal d’origine. |
| sComputerName |W3CIISLog |Champ s-computername dans le journal d’origine. |
| sIP |W3CIISLog |Adresse IP du serveur à laquelle la requête HTTP a été envoyée. Champ s-ip dans le journal d’origine. |
| csMethod |W3CIISLog |Méthode HTTP (par exemple, GET/POST) utilisée par le client dans la requête HTTP. Méthode cs-method dans le journal d’origine. |
| cIP |W3CIISLog |Adresse IP cliente d’où provenait la requête HTTP. Champ c-ip dans le journal d’origine. |
| csUserAgent |W3CIISLog |Agent utilisateur HTTP déclaré par le client (navigateur ou autre). Champ cs-user-agent dans le journal d’origine. |
| scStatus |W3CIISLog |Code d’état HTTP (par exemple, 200/403/500) retourné par le serveur au client. Champ cs-status dans le journal d’origine. |
| TimeTaken |W3CIISLog |La durée (en millisecondes) nécessaire à la requête pour se terminer. Champ timetaken dans le journal d’origine. |
| csUriStem |W3CIISLog |URI relatif (sans adresse d’hôte, c’est-à-dire /search) qui a été demandé. Champ cs-uristem dans le journal d’origine. |
| csUriQuery |W3CIISLog |Requête d’URI. Les requêtes d’URI sont nécessaires uniquement pour les pages dynamiques, telles que des pages ASP. Par conséquent, ce champ contient souvent un trait d’union pour les pages statiques. |
| sPort |W3CIISLog |Port du serveur auquel la demande HTTP a été envoyée (et qui est écouté par IIS puisqu’il a récupéré cette demande). |
| csUserName |W3CIISLog |Nom d’utilisateur authentifié, si la demande est authentifiée et n’est pas anonyme. |
| csVersion |W3CIISLog |Version du protocole HTTP utilisée dans la demande (par exemple, HTTP/1.1). |
| csCookie |W3CIISLog |Informations sur le cookie. |
| csReferer |W3CIISLog |Dernier site visité par l’utilisateur. Ce site a fourni un lien vers le site actuel. |
| csHost |W3CIISLog |En-tête d’hôte (par exemple, www.mysite.com) qui a été demandé. |
| scSubStatus |W3CIISLog |Code d'erreur du sous-état. |
| scWin32Status |W3CIISLog |Code d’état Windows. |
| csBytes |W3CIISLog |Octets envoyés dans la demande du client au serveur. |
| scBytes |W3CIISLog |Octets retournés dans la réponse du serveur au client. |
| ConfigChangeType |ConfigurationChange |Type de modification (par exemple, services Windows et logiciels). |
| ChangeCategory |ConfigurationChange |Catégorie de la modification (modifié, ajouté, supprimé). |
| SoftwareType |ConfigurationChange |Type de logiciel (mise à jour, application). |
| SoftwareName |ConfigurationChange |Nom du logiciel (applicable uniquement aux modifications logicielles). |
| Éditeur |ConfigurationChange |Fournisseur qui publie le logiciel (applicable uniquement aux modifications logicielles). |
| SvcChangeType |ConfigurationChange |Type de modification appliquée à un service Windows (état, type d’état, chemin d’accès, compte de service). Cela s’applique uniquement aux modifications de service Windows. |
| SvcDisplayName |ConfigurationChange |Nom d’affichage du service qui a été modifié. |
| SvcName |ConfigurationChange |Nom du service qui a été modifié. |
| SvcState |ConfigurationChange |Nouvel état (actuel) du service. |
| SvcPreviousState |ConfigurationChange |Précédent état connu du service (applicable uniquement si l’état du service a été modifié). |
| SvcStartupType |ConfigurationChange |Type de démarrage du service. |
| SvcPreviousStartupType |ConfigurationChange |Dernier type de démarrage du service (applicable uniquement si le type de démarrage du service a été modifié). |
| SvcAccount |ConfigurationChange |Compte de service. |
| SvcPreviousAccount |ConfigurationChange |Compte de service précédent (applicable uniquement si le compte de service a été modifié). |
| SvcPath |ConfigurationChange |Chemin d’accès au fichier exécutable du service Windows. |
| SvcPreviousPath |ConfigurationChange |Chemin d’accès précédent de l’exécutable du service Windows (applicable uniquement s’il a été modifié). |
| SvcDescription |ConfigurationChange |Description du service. |
| Précédent |ConfigurationChange |État précédent de ce logiciel (installé, non installé, version précédente). |
| Current |ConfigurationChange |Dernier état de ce logiciel (installé, non installé, version actuelle). |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les recherches de journal :

* Familiarisez-vous avec les [recherches de journal](log-analytics-log-searches.md) pour afficher les informations détaillées collectées par les solutions.
* Utilisez des [champs personnalisés dans Log Analytics](log-analytics-custom-fields.md) pour étendre les recherches de journal.

