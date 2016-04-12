<properties 
	pageTitle="Récapitulatif et agrégation dans Analytics au sein d’Application Insights" 
	description="Informations de référence sur les fonctions d’agrégation et l’instruction summarize dans Analytics, puissant outil de recherche d’Application Insights." 
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
	ms.date="03/30/2016" 
	ms.author="awills"/>

# Agrégation dans Analytics

[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante d’[Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]


## opérateur summarize

Génère une table qui agrège le contenu de la table d’entrée.

    purchases
    | summarize avg(Price) 
      by Fruit, Supplier

![](./media/app-insights-analytics-aggregations/01.png)

* Les enregistrements d’entrée sont collectés en groupes disposant chacun d’une combinaison spécifique de valeurs en fonction des champs `by`.
* Les expressions d’agrégation sont évaluées sur les membres de chaque groupe.
* Il existe une ligne de sortie par combinaison distincte de valeurs des expressions « by ». 
* Il existe une colonne de sortie par expression d’agrégation et par expression « by ». Toutes les autres colonnes d’entrée sont supprimées.

### Syntaxe

    T | summarize
         [  [ Column = ] Aggregation [ , ... ]]
         [ by
            [ Column = ] GroupExpression [ , ... ]]

**Arguments**

* *Column* : nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *Aggregation* : appel à une fonction d’agrégation telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Consultez la liste des fonctions d’agrégation ci-dessous.
* *GroupExpression* : expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En général, il s’agit d’un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou une fonction `bin()` avec une colonne numérique ou de temps comme argument. 

Si vous fournissez une expression numérique ou de temps sans utiliser `bin()`, Analytics l’applique automatiquement avec un intervalle de `1h` pour les heures ou de `1.0` pour les nombres.

Si vous ne fournissez pas *GroupExpression*, la table entière est résumée dans une ligne de sortie unique.

Vous devez utiliser un type simple, pas un type dynamique, dans la clause `by`. Par exemple, le transtypage `tostring` est essentiel ici :

    exceptions
	| summarize count()
      by tostring(customDimensions.ClientRequestId)

### Résumer par colonnes avec des valeurs discrètes

Requête qui affiche les temps de réponse moyens à différentes requêtes HTTP, en fonction des codes de réponse :

    requests 
    | summarize count(), avg(duration) 
      by operation_Name, resultCode

![result](./media/app-insights-analytics-aggregations/03.png)


* Les expressions d’agrégation (telles que count et avg) doivent être formées à partir de fonctions d’agrégation documentées dans cet article. Leurs arguments peuvent être des fonctions scalaires.
* Les expressions de regroupement (après « by ») peuvent être des expressions scalaires, mais elles sont plus performantes si elles sont simplement des noms de champs.

### Résumer par colonnes numériques

Pour effectuer un regroupement d’après un critère scalaire continu comme un nombre ou une heure, utilisez la fonction `bin` (également appelée `floor`) pour répartir la plage continue en emplacements.

    requests
    | summarize count() 
      by bin(duration, 1000)/1000

![result](./media/app-insights-analytics-aggregations/04.png)

(Le champ de durée de requête est un nombre en millisecondes.)
 
## Conseils

* Utilisez `where` pour supprimer toutes les lignes qui ne vous intéressent pas avant `summarize`.
 * Supprimez les valeurs NULL. Une valeur NULL dans un groupe rend le résultat de l’agrégat NULL. 

```

        requests 
        | where isnotnull(duration) 
        | summarize count(), avg(duration)
          by operation_Name
```

* Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de champ simples ou d’appliquer `bin()` à un champ numérique.





## Exemples

#### Compter les requêtes

Rechercher le nombre de requêtes de chaque nom au cours de la dernière heure :

    requests | where timestamp > ago(1h)
    | summarize req_count = sum(itemCount) by name

> [AZURE.NOTE] Au lieu de simplement compter les points de données de requête, utilisez la propriété itemCount. Cela permet de compenser tout [échantillonnage](app-insights-sampling.md) en cours. Par exemple, si l'échantillonnage est défini à 33 %, itemCount aura la valeur 3 dans chaque point de données.

#### Min et Max

Rechercher l’horodatage minimal et maximal de tous les enregistrements dans le flux de requêtes. Comme il n’y a pas de clause group by, la sortie contient une seule ligne :

```

requests | summarize Min = min(timestamp), Max = max(timestamp)
```

|`Min`|`Max`
|---|---
|`2015-12-09 09:21:45` | `2015-12-24 23:45:00`



#### Durée des sessions

Les sessions dans un journal des événements ont plusieurs événements. Déterminons le début et la fin de chaque session en recherchant les premier et dernier événements dans chacune d’elle :

```

    requests 
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) by session_Id 
    | extend duration = bin(stop - start, 1s)
```

L’opération `extend` ajoute la colonne duration, qu’elle arrondit à la seconde la plus proche à l’aide de `bin` (également appelé `floor`).

![](./media/app-insights-analytics-aggregations/minmax.png)

#### Exemple : durée moyenne

Recherchons maintenant les durées de session moyennes pour les clients dans différentes villes :

```

    requests
    | where isnotempty(session_Id)
    | summarize start=min(timestamp), stop=max(timestamp) 
      by session_Id, client_City 
    | extend duration = stop - start
    | summarize duration_by_city=bin(avg(duration),1s) by client_City
    | top 50 by duration_by_city
```

Nous avons ajouté la colonne `client_City` à la clause `by` afin qu’elle soit prise en compte dans la première opération de résumé. En supposant que tous les événements d’une même session cliente se produisent dans la même ville, le nombre de sorties du résumé reste le même.


![](./media/app-insights-analytics-aggregations/durationcity.png)


#### Exemple : 

Trouver l’heure du jour la plus chargée dans chaque ville où se trouvent des clients. Par « la plus chargée » nous entendons l’heure à laquelle le nombre maximal de sessions est démarré dans une journée moyenne.

```
requests  
| summarize start=min(timestamp) by session_Id, city=client_City 
| extend timeofday=start % 1d 
| summarize popularity=dcount(session_Id) by bin(timeofday, 1h), city 
| summarize argmax(popularity, *) by city  
| sort by max_pop_tod asc
```

## AGRÉGATIONS

## any 

    any(Expression)

Sélectionne une ligne du groupe de façon aléatoire et retourne la valeur de l’expression spécifiée.

Cette fonction est utile, par exemple, quand une colonne a un grand nombre de valeurs similaires (par exemple, une colonne « texte d’erreur ») et que vous souhaitez échantillonner cette colonne une fois en fonction d’une valeur unique de la clé de groupe composée.

**Exemple**

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a> <a name="argmax"></a>
## argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Recherche dans le groupe la ligne correspondant à la valeur maximale de *ExprToMaximize* ou à la valeur minimale et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

**Conseil** : les colonnes analysées sont automatiquement renommées. Pour vérifier que vous utilisez les noms corrects, examinez les résultats à l’aide de `take 5` avant de les transmettre à un autre opérateur.

**Exemples**

Pour chaque nom de requête, afficher à quel moment la requête la plus longue s’est produite :

    requests | summarize argmax(duration, timestamp) by name

Afficher tous les détails de la requête la plus longue, pas seulement l’horodatage :

    requests | summarize argmax(duration, *) by name


Trouver la plus petite valeur de chaque métrique, ainsi que son horodatage et d’autres données :

    metrics 
    | summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-aggregations/argmin.png)
 


