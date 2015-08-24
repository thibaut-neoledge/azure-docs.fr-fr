<properties 
	pageTitle="Utilisation de Diagnostic Search" 
	description="Recherchez et filtrez des événements individuels, des demandes et le suivi des journaux." 
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
	ms.date="08/07/2015" 
	ms.author="awills"/>
 
# Utilisation de Recherche de diagnostic dans Application Insights

Recherche de diagnostic est le panneau d’[Application Insights][start] qui vous permet de rechercher et d’explorer les éléments de télémétrie, par exemple les pages vues, les exceptions ou les demandes web. Vous pouvez également afficher le suivi et les événements de journal que vous avez codés.

## Quand voyez-vous Recherche de diagnostic ?

Vous pouvez ouvrir Recherche de diagnostic de façon explicite :

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


Il s’affiche également lorsque vous parcourez certains graphiques et éléments de la grille. Dans ce cas, les filtres sont prédéfinis pour vous permettre de vous concentrer sur le type d’élément que vous avez sélectionné.

Par exemple, si votre application est un service web, le panneau Vue d’ensemble affiche un graphique du volume des demandes. Cliquez sur ce graphique pour obtenir un graphique plus détaillé, avec une liste indiquant combien de demandes ont été effectuées pour chaque URL. Cliquez sur une ligne pour obtenir une liste des demandes individuelles pour cette URL :

![Open diagnostic search](./media/app-insights-diagnostic-search/07-open-from-filters.png)


La partie principale de Recherche de diagnostic est une liste d’éléments de télémétrie : demandes serveur, pages vues, événements personnalisés que vous avez codés, et ainsi de suite. En haut de la liste se trouve un graphique de synthèse indiquant le nombre d’événements au fil du temps.

Les événements s’affichent généralement dans Recherche de diagnostic avant d’apparaître dans Metrics Explorer. Même si le panneau est actualisé à intervalles réguliers, vous pouvez cliquer sur Actualiser si vous attendez un événement spécifique.


## Inspecter les éléments un par un

Sélectionnez un élément de télémétrie pour afficher les champs clés et les éléments associés. Si vous voulez voir l’ensemble des champs, cliquez sur « ... ».

![Open diagnostic search](./media/app-insights-diagnostic-search/10-detail.png)

Pour obtenir l’ensemble des champs, utilisez des chaînes de texte brut (sans caractères génériques). Les champs disponibles dépendent du type de télémétrie.

## Filtrer les types d’événement

Ouvrez le panneau Filtre et choisissez les types d’événement que vous souhaitez afficher. (Si vous souhaitez restaurer plus tard les filtres avec lesquels vous avez ouvert le panneau, cliquez sur Réinitialiser).


![Choisissez le filtre et sélectionnez les types de télémétrie](./media/app-insights-diagnostic-search/02-filter-req.png)


Les types d'événements sont :

* **Suivi** : les journaux de diagnostic comprennent les appels TrackTrace, log4Net, NLog et System.Diagnostic.Trace.
* **Demandes** : demandes HTTP reçues par votre serveur d’applications, dont les pages, les scripts, les images, les fichiers de style et les données. Ces événements sont utilisés pour créer les graphiques de présentation de la demande et la réponse.
* **Affichage de page** : télémétrie envoyée par le client web et utilisée pour créer les rapports d’affichage des pages. 
* **Événement personnalisé** : si vous avez inséré des appels vers TrackEvent() pour [surveiller l’utilisation][track], vous pouvez les rechercher ici.
* **Exception** : exceptions non interceptées sur le serveur et celles que vous enregistrez avec TrackException().

## Filtrer des valeurs de propriétés

Vous pouvez filtrer les événements en fonction des valeurs de leurs propriétés. Les propriétés disponibles varient en fonction des types d’événement que vous avez sélectionnés.

Par exemple, les demandes avec un code de réponse spécifique.

![Développez une propriété et choisissez une valeur](./media/app-insights-diagnostic-search/03-response500.png)

Si vous ne choisissez aucune valeur pour une propriété, cela a le même effet que si vous sélectionniez toutes les valeurs : le filtrage sur cette propriété est désactivé.


### Affiner votre recherche

Notez que les nombres à droite des valeurs de filtre affichent le nombre d’occurrences dans le jeu actuellement filtré.

Dans cet exemple, il est clair que la demande `Reports/Employees` provoque la majorité des 500 erreurs :

![Développez une propriété et choisissez une valeur](./media/app-insights-diagnostic-search/04-failingReq.png)

En outre, si vous voulez également voir les autres événements qui se sont produits pendant ce temps, vous pouvez vérifier **les événements dont les propriétés ne sont pas définies**.

## Supprimer le robot et tester le trafic web

Utilisez le filtre de **trafic réel ou synthétique** et activez l'option **réel**.

Vous pouvez également filtrer par **source du trafic synthétique**.

## Inspecter les occurrences individuelles

Ajoutez le nom de cette demande au jeu de filtres et vous pourrez inspecter chaque occurrence de cet événement.

