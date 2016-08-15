<properties 
	pageTitle="Visite guidée d’Analytics dans Application Insights | Microsoft Azure" 
	description="Courts exemples de toutes les requêtes principales dans Analytics, outil de recherche puissant d’Application Insights." 
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
	ms.date="07/29/2016" 
	ms.author="awills"/>


 
# Visite guidée d’Analytics dans Application Insights


[Analytics](app-insights-analytics.md) est la fonctionnalité de recherche performante d’[Application Insights](app-insights-overview.md). Ces pages décrivent le langage de requête Analytics.


* **[Regardez la vidéo d’introduction](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.


Pour vous aider à démarrer, examinons certaines requêtes de base.

## Connectez-vous à vos données Application Insights

Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights :

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-insights-analytics-tour/001.png)

	
## [Take](app-insights-analytics-reference.md#take-operator) : afficher n lignes

Les points de données qui consignent les opérations des utilisateurs (généralement des requêtes HTTP reçues par votre application web) sont stockés dans une table appelée `requests`. Chaque ligne est un point de données de télémétrie provenant du Kit de développement logiciel (SDK) Application Insights dans votre application.

Commençons par examiner quelques exemples de lignes de la table :

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Placez le curseur quelque part dans l’instruction avant de cliquer sur OK. Vous pouvez répartir une instruction sur plusieurs lignes, mais ne placez pas de lignes vides dans une instruction. Pour conserver plusieurs requêtes dans la fenêtre, séparez-les par des lignes vides.


Choisissez les colonnes et ajustez leurs positions :

![Cliquez sur le sélecteur de colonnes en haut à droite des résultats](./media/app-insights-analytics-tour/030.png)


Développez un élément pour afficher les détails :
 