## avg

    avg(Expression)

Calcule la moyenne de *Expression* dans le groupe.

## buildschema

    buildschema(DynamicExpression)

Retourne le schéma minimal qui admet toutes les valeurs de *DynamicExpression*.

Le type de la colonne de paramètre doit être `dynamic` (tableau ou conteneur de propriétés).

**Exemple**

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

**Exemple**

Supposons que la colonne d’entrée a trois valeurs dynamiques :

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Le schéma résultant serait :

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Le schéma fournit les informations suivantes :

* L’objet racine est un conteneur avec quatre propriétés nommées x, y, z et t.
* La propriété appelée « x » peut être de type « int » ou « string ».
* La propriété appelée « y » peut être de type « double », ou un autre conteneur avec une propriété appelée « w » de type « string ».
* Le mot clé ``indexer`` indique que « z » et « t » sont des tableaux.
* Chaque élément du tableau « z » est un entier ou une chaîne.
* « t » est un tableau de chaînes.
* Chaque propriété est implicitement facultative et tout tableau peut être vide.

#### Modèle de schéma

La syntaxe du schéma retourné est la suivante :

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
	Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

Le schéma s’apparente à un sous-ensemble d’annotations de type TypeScript, encodées sous la forme d’une valeur dynamique. En Typescript, l’exemple de schéma serait le suivant :

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


## count

    count([ Predicate ])

Retourne le nombre de lignes pour lesquelles *Predicate* vaut `true`. Si *Predicate* n’est pas spécifié, retourne le nombre total d’enregistrements dans le groupe.