![Sélectionnez une valeur](./media/app-insights-diagnostic-search/05-reqDetails.png)

Pour les événements des demandes, les détails indiquent les exceptions qui se sont produites lors du traitement de la demande.

Cliquez sur une exception pour en afficher les détails, y compris l’arborescence des appels de procédure.

![Cliquez sur une exception](./media/app-insights-diagnostic-search/06-callStack.png)

## Rechercher des événements avec la même propriété

Recherchez tous les éléments dont la valeur de la propriété est la même :

![Cliquez avec le bouton droit sur une propriété](./media/app-insights-diagnostic-search/12-samevalue.png)

## Rechercher par valeur de mesure

Récupérez toutes les demandes dont le temps de réponse excède 5 secondes. Les heures sont indiquées en cycles : 10 000 cycles = 1 ms.

!["Response time":(threshold TO \*)](./media/app-insights-diagnostic-search/11-responsetime.png)



## Recherche dans les données

Vous pouvez rechercher des termes dans une des valeurs des propriétés. Ceci est particulièrement utile si vous avez écrit des [événements personnalisés][track] avec les valeurs de propriétés.

Vous pouvez définir une durée, car les recherches sur les plages courtes sont plus rapides.

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

Recherchez des termes, et non des sous-chaînes. Les termes sont des chaînes alphanumériques comprenant des signes de ponctuation (comme « . » et « \_ »). Par exemple :

terme|*non* trouvé en recherchant|mais en recherchant
---|---|---
HomeController.About|about<br/>home|h\*about<br/>home\*
IsLocal|local<br/>is<br/>\*local|isl\*<br/>islocal<br/>i\*l\*
New Delay|w d|new<br/>delay<br/>n\* AND d\*


Expressions de recherche utilisables :

Exemple de requête | Résultat 
---|---
slow|Trouve tous les événements dont la période comprend le terme « slow »
database??|Renvoie database01, databaseAB,...<br/>? n’est pas autorisé au début du terme à rechercher.
database\*|Renvoie database, database01, databaseNNNN<br/>\* n’est pas autorisé au début du terme à rechercher.
apple AND banana|Trouve les événements qui contiennent les deux termes. Utilisez « AND » en lettres majuscules (et non « and » en lettres minuscules).
apple OR banana<br/>apple banana|Trouve les événements qui contiennent un des deux termes. Utilisez « OR » et non « or ».</br/>Forme abrégée.
apple NOT banana<br/>apple -banana|Trouve les événements qui contiennent un terme, mais pas l’autre.<br/>Forme abrégée.
app\* AND banana -(grape pear)|Opérateurs logiques et utilisation des parenthèses.
"Metric": 0 TO 500<br/>"Metric" : 500 TO \* | Trouve les événements qui contiennent la mesure nommée dans la plage de valeurs.


## Enregistrer votre recherche

Une fois que vous avez défini tous les filtres que vous voulez, vous pouvez enregistrer la recherche dans vos recherches favorites. Si vous travaillez avec un compte professionnel, vous pouvez choisir de la partager avec d’autres membres de l’équipe.

![Cliquez sur Favori, définissez le nom et cliquez sur Enregistrer](./media/app-insights-diagnostic-search/08-favorite-save.png)


Pour réafficher la recherche, **allez dans le panneau Vue d’ensemble** et ouvrez Favoris :

![Vignette des favoris](./media/app-insights-diagnostic-search/09-favorite-get.png)

Si vous avez enregistré une période relative, le panneau rouvert comporte les données les plus récentes. Si vous avez enregistré une période absolue, vous voyez les mêmes données à chaque fois.


## Envoyer plus de télémétrie à Application Insights

En plus de la télémétrie fournie par le Kit de développement logiciel (SDK) Application Insights, vous pouvez :

* Capturer le suivi du journal dans votre infrastructure de journalisation favorite dans [.NET][netlogs] ou [Java][javalogs]. Cela signifie que vous pouvez effectuer des recherches dans le suivi du journal et les mettre en corrélation avec les pages vues, les exceptions et autres événements. 
* [Écrire du code][track] pour envoyer les événements personnalisés, les pages vues et les exceptions. 

[Découvrez comment envoyer les journaux et la télémétrie personnalisée à Application Insights][trace].


## <a name="questions"></a>Questions et réponses

### <a name="limits"></a>Quelle est la quantité de données conservée ?

Jusqu'à 500 événements par seconde pour chaque application. Les événements sont conservés pendant sept jours.

### Comment puis-je consulter les données POST dans mes demandes serveur ?

Nous n’enregistrons pas automatiquement les données POST, mais vous pouvez utiliser [TrackTrace ou le journal des appels][trace]. Placez les données POST dans le paramètre de message. Vous ne pouvez pas filtrer les messages comme pour les propriétés, mais la limite de taille est plus importante.

## <a name="add"></a>Étapes suivantes

* [Envoi des journaux et de la télémétrie personnalisée à Application Insights][trace]
* [Configuration des tests de disponibilité et de réactivité][availability]
* [Résolution des problèmes][qna]



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=August15_HO7-->