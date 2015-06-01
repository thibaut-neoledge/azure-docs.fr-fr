<properties 
	pageTitle="Exploration des mesures dans Application Insights" 
	description="Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/07/2015" 
	ms.author="awills"/>
 
# Exploration des mesures dans Application Insights

Les mesures dans [Application Insights][start] sont des mesures et le nombre des événements envoyés par la télémétrie de votre application. Elles vous permettent de détecter les problèmes de performances et de constater les tendances dans l'utilisation de votre application. Il existe un large éventail de mesures standard et vous pouvez également créer vos propres mesures personnalisées et vos propres événements personnalisés.

Les mesures et le nombre des événements sont affichés dans des graphiques présentant les valeurs agrégées, comme la somme, la moyenne ou le décompte.

Par exemple, si vous ajoutez Application Insights à une application web, voici ce que vous voyez en haut de la vue d'ensemble :

![Ouvrez le panneau Vue d'ensemble de votre application dans le portail Azure](./media/app-insights-metrics-explorer/01-overview.png)

Certains graphiques sont segmentés : la hauteur totale du graphique correspond à la somme des mesures affichées. La légende par défaut affiche les plus grandes quantités.

Les pointillés indiquent la valeur de la mesure une semaine auparavant.

## Valeurs de point

Pointez votre souris sur le graphique pour afficher les valeurs des mesures à ce stade.


![Pointez la souris sur un graphique](./media/app-insights-metrics-explorer/02-focus.png)

La valeur de la mesure à un moment donné est agrégée à l'intervalle d'échantillonnage précédent. Cela peut varier en fonction de la période de l'ensemble du graphique.

L'intervalle d'échantillonnage, ou « grain », s'affiche en haut du panneau.

![En-tête du panneau.](./media/app-insights-metrics-explorer/11-grain.png)

## Période

Vous pouvez modifier la période couverte par la plupart des graphiques et des grilles dans n'importe quel panneau.

![Ouvrez le panneau Vue d'ensemble de votre application dans le portail Azure](./media/app-insights-metrics-explorer/03-range.png)


Si vous attendez des données qui ne sont pas encore affichées, cliquez sur Actualiser. Les graphiques ne sont pas mis à jour automatiquement. Dans la version finale, les données peuvent mettre un certain temps pour passer du pipeline d'analyse au graphique.

Dans le panneau Vue d'ensemble, sélectionnez une partie du graphique pour l'ouvrir en plus grand dans un nouveau graphique.


![Sélectionnez une partie d'un graphique.](./media/app-insights-metrics-explorer/12-drag.png)


## Metrics Explorer

Cliquez sur les graphiques du panneau Vue d'ensemble pour afficher un jeu plus détaillé de graphiques et de grilles connexes. Vous pouvez modifier ces graphiques et ces grilles pour vous concentrer sur les détails qui vous intéressent.

Par exemple, parcourez le graphique des demandes ayant échoué pour l'application web :

![Dans le panneau Vue d'ensemble, cliquez sur un graphique](./media/app-insights-metrics-explorer/14-trix.png)


## Que veulent dire les chiffres ?

La légende sur le côté affiche par défaut la valeur agrégée sur la période couverte par le graphique.

Chaque point de données du graphique est également un agrégat des valeurs de données reçues dans l'intervalle d'échantillonnage précédent, encore appelé « grain ». Le grain est indiqué en haut du panneau et varie en fonction de l'échelle de temps globale du graphique.

Les différentes mesures sont agrégées de différentes façons :

 * Pour mesurer le temps de réponse, la **moyenne** des valeurs est calculée sur la période du graphique.
 * Pour le nombre d'événements comme les demandes ayant échoué, l'agrégat est la **somme** des nombres sur la période.
 * Pour le nombre d'utilisateurs, l'agrégat est le nombre d'utilisateurs **uniques** sur la période. (Si un utilisateur est suivi plusieurs fois pendant la période, il est compté une seule fois.)

Pour déterminer si la valeur est une somme, une moyenne ou une valeur unique, cliquez sur le graphique et faites défiler jusqu'à la valeur sélectionnée. Vous pouvez également obtenir une brève description de la mesure.

![Placez le pointeur sur (i)](./media/app-insights-metrics-explorer/06-total.png)
 


## Modification des graphiques et des grilles

Pour ajouter un nouveau graphique au panneau :

![Dans Metrics Explorer, sélectionnez Ajouter un graphique](./media/app-insights-metrics-explorer/04-add.png)

Sélectionnez un graphique existant ou un nouveau graphique pour modifier ce qu'il présente :

![Sélectionner une ou plusieurs mesures](./media/app-insights-metrics-explorer/08-select.png)

Vous pouvez afficher plusieurs mesures dans un graphique, bien qu'il existe des restrictions sur les combinaisons affichables. Dès que vous sélectionnez une mesure, certaines autres sont désactivées.

Si vous avez ajouté des [mesures personnalisées][track] au code de votre application (appels à TrackMetric et TrackEvent), elles sont répertoriées ici.

## Segmenter vos données

Sélectionnez un graphique ou une grille, basculez vers le regroupement et choisissez une propriété de regroupement :

![Activez le regroupement, puis une propriété de regroupement](./media/app-insights-metrics-explorer/15-segment.png)

Si vous avez ajouté des [mesures personnalisées][track] au code de votre application et qu'elles incluent des valeurs de propriétés, vous pourrez sélectionner la propriété dans la liste.

Le graphique est trop petit pour les données segmentées ? Ajustez la hauteur :


![Ajustez le curseur.](./media/app-insights-metrics-explorer/18-height.png)


## Filtrer vos données

Pour afficher uniquement les mesures d'un jeu de valeurs de propriété sélectionné :

![Cliquez sur le filtre, développez une propriété et vérifiez les valeurs](./media/app-insights-metrics-explorer/19-filter.png)

Si vous ne sélectionnez aucune valeur pour une propriété particulière, c'est la même chose que si vous aviez sélectionné toutes les valeurs : il n'existe aucun filtre sur cette propriété.

Notez le nombre d'événements en même temps que chaque valeur de propriété. Lorsque vous sélectionnez les valeurs d'une propriété, le nombre est modifié, en même temps que les autres valeurs de la propriété.

## Supprimer le robot et tester le trafic web

Utilisez le filtre de **trafic réel ou synthétique** et activez l'option **réel**.

Vous pouvez également filtrer par **source du trafic synthétique**.

## Modifier le type de graphique

En particulier, notez que vous pouvez basculer entre les grilles et les graphiques :

![Sélectionnez une grille ou un graphique, puis choisissez un type de graphique](./media/app-insights-metrics-explorer/16-chart-grid.png)

## Enregistrer votre panneau de mesures

Une fois que vous avez créé des graphiques, enregistrez-les en tant que favoris. Si vous travaillez avec un compte professionnel, vous pouvez choisir de les partager avec d'autres membres de l'équipe.

![Choisissez Favori](./media/app-insights-metrics-explorer/21-favorite-save.png)

Pour réafficher le panneau, **allez dans le panneau Vue d'ensemble** et ouvrez Favoris :

![Dans le panneau Vue d'ensemble, cliquez sur Favoris](./media/app-insights-metrics-explorer/22-favorite-get.png)

Si vous avez choisi une période relative lorsque vous avez enregistré, le panneau sera mis à jour avec les dernières mesures. Si vous avez choisi une période absolue, il affiche les mêmes données à chaque fois.

## Réinitialiser le panneau

Si vous modifiez un panneau, mais que vous souhaitez revenir à la configuration originale enregistrée, cliquez sur Réinitialiser.

![Dans les boutons en haut de Metrics Explorer](./media/app-insights-metrics-explorer/17-reset.png)

## Définir des alertes

Pour être averti par courrier électronique en cas de valeurs inhabituelles pour une métrique, ajoutez une alerte. Vous pouvez choisir d'envoyer le courrier électronique aux administrateurs de compte ou à des adresses de messagerie spécifiques.

![Dans Metrics Explorer, sélectionnez Règles d'alerte, Ajouter une alerte](./media/appinsights/appinsights-413setMetricAlert.png)

[En savoir plus][alerts].


<!--Link references-->

[alerts]: app-insights-alerts.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54-->