<properties 
	pageTitle="Visite guidée d’Application Insights Analytics" 
	description="Courts exemples de toutes les requêtes principales dans Application Insights Analytics, outil de recherche puissant pour Application Insights." 
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


 
# Visite guidée d’Application Insights Analytics


Application Insights Analytics est un moteur de recherche et de diagnostic puissant pour vos données de télémétrie [Application Insights](app-insights-overview.md).


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]
 
Pour vous aider à démarrer, examinons certaines requêtes de base.

## Connectez-vous à vos données Application Insights

Ouvrez Analytics à partir du [panneau Vue d’ensemble](app-insights-dashboards.md) de votre application dans Application Insights :

![Ouvrez portal.azure.com, ouvrez votre ressource Application Insights, puis cliquez sur Analyse.](./media/app-analytics/001.png)

## Compter les lignes

Les métriques telles que les compteurs de performance sont stockées dans une table appelée metrics. Chaque ligne est un point de données de télémétrie provenant du Kit de développement logiciel (SDK) Application Insights dans une application. Pour déterminer la taille de la table, nous allons diriger son contenu vers un opérateur qui compte simplement les lignes :

```AIQL
	
    requests | count
```

> [AZURE.NOTE] Placez le curseur quelque part dans l’instruction avant de cliquer sur OK. Vous pouvez répartir une instruction sur plusieurs lignes, mais ne placez pas de lignes vides dans une instruction. Pour conserver plusieurs requêtes dans la fenêtre, séparez-les par des lignes vides.

Voici le résultat :


![](./media/app-analytics-tour/010.png)

	
`Count` est un des nombreux [opérateurs de requête](app-analytics-queries.md) que nous pouvons organiser en canal, pour filtrer, remodeler et joindre les données en plusieurs étapes.
	
## Take : afficher n lignes


Examinons quelques données : quel est le contenu d’un échantillon de 5 lignes ?

```AIQL

	requests | take 5
```

Et voici ce que nous obtenons :

![results](./media/app-analytics-tour/020.png)

Choisissez les colonnes et ajustez leurs positions :

![Cliquez sur le sélecteur de colonnes en haut à droite des résultats](./media/app-analytics-tour/030.png)

Développez un élément pour afficher les détails :
 
![Choisissez la vue de table et utilisez Configurer les colonnes](./media/app-analytics-tour/040.png)


## Top et sort

`take` est utile pour obtenir un exemple rapide d’un résultat, mais il n’affiche pas les lignes de la table dans un ordre particulier. Pour obtenir un affichage ordonné, utilisez `top` (pour un échantillon) ou `sort` (qui porte sur la table entière).

Afficher les n premières lignes, classées par une colonne particulière :

```AIQL

	requests | top 10 by timestamp desc 
```

* *Syntaxe :* la plupart des opérateurs ont des paramètres de mot clé comme `by`.
* `desc` = ordre décroissant, `asc` = ordre croissant.

![](./media/app-analytics-tour/260.png)

`top...` est une façon plus performante d’exprimer `sort ... | take...`. Nous aurions pu écrire :

```AIQL

	requests | sort by timestamp desc | take 10
```

Le résultat serait le même, mais l’exécution de la requête serait un peu plus lente. (Vous pouvez également écrire `order`, qui est un alias de `sort`.)

Les en-têtes de colonne dans la vue de table peuvent également servir à trier les résultats sur l’écran. Mais bien sûr, si vous avez utilisé `take` ou `top` pour récupérer une partie seulement d’une table, vous devez uniquement trier de nouveau les enregistrements que vous avez récupérés.


## Project : sélectionner, renommer et calculer des colonnes

Utilisez `project` pour choisir uniquement les colonnes que vous souhaitez :

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-analytics-tour/240.png)


Vous pouvez également renommer des colonnes et en définir de nouvelles :

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project timestamp, 
               timeOfDay = floor(timestamp % 1d, 1s), 
               name, 
               response = resultCode