![Choisissez la vue de table et utilisez Configurer les colonnes](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Cliquez sur l’en-tête d’une colonne pour trier à nouveau les résultats disponibles dans le navigateur web. Sachez toutefois que, pour un jeu de résultats volumineux, le système limite le nombre de lignes téléchargées vers le navigateur. N’oubliez pas que cette méthode de tri n’affiche pas toujours réellement les éléments les plus grands ou les plus petits. Pour ce faire, vous devez utiliser l’opérateur `top` ou `sort`.

## [Top](app-insights-analytics-reference.md#top-operator) et [sort](app-insights-analytics-reference.md#sort-operator)

`take` est utile pour obtenir un exemple rapide d’un résultat, mais il n’affiche pas les lignes de la table dans un ordre particulier. Pour obtenir un affichage ordonné, utilisez `top` (pour un échantillon) ou `sort` (qui porte sur la table entière).

Afficher les n premières lignes, classées par une colonne particulière :

```AIQL

	requests | top 10 by timestamp desc 
```

* *Syntaxe :* la plupart des opérateurs ont des paramètres de mot clé comme `by`.
* `desc` = ordre décroissant, `asc` = ordre croissant.

![](./media/app-insights-analytics-tour/260.png)

`top...` est une façon plus performante d’exprimer `sort ... | take...`. Nous aurions pu écrire :

```AIQL

	requests | sort by timestamp desc | take 10
```

Le résultat serait le même, mais l’exécution de la requête serait un peu plus lente. (Vous pouvez également écrire `order`, qui est un alias de `sort`.)

Les en-têtes de colonne dans la vue de table peuvent également servir à trier les résultats sur l’écran. Mais bien sûr, si vous avez utilisé `take` ou `top` pour récupérer une partie seulement d’une table, vous devez uniquement trier de nouveau les enregistrements que vous avez récupérés.


## [Project](app-insights-analytics-reference.md#project-operator) : sélectionner, renommer et calculer des colonnes

Utilisez [`project`](app-insights-analytics-reference.md#project-operator) pour choisir uniquement les colonnes qui vous intéressent :

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


Vous pouvez également renommer des colonnes et en définir de nouvelles :

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Les [noms de colonne](app-insights-analytics-reference.md#names) peuvent contenir des espaces ou des symboles s’ils sont placés entre crochets comme suit : `['...']` ou `["..."]`
* `%` est l’opérateur modulo habituel.
* `1d` (le chiffre un, suivi de la lettre d) est un littéral d’intervalle de temps qui signifie un jour. Voici d’autres littéraux d’intervalle de temps : `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrondit une valeur au multiple inférieur le plus proche de la valeur de base que vous fournissez. Ainsi, `floor(aTime, 1s)` arrondit une heure vers le bas à la seconde la plus proche.

Les [expressions](app-insights-analytics-reference.md#scalars) peuvent inclure tous les opérateurs habituels (`+`, `-`, ...), et il existe une gamme de fonctions utiles.

    

## [Extend](app-insights-analytics-reference.md#extend-operator) : calculer des colonnes

Si vous souhaitez simplement ajouter des colonnes à des colonnes existantes, utilisez [`extend`](app-insights-analytics-reference.md#extend-operator) :

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Utiliser [`extend`](app-insights-analytics-reference.md#extend-operator) est plus concis que [`project`](app-insights-analytics-reference.md#project-operator) si vous souhaitez conserver toutes les colonnes existantes.


## Accès à des objets imbriqués

Les objets imbriqués sont faciles d’accès. Par exemple, dans le flux d’exceptions, vous verrez des objets structurés comme suit :

![result](./media/app-insights-analytics-tour/520.png)

Vous pouvez les aplatir en choisissant les propriétés qui vous intéressent :

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Notez que vous devez utiliser une [conversion de type (transtypage)](app-insights-analytics-reference.md#casts) vers le type approprié.

## Mesures et propriétés personnalisées

Si votre application attache [des dimensions (propriétés) personnalisées et des mesures personnalisées](app-insights-api-custom-events-metrics.md#properties) à des événements, elles seront visibles dans les objets `customDimensions` et `customMeasurements`.


Par exemple, si votre application inclut :

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
	telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Pour extraire ces valeurs dans Analytics :

```AIQL

    customEvents
    | extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

> [AZURE.NOTE] Dans [Metrics Explorer](app-insights-metrics-explorer.md), toutes les mesures personnalisées attachées à une télémétrie, quel que soit son type, apparaissent dans le panneau de métriques avec les métriques envoyées à l’aide de `TrackMetric()`. Dans Analytics, en revanche, les mesures personnalisées restent attachées au type de télémétrie d’origine, et les métriques apparaissent dans leur propre flux `metrics`.


## [Summarize](app-insights-analytics-reference.md#summarize-operator) : agréger des groupes de lignes

`Summarize` applique une *fonction d’agrégation* spécifiée sur des groupes de lignes.

Par exemple, le temps que met votre application web à répondre à une requête est reporté dans le champ `duration`. Observons le temps de réponse moyen pour toutes les demandes :

![](./media/app-insights-analytics-tour/410.png)

Nous pouvons également séparer les résultats en requêtes de noms différents :


![](./media/app-insights-analytics-tour/420.png)

`Summarize` regroupe les points de données du flux que la clause `by` évalue équitablement. Chaque valeur de l’expression `by` (chaque nom d’opération dans l’exemple ci-dessus) génère une ligne dans la table des résultats.

Nous pouvons également regrouper les résultats par heure :

![](./media/app-insights-analytics-tour/430.png)

Notez que nous utilisons la fonction `bin` (également appelée `floor`). Si nous avions simplement utilisé `by timestamp`, chaque ligne d’entrée apparaîtrait dans un groupe individuel. Pour des valeurs scalaires continues telles que des heures ou des nombres, nous devons diviser la plage continue en un nombre gérable de valeurs discrètes, et `bin` (qui représente simplement la fonction `floor` habituelle d’arrondi à la valeur inférieure) est le plus simple moyen d’y parvenir.

Nous pouvons utiliser la même technique pour réduire les plages de chaînes :


![](./media/app-insights-analytics-tour/440.png)

Notez que vous pouvez utiliser `name=` pour définir le nom d’une colonne de résultat, dans les expressions d’agrégation ou dans la clause by.

## Décompte des données échantillonnées

`sum(itemCount)` est l’agrégation recommandée pour le comptage des événements. Dans de nombreux cas, étant donné que itemCount==1, la fonction compte simplement le nombre de lignes dans le groupe. En revanche, quand [l’échantillonnage](app-insights-sampling.md) est en cours, seule une fraction des événements d’origine est conservée comme point de données dans Application Insights. Ainsi, pour chaque point de données que vous voyez, il existe `itemCount` événements.

Par exemple, si l’échantillonnage ignore 75 % des événements d’origine, itemCount == 4 dans les enregistrements conservés. Autrement dit, pour chaque enregistrement conservé, il existe quatre enregistrements d’origine.

L’échantillonnage adaptatif rend la valeur itemCount supérieure lors des périodes pendant lesquelles votre application est utilisée de façon intensive.

Par conséquent, le fait de résumer itemCount donne une bonne estimation du nombre d’événements d’origine.


![](./media/app-insights-analytics-tour/510.png)

Il existe également une agrégation `count()` (et une opération de comptage), pour les cas où vous souhaitez réellement compter le nombre de lignes dans un groupe.


Il existe toute une gamme de [fonctions d’agrégation](app-insights-analytics-reference.md#aggregations).


## Affichage des résultats dans un graphique


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

Par défaut, les résultats sont affichés sous forme de table :

![](./media/app-insights-analytics-tour/225.png)


Nous pouvons aller au-delà de la vue de table. Examinons les résultats dans la vue graphique avec l’option barres verticales :

![Cliquez sur la vue graphique, puis choisissez le graphique à barres verticales et affectez les axes x et y](./media/app-insights-analytics-tour/230.png)

Bien que nous n’ayons pas trié les résultats par heure (comme le montre l’affichage de table), le graphique affiche toujours les dates dans l’ordre approprié.


## [Where](app-insights-analytics-reference.md#where-operator) : filtrage sur une condition

Si vous avez configuré la surveillance Application Insights pour les côtés [client](app-insights-javascript.md) et serveur de votre application, certaines données télémétriques de la base de données proviennent de navigateurs.

Examinons uniquement les exceptions signalées à partir des navigateurs :

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

L’opérateur `where` utilise une expression booléenne. Voici quelques points clés les concernant :

 * `and`, `or` : opérateurs booléens
 * `==`, `<>` : égal à et différent de
 * `=~`, `!=` : chaîne ne respectant pas la casse (égal à et différent de). Il existe de nombreux autres opérateurs de comparaison de chaîne.

Consultez l’ensemble des informations sur les [expressions scalaires](app-insights-analytics-reference.md#scalars).

### Filtrage des événements

Rechercher les requêtes ayant échoué :

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` présente le type chaîne. Nous devons donc en [convertir le type](app-insights-analytics-reference.md#casts) pour effectuer une comparaison numérique.

Résumer les différentes réponses :

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Graphiques temporels

Afficher le nombre d’événements qui se produisent chaque jour :

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Sélectionnez l’option d’affichage graphique :

![Graphique temporel](./media/app-insights-analytics-tour/080.png)

L’axe des x des graphiques en courbes doit être de type DateTime.

## Séries multiples 

Utilisez plusieurs valeurs dans une clause `summarize by` afin de créer une ligne distincte pour chaque combinaison de valeurs :

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Pour afficher plusieurs lignes d’un graphique, cliquez sur **Répartir par** et choisissez une colonne.

![](./media/app-insights-analytics-tour/100.png)



## Cycle moyen quotidien

Dans quelle mesure l’utilisation varie-t-elle dans un jour moyen ?

Compter les requêtes en prenant un jour comme modulo de temps et en fractionnant le résultat par heure :

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Graphique en courbes des heures dans une journée moyenne](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Nous devons convertir les durées en dates pour afficher les résultats sur un graphique.


## Comparer plusieurs séries quotidiennes

Dans quelle mesure l’utilisation varie-t-elle au fil de la journée dans différents États ?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Répartir le graphique par État :

![Répartition par client\_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## Tracer une distribution

Combien existe-t-il de sessions de différentes longueurs ?

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

La dernière ligne est nécessaire pour la conversion en valeurs datetime : actuellement, l’axe des x d’un graphique en courbes ne peut être que de type datetime.

La clause `where` exclut les sessions à déclenchement unique (sessionDuration==0) et définit la longueur de l’axe X.


![](./media/app-insights-analytics-tour/290.png)



## [Centiles](app-insights-analytics-reference.md#percentiles)

Quelles sont les plages de durées qui couvrent différents pourcentages de sessions ?

Utilisez la requête ci-dessus, mais remplacez la dernière ligne :

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

Nous avons également supprimé la limite supérieure dans la clause where, afin d’obtenir des chiffres corrects englobant toutes les sessions avec plusieurs requêtes :

![result](./media/app-insights-analytics-tour/180.png)

Nous pouvons voir que :

* 5 % des sessions durent moins de 3 minutes 34 s ;
* 50 % des sessions durent moins de 36 minutes ;
* 5 % des sessions durent plus de 7 jours.

Pour obtenir une répartition distincte pour chaque pays, il suffit simplement d’intégrer la colonne client\_CountryOrRegion séparément par le biais des deux opérateurs summarize :

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)


## [Join](app-insights-analytics-reference.md#join)

Nous avons accès à plusieurs tables, y compris les demandes et les exceptions.

Pour rechercher les exceptions liées à une requête qui a renvoyé une réponse d’échec, nous pouvons joindre les tables sur `session_Id` :

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Avant d’effectuer la jointure, nous pouvons utiliser `project` pour sélectionner uniquement les colonnes dont nous avons besoin. Dans les mêmes clauses, nous renommons la colonne timestamp.



## [Let](app-insights-analytics-reference.md#let-clause) : affecter un résultat à une variable

Utilisez [let](./app-insights-analytics-reference.md#let-statements) pour séparer les parties de l’expression précédente. Les résultats sont identiques :

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Conseil : dans le client Analytics, ne placez pas de lignes vides entre les différentes parties de l’expression. Exécutez-la en totalité.


* **[Testez la version d’évaluation d’Analytics sur nos données simulées](https://analytics.applicationinsights.io/demo)** si votre application n’envoie pas encore de données à Application Insights.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0803_2016-->