**Conseil pour optimiser les performances** : utilisez `summarize count(filter)` à la place de `where filter | summarize count()`.

> [AZURE.NOTE] Évitez d'utiliser count() pour rechercher le nombre de demandes, d’exceptions ou autres événements qui se sont produits. Quand l’[échantillonnage](app-insights-sampling.md) est en cours, le nombre de points de données est inférieur au nombre d'événements réels. Utilisez plutôt `summarize sum(itemCount)...`. La propriété itemCount reflète le nombre d'événements originaux qui sont représentés par chaque point de données conservé.
   

## dcount

    dcount( Expression [ ,  Accuracy ])

Retourne une estimation du nombre de valeurs distinctes de *Expr* dans le groupe. (Pour afficher les valeurs distinctes, utilisez [`makeset`](#makeset).)

Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.

 * `0` : calcul le moins précis, mais le plus rapide.
 * `1` (valeur par défaut) : meilleur compromis entre précision et vitesse de calcul (environ 0,8 % d’erreur).
 * `2` : calcul le plus précis, mais le plus lent (environ 0,4 % d’erreur).

**Exemple**

    pageViews 
    | summarize countries=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/dcount.png)

## makelist

    makelist(Expr [ ,  MaxListSize ] )

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.

* *MaxListSize* est une limite de nombre entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *128*).

## makeset

    makeset(Expression [ , MaxSetSize ] )

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes que *Expr* prend dans le groupe. (Conseil : pour comptabiliser uniquement les valeurs distinctes, utilisez [`dcount`](#dcount).)
  
*  *MaxSetSize* est une limite de nombre entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *128*).

**Exemple**

    pageViews 
    | summarize countries=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-aggregations/makeset.png)

Consultez aussi l’[opérateur `mvexpand`](app-insights-analytics-queries.md#mvexpand-operator) pour la fonction inverse.


## max, min

    max(Expr)

Calcule la valeur maximale de *Expr*.
    
    min(Expr)

Calcule la valeur minimale de *Expr*.

**Conseil** : ces fonctions ne donnent que des valeurs maximale ou minimale, par exemple, le prix le plus élevé ou le plus bas. Pour que la ligne contienne d’autres colonnes, par exemple, le nom du fournisseur proposant le prix le plus bas, utilisez [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a> <a name="percentiles"></a>
## percentile, percentiles

    percentile(Expression, Percentile)

Retourne une estimation de *Expression* du centile spécifié dans le groupe. La précision dépend de la densité de population dans la région du centile.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ] )

Similaire à `percentile()`, mais calcule un nombre de valeurs de centile (opération plus rapide que le calcul de chaque centile individuellement).

**Exemples**


Pour chaque nom de requête, valeur de `duration` supérieure à 95 % du jeu d’échantillon et inférieure à 5 % du jeu d’échantillon :

    request 
    | summarize percentile(duration, 95)
      by name

Omettez « by... » pour calculer la table entière.

Calculer simultanément plusieurs centiles pour différents noms de requête :

    
    requests 
    | summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-aggregations/percentiles.png)

Les résultats montrent que pour la requête /Events/Index, 5 % des requêtes reçoivent une réponse en moins de 2,44 s, la moitié en 3,52 s et 5 % en plus de 6,85 s.


Calculer plusieurs statistiques :

    requests 
    | summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### Erreur d’estimation dans les centiles

L’agrégation de centiles fournit une valeur approximative au moyen de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

Voici quelques points importants :

* Les limites de l’erreur d’estimation dépendent de la valeur du centile demandé. Les extrémités de l’échelle [0..100] offrent la meilleure précision, les centiles 0 et 100 étant les valeurs minimale et maximale exactes de la distribution. La précision diminue progressivement vers le milieu de l’échelle. Elle est la plus dégradée à la valeur médiane et est limitée à 1 %. 
* Les limites d’erreur sont observées sur le classement, et non sur la valeur. Supposons que percentile(X, 50) retourne la valeur Xm. L’estimation garantit qu’au moins 49 % et au plus 51 % des valeurs de X sont inférieures à Xm. Il n’existe aucune limite théorique quant à la différence entre Xm et la valeur médiane réelle de X.

## stdev

     stdev(Expr)

Retourne l’écart type de *Expr* sur le groupe.

## variance

    variance(Expr)

Retourne la variance de *Expr* sur le groupe.

## sum

    sum(Expr)

Retourne la somme de *Expr* sur le groupe.




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->