```

![result](./media/app-analytics-tour/270.png)

Dans l’expression scalaire :

* `%` est l’opérateur modulo habituel. 
* `1d` (le chiffre un, suivi de « d ») est un littéral d’intervalle de temps qui signifie un jour. Voici d’autres littéraux d’intervalle de temps : `12h`, `30m`, `10s`, `0.01s`.
* `floor` (alias `bin`) arrondit une valeur au multiple inférieur le plus proche de la valeur de base que vous fournissez. Ainsi, `floor(aTime, 1s)` arrondit une heure vers le bas à la seconde la plus proche.

Les [expressions](app-analytics-scalars.md) peuvent inclure tous les opérateurs habituels (`+`, `-`, ...), et il existe une gamme de fonctions utiles.

## Extend : calculer des colonnes

Si vous souhaitez simplement ajouter des colonnes à des colonnes existantes, utilisez `extend` :

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

Utiliser `extend` est plus concis que `project` si vous souhaitez conserver toutes les colonnes existantes.

## Summarize : agréger des groupes de lignes

En examinant un exemple de table, nous pouvons voir les champs où sont stockées les différentes données de télémétrie. Par exemple, `exception | take 20` nous montre rapidement que les messages d’exception se trouvent dans un champ appelé `outerExceptionType`.

Mais au lieu de nous attarder sur chacune des instances, demandons le nombre d’exceptions qui ont été recensées, selon leur type :

```AIQL

	exceptions 
    | summarize count() by outerExceptionType
```

![](./media/app-analytics-tour/210.png)

`Summarize` regroupe les lignes qui ont les mêmes valeurs dans les champs nommés dans la clause `by`, ce qui produit une ligne de résultat unique par groupe. Ainsi, dans ce cas, il y a une ligne par type d’exception. La fonction d’agrégation `count()` compte les lignes dans chaque groupe, fournissant une colonne dans le résultat.


De nombreuses [fonctions d’agrégation](app-analytics-aggregations.md) sont à votre disposition, et vous pouvez utiliser plusieurs d’entre elles dans un même opérateur summarize pour produire plusieurs colonnes calculées.

Par exemple, nous allons répertorier les requêtes HTTP pour lesquelles ces exceptions se produisent. À nouveau, en examinant un exemple de la table d’exceptions, vous remarquerez que les chemins des requêtes HTTP apparaissent dans une colonne nommée `operation_Name`.

```AIQL

    exceptions 
    | summarize count(), makeset(operation_Name)
      by outerExceptionType	      
```

![](./media/app-analytics-tour/220.png)

La fonction d’agrégation `makeset()` crée un ensemble de toutes les valeurs spécifiées dans chaque groupe. Comme le montre cet exemple, une seule opération est à l’origine de chaque exception.


Le résultat d’un résumé a :

* chaque colonne nommée dans `by` ;
* plus une colonne pour chaque expression d’agrégation ;
* une ligne pour chaque combinaison de valeurs `by`.


## Résumer en fonction de valeurs scalaires


Vous pouvez utiliser des valeurs scalaires (numériques, heure ou intervalle) dans la clause by. Mais les nombres remplissent généralement une plage continue. Pour regrouper les points de données, vous pouvez les affecter à des emplacements de valeurs discrètes. La fonction `bin` est utile pour cela :

```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

![](./media/app-analytics-tour/225.png)

`bin` réduit tous les horodatages à des intervalles de 1 jour. C’est un alias de `floor`, fonction courante dans la plupart des langages. Il réduit simplement chaque valeur au multiple le plus proche du modulo que vous spécifiez ; ainsi, `summarize` peut affecter les lignes à des groupes d’une taille raisonnable. (Sans cette fonction, nous aurions une ligne de résultat pour chaque fraction de seconde et les données ne seraient pas du tout résumées).

Nous pouvons aller au-delà de la vue de table. Examinons les résultats dans la vue graphique avec l’option barres verticales :

![Cliquez sur la vue graphique, puis choisissez le graphique à barres verticales et affectez les axes x et y](./media/app-analytics-tour/230.png)

Bien que nous n’ayons pas trié les résultats par heure (comme le montre l’affichage de table), le graphique affiche toujours les dates dans l’ordre approprié.


## Where : filtrage sur une condition

Si vous avez configuré la surveillance Application Insights pour les côtés [client](app-insights-javascript.md) et serveur de votre application, certaines des données de télémétrie dans la base de données proviennent des navigateurs.

