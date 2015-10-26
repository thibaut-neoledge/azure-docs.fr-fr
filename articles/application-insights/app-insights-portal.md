<properties
	pageTitle="Utilisation du portail Application Insights"
	description="Présentation de l'analyse de l'utilisation avec Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Utilisation du portail Application Insights

Une fois que vous avez [configuré Application Insights sur votre projet](app-insights-overview.md), les données de télémétrie relatives aux performances et à l’utilisation de votre application apparaissent dans les ressources Application Insights de votre projet dans le [portail Azure](https://portal.azure.com).

## Localiser vos données de télémétrie dans Azure

Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à la ressource Application Insights que vous avez créée pour votre application.

![Cliquez sur Parcourir, sélectionnez Application Insights, puis sélectionnez votre application.](./media/app-insights-portal/00-start.png)

La page Vue d’ensemble fournit des données de télémétrie de base et offre une diversité de liens. Le contenu dépend du type de votre application et peut être personnalisé.

## Panneau Vue d’ensemble de l’application

Le panneau (la page) Vue d’ensemble de votre application affiche les graphiques essentiels pour la surveillance des performances et de l’utilisation. Le contenu dépend du type de votre application et peut être personnalisé comme vous le souhaitez.


### Personnalisation du panneau Vue d’ensemble 

Choisissez ce que vous souhaitez afficher sur le panneau Vue d’ensemble. Dans la zone de personnalisation, vous pouvez insérer des titres de section, faire glisser des vignettes et des graphiques, supprimer des éléments et ajouter de nouvelles vignettes et graphiques à partir de la galerie.

![Cliquez sur «... », puis sur Personnaliser. Faites glisser les vignettes et les graphiques. Ajoutez des vignettes de la galerie.](./media/app-insights-portal/020-customize.png)


## Créer votre propre graphique de mesure et vos propres grilles

### Modification des graphiques et des grilles

Pour ajouter un nouveau graphique au panneau :

![Dans Metrics Explorer, sélectionnez Ajouter un graphique](./media/app-insights-metrics-explorer/04-add.png)

Sélectionnez un graphique existant ou un nouveau graphique pour modifier ce qu'il présente :

![Sélectionner une ou plusieurs mesures](./media/app-insights-metrics-explorer/08-select.png)

Vous pouvez afficher plusieurs mesures dans un graphique, bien qu'il existe des restrictions sur les combinaisons affichables. Dès que vous sélectionnez une mesure, certaines autres sont désactivées.

Si vous avez ajouté des [mesures personnalisées](app-insights-api-custom-events-metrics.md#track-metric) au code de votre application (appels à TrackMetric et TrackEvent), elles sont répertoriées ici.

### Segmenter vos données

Sélectionnez un graphique ou une grille, basculez vers le regroupement et choisissez une propriété de regroupement :

![Activez le regroupement, puis une propriété de regroupement](./media/app-insights-metrics-explorer/15-segment.png)

Si vous avez ajouté des mesures personnalisées au code de votre application et qu’elles incluent des [valeurs de propriétés](app-insights-api-custom-events-metrics.md#properties), vous pourrez sélectionner la propriété dans la liste.

Le graphique est trop petit pour les données segmentées ? Ajustez la hauteur :

![Ajustez le curseur.](./media/app-insights-metrics-explorer/18-height.png)

### Filtrer vos données

Pour afficher uniquement les mesures d'un jeu de valeurs de propriété sélectionné :

![Cliquez sur le filtre, développez une propriété et vérifiez les valeurs](./media/app-insights-metrics-explorer/19-filter.png)

Si vous ne sélectionnez aucune valeur pour une propriété particulière, c'est la même chose que si vous aviez sélectionné toutes les valeurs : il n'existe aucun filtre sur cette propriété.

Notez le nombre d'événements en même temps que chaque valeur de propriété. Lorsque vous sélectionnez les valeurs d'une propriété, le nombre est modifié, en même temps que les autres valeurs de la propriété.

### Enregistrer votre panneau de mesures

Une fois que vous avez créé des graphiques, enregistrez-les en tant que favoris. Si vous travaillez avec un compte professionnel, vous pouvez choisir de les partager avec d'autres membres de l'équipe.

![Choisissez Favori](./media/app-insights-metrics-explorer/21-favorite-save.png)

Pour réafficher le panneau, **allez dans le panneau Vue d'ensemble** et ouvrez Favoris :

![Dans le panneau Vue d'ensemble, cliquez sur Favoris](./media/app-insights-metrics-explorer/22-favorite-get.png)

Si vous avez choisi une période relative lorsque vous avez enregistré, le panneau sera mis à jour avec les dernières mesures. Si vous avez choisi une période absolue, il affiche les mêmes données à chaque fois.

### Réinitialiser le panneau

Si vous modifiez un panneau, mais que vous souhaitez revenir à la configuration originale enregistrée, cliquez sur Réinitialiser.

![Dans les boutons en haut de Metrics Explorer](./media/app-insights-metrics-explorer/17-reset.png)

## Création d’une page de recherche

Ouvrez la recherche de diagnostic :

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Ouvrez le panneau Filtre et choisissez les types d’événement que vous souhaitez afficher. (Si vous souhaitez restaurer plus tard les filtres avec lesquels vous avez ouvert le panneau, cliquez sur Réinitialiser).

![Choisissez le filtre et sélectionnez les types de télémétrie](./media/app-insights-diagnostic-search/02-filter-req.png)

### Filtrer des valeurs de propriétés

Vous pouvez filtrer les événements en fonction des valeurs de leurs propriétés. Les propriétés disponibles varient en fonction des types d’événement que vous avez sélectionnés.

Par exemple, les demandes avec un code de réponse spécifique.

![Développez une propriété et choisissez une valeur](./media/app-insights-diagnostic-search/03-response500.png)

Si vous ne choisissez aucune valeur pour une propriété, cela a le même effet que si vous sélectionniez toutes les valeurs : le filtrage sur cette propriété est désactivé.


### Affiner votre recherche

Notez que les nombres à droite des valeurs de filtre affichent le nombre d’occurrences dans le jeu actuellement filtré.

Dans cet exemple, il est clair que la demande `Reports/Employees` provoque la majorité des 500 erreurs :

![Développez une propriété et choisissez une valeur](./media/app-insights-diagnostic-search/04-failingReq.png)

En outre, si vous voulez également voir les autres événements qui se sont produits pendant ce temps, vous pouvez vérifier **les événements dont les propriétés ne sont pas définies**.

### Enregistrer votre recherche

Une fois que vous avez défini tous les filtres que vous voulez, vous pouvez enregistrer la recherche dans vos recherches favorites. Si vous travaillez avec un compte professionnel, vous pouvez choisir de la partager avec d’autres membres de l’équipe.

![Cliquez sur Favori, définissez le nom et cliquez sur Enregistrer](./media/app-insights-diagnostic-search/08-favorite-save.png)


Pour réafficher la recherche, **allez dans le panneau Vue d’ensemble** et ouvrez Favoris :

![Vignette des favoris](./media/app-insights-diagnostic-search/09-favorite-get.png)

Si vous avez enregistré une période relative, le panneau rouvert comporte les données les plus récentes. Si vous avez enregistré une période absolue, vous voyez les mêmes données à chaque fois.

<!---HONumber=Oct15_HO3-->