<properties
	pageTitle="Utilisation du portail Application Insights"
	description="Mesures, recherche, tableaux de bord et paramètres dans Application Insights."
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
	ms.date="07/30/2016"
	ms.author="awills"/>

# Navigation et tableaux de bord dans le portail Application Insights

Une fois que vous avez [configuré Application Insights sur votre projet](app-insights-overview.md), les données de télémétrie relatives aux performances et à l’utilisation de votre application apparaissent dans les ressources Application Insights de votre projet dans le [portail Azure](https://portal.azure.com).

## Rechercher vos données de télémétrie

Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à la ressource Application Insights que vous avez créée pour votre application.

![Cliquez sur Parcourir, sélectionnez Application Insights, puis sélectionnez votre application.](./media/app-insights-dashboards/00-start.png)

La page Vue d’ensemble fournit des données de télémétrie de base et offre une diversité de liens. Le contenu dépend du type de votre application et peut être personnalisé.


## Panneau Vue d’ensemble de l’application

Le panneau de vue d’ensemble (page) de votre application affiche un résumé des principales mesures de diagnostic de votre application et constitue une passerelle vers les autres fonctionnalités du portail.

Cliquez sur :

* **N’importe quel graphique ou mosaïque** pour plus de détails sur ce qui est affiché sur le graphique.
* **Paramètres** pour obtenir des panneaux prédéfinis d’autres mesures, ainsi que des pages de configuration.
* [**Metrics Explorer**](app-insights-metrics-explorer.md) pour créer des pages de mesures de votre choix ;
* [**Rechercher**](app-insights-diagnostic-search.md) pour analyser des instances spécifiques d’événements tels que les demandes, les exceptions ou les suivis du journal.
* [**Analytics**](app-insights-analytics.md) pour des requêtes puissantes sur vos données de télémétrie.
* [**Flux en direct**](app-insights-metrics-explorer.md#live-stream) pour un ensemble de mesures quasi instantanées, ce qui est utile lors du déploiement d’une nouvelle version ou du débogage.


![Procédures principales d'affichage de vos données de télémétrie](./media/app-insights-dashboards/010-oview.png)


### Personnalisation du panneau Vue d’ensemble 

Choisissez ce que vous souhaitez afficher sur le panneau Vue d’ensemble. Dans la zone de personnalisation, vous pouvez insérer des titres de section, faire glisser des vignettes et des graphiques, supprimer des éléments et ajouter de nouvelles vignettes et graphiques à partir de la galerie.

![Cliquez sur Modifier. Faites glisser les vignettes et les graphiques. Ajoutez des vignettes de la galerie. Cliquez ensuite sur Terminé.](./media/app-insights-dashboards/020-customize.png)

## Tableaux de bord

La première chose que vous voyez après vous être connecté au [portail Microsoft Azure](https://portal.azure.com) est un tableau de bord. Celui-ci vous permet de rassembler les graphiques les plus importants pour vous pour toutes vos ressources Azure, notamment les données de télémétrie de [Visual Studio Application Insights](app-insights-overview.md).
 

![Un tableau de bord personnalisé.](./media/app-insights-dashboards/31.png)


1. Modifiez, créez et partagez des tableaux de bord à l’aide de la barre d’outils du tableau de bord.
2. Revenez au tableau de bord actuel ou basculez vers les autres vues récentes.
3. Changez de tableau de bord.
4. Créez, modifiez et partagez des tableaux de bord.
5. Pointez sur une vignette, puis utilisez sa barre supérieure pour déplacer, personnaliser ou supprimer la vignette.

## Ajouter des données à un tableau de bord

Lorsque vous examinez un panneau ou un ensemble de graphiques qui est particulièrement intéressant, vous pouvez en épingler une copie au tableau de bord. Celui-ci sera affiché lors de votre prochain accès au tableau de bord.

![Pour épingler un graphique, passez la souris sur celui-ci, puis cliquez sur « ... » dans l’en-tête.](./media/app-insights-dashboards/33.png)

1. Épinglez un graphique au tableau de bord. Une copie du graphique apparaît sur le tableau de bord.
2. Épinglez la totalité du panneau au tableau de bord. Il apparaît alors sur le tableau de bord sous la forme d’une vignette sur laquelle vous pouvez cliquer.
3. Cliquez sur le coin supérieur gauche pour revenir au tableau de bord actuel. Vous pouvez ensuite utiliser le menu déroulant pour revenir à la vue actuelle.

Notez que les graphiques sont regroupés en vignettes : une vignette peut contenir plusieurs graphiques. Vous épinglez la vignette entière au tableau de bord.

### Graphiques Analytics

Vous pouvez également [épingler des graphiques Analytics](app-insights-analytics-using.md#pin-to-dashboard) à un tableau de bord [partagé](#share-dashboards-with-your-team). Ainsi, vous pouvez ajouter les graphiques de toute requête arbitraire en parallèle des mesures standards.

## Ajustement d’une vignette sur le tableau de bord

Lorsqu’une vignette se trouve sur le tableau de bord, vous pouvez l’ajuster.

![Survolez un graphique avec la souris pour le modifier.](./media/app-insights-dashboards/36.png)

1. Ajoutez un graphique à la vignette.
2. Définissez la mesure, la dimension et le style de regroupement (tableau, graphique) d’un graphique.
3. Définissez les propriétés de filtre et d’intervalle de temps pour les graphiques sur la vignette. Vous pouvez définir votre propre intervalle de temps du graphique, ou utiliser l’intervalle du tableau de bord.
4. Définissez le titre de la vignette.

Les vignettes épinglées à partir des panneaux de Metrics Explorer ont davantage d’options d’édition que les vignettes épinglées à partir d’un panneau Vue d’ensemble.

La vignette d’origine que vous avez épinglée n’est pas affectée par vos modifications.


## Basculement entre les tableaux de bord

Vous pouvez enregistrer plusieurs tableaux de bord et basculer entre ceux-ci. Lorsque vous épinglez un graphique ou un panneau, il est ajouté au tableau de bord actuel.

![Pour basculer entre les tableaux de bord, cliquez sur Tableau de bord et sélectionnez un tableau de bord enregistré. Pour créer et enregistrer un nouveau tableau de bord, cliquez sur Nouveau. Pour les réorganiser, cliquez sur Modifier.](./media/app-insights-dashboards/32.png)

Vous pouvez, par exemple, avoir un tableau de bord pour l’affichage en plein écran dans la salle de réunion et un autre pour le développement général.


Sur le tableau de bord, un panneau s’affiche sous forme de vignette : cliquez dessus pour accéder au panneau. Un graphique réplique le graphique dans son emplacement d’origine.

![Cliquez sur une vignette pour ouvrir le panneau qu’elle représente.](./media/app-insights-dashboards/35.png)


## Partage de tableaux de bord avec votre équipe

Lorsque vous avez créé un tableau de bord, vous pouvez le partager avec d’autres utilisateurs.

![Dans l’en-tête du tableau de bord, cliquez sur Partager](./media/app-insights-dashboards/41.png)

Apprenez-en davantage sur [les rôles et le contrôle d’accès](app-insights-resources-roles-access-control.md).

## Paramètres

Le bouton Paramètres du panneau de la vue d’ensemble ne se limite pas à la configuration, il permet également d’afficher plusieurs types de graphiques.

* **Journaux d’audit** : examinez les alertes et les actions qui ont été appliquées à votre ressource d’application.
* [Nouvelle demande de support](app-insights-get-dev-support.md) : découvrez les options permettant d’obtenir de l’aide sur tous les aspects d’Azure. Pour Application Insights, vous pouvez également obtenir de l’aide en utilisant le bouton émoticône en haut à droite de la fenêtre.
* [Plan de l’application](app-insights-dependencies.md#application-map) : plan actif présentant les composants de votre application, dérivé des informations de dépendance.
* [Live Metrics Stream (Flux de métriques en temps réel)](app-insights-metrics-explorer.md#live-metrics-stream) : métriques clés, avec une latence d’environ 1 seconde, pour la surveillance d’une nouvelle version.
* **Échecs** : demandes ayant échoué, appels de dépendance ayant échoué et exceptions. [Panneau de métriques modifiables](app-insights-metrics-explorer.md).
* **Performances** : temps de réponse, temps de réponse de dépendance. [Panneau de métriques modifiables](app-insights-metrics-explorer.md).
* [Serveurs](app-insights-web-monitor-performance.md) : compteurs de performances. Disponible si vous [installez Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Navigateur** : nombre de pages consultées et performances AJAX. Disponible si vous [instrumentez vos pages web](app-insights-javascript.md).
* **Utilisation** : nombre de pages consultées, d’utilisateurs et de sessions. Disponible si vous [instrumentez vos pages web](app-insights-javascript.md).
* **Prise en main** : didacticiel en ligne.
* **Propriétés** : clé d’instrumentation, abonnement et ID de ressource.
* [Disponibilité](app-insights-monitor-web-app-availability.md) : configuration et résultats du test web.
* [Alertes](app-insights-alerts.md) : configuration des alertes de métriques.
* [Exportation continue](app-insights-export-telemetry.md) : configurez l’exportation des données de télémétrie vers le stockage Azure.
* [Tests de performances](app-insights-monitor-web-app-availability.md#performance-tests) : configurez une charge synthétique sur votre site web.
* [Quota et tarification](app-insights-pricing.md) et [échantillonnage d’ingestion](app-insights-sampling.md).
* **Accès à l’API** : actuellement utilisé pour créer des [annotations de version](app-insights-annotations.md) et pour l’API d’accès aux données.
* [**Éléments de travail**](app-insights-diagnostic-search.md#create-work-item) : connectez-vous à un système de suivi du travail afin de créer des bogues lors de l’inspection de la télémétrie.
* [**Utilisateurs**](app-insights-resources-roles-access-control.md) : gérez les personnes qui ont accès à vos ressources d’application.
* [**Rôles**](app-insights-resources-roles-access-control.md) : gérez les actions exécutables par les utilisateurs.
* [**Balises**](..\resource-group-using-tags.md) : organisez les ressources Azure.
* [**Verrous**](..\resource-group-lock-resources.md) : verrouillez les ressources Azure.
* [**Exporter un modèle**](app-insights-powershell.md) : exportez une définition de la ressource Azure afin de l’utiliser comme modèle pour créer d’autres ressources.

## Et ensuite ?

||
|---|---
|[Metrics Explorer](app-insights-metrics-explorer.md)<br/>Filtrez et segmentez les mesures.|![Exemple de recherche](./media/app-insights-dashboards/64.png)
|[Recherche de diagnostic](app-insights-diagnostic-search.md)<br/>Recherchez et examinez des événements, ainsi que les événements associés, et créez des bogues. |![Exemple de recherche](./media/app-insights-dashboards/61.png)
|[Analytics](app-insights-analytics.md)<br/>Tirez parti d’un puissant langage de requête.| ![Exemple de recherche](./media/app-insights-dashboards/63.png)

<!---HONumber=AcomDC_0817_2016-->