Examinons uniquement les exceptions signalées à partir des navigateurs :

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-analytics-tour/250.png)

L’opérateur `where` prend une expression booléenne. Voici quelques points clés les concernant :

 * `and`, `or` : opérateurs booléens
 * `==`, `<>` : égal et non égal
 * `=~`, `!=` : chaîne respectant la casse (égal et non égal). Il existe de nombreux autres opérateurs de comparaison de chaîne.

Tout savoir sur les [expressions scalaires](app-analytics-scalars.md).

### Filtrage des événements

Rechercher les requêtes ayant échoué :

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` étant de type chaîne, nous devons [le convertir](app-analytics-scalars.md#casts) pour une comparaison numérique.

Résumer les différentes réponses :

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Graphiques temporels

Afficher le nombre d’événements qui se produisent chaque jour :

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Sélectionnez l’option d’affichage graphique :

![Graphique temporel](./media/app-analytics-tour/080.png)

L’axe des x des graphiques en courbes doit être de type DateTime.

## Séries multiples 

Utilisez plusieurs valeurs dans une clause `summarize by` pour créer une ligne distincte pour chaque combinaison de valeurs :

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-analytics-tour/090.png)

Pour afficher plusieurs lignes d’un graphique, cliquez sur **Répartir par** et choisissez une colonne.

![](./media/app-analytics-tour/100.png)



## Cycle moyen quotidien

Dans quelle mesure l’utilisation varie-t-elle dans un jour moyen ?

Compter les requêtes en prenant un jour comme modulo de temps et en fractionnant le résultat par heure :

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Graphique en courbes des heures dans une journée moyenne](./media/app-analytics-tour/120.png)

>[AZURE.NOTE] Nous devons convertir les durées en dates pour afficher les résultats sur un graphique.


## Comparer plusieurs séries quotidiennes

Dans quelle mesure l’utilisation varie-t-elle au fil de la journée dans différents États ?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Répartir le graphique par État :

![Répartition par client\_StateOrProvince](./media/app-analytics-tour/130.png)


## Tracer une distribution

Combien existe-t-il de sessions de différentes longueurs ?

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

La dernière ligne est nécessaire pour la conversion en valeurs datetime : actuellement, l’axe des x d’un graphique en courbes ne peut être que de type datetime.

La clause `where` exclut les sessions à déclenchement unique (sessionDuration==0) et définit la longueur de l’axe des x.


![](./media/app-analytics-tour/290.png)



## Centiles

Quelles sont les plages de durées qui couvrent différents pourcentages de sessions ?

Utilisez la requête ci-dessus, mais remplacez la dernière ligne :

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

Nous avons également supprimé la limite supérieure dans la clause where, afin d’obtenir des chiffres corrects englobant toutes les sessions avec plusieurs requêtes :

![result](./media/app-analytics-tour/180.png)

Nous pouvons voir que :

* 5 % des sessions durent moins de 3 minutes 34 s ; 
* 50 % des sessions durent moins de 36 minutes ;
* 5 % des sessions durent plus de 7 jours.

Pour obtenir une répartition distincte pour chaque pays, il suffit simplement d’intégrer la colonne client\_CountryOrRegion séparément par le biais des deux opérateurs summarize :

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

![](./media/app-analytics-tour/190.png)


## Join

Nous avons accès à trois tables : metric, exceptions et event. `event` comprend des rapports de requête, des affichages de pages, des événements personnalisés, etc.

Pour rechercher les exceptions liées à une requête qui a retourné une réponse d’échec, nous pouvons joindre les tables sur `session_Id` :

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Avant d’effectuer la jointure, nous pouvons utiliser `project` pour sélectionner uniquement les colonnes dont nous avons besoin. Dans les mêmes clauses, nous renommons la colonne timestamp.



## Let : affecter un résultat à une variable

Utilisez [let](./app-analytics-syntax.md#let-statements) pour séparer les parties de l’expression précédente. Les résultats sont identiques :

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Conseil : dans le client AI Analytics, ne placez pas de lignes vides entre les différentes parties de l’expression. Exécutez-la en totalité.


[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->