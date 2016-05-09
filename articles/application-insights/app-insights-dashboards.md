<properties
	pageTitle="Tableaux de bord dans le portail Application Insights"
	description="Affichez les données de télémétrie de plusieurs composants de votre application dans un tableau de bord."
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
	ms.date="04/25/2016"
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

* **n'importe quel graphique** pour afficher plus de détails ;
* **Paramètres** pour accéder à des pages prédéfinies d’autres mesures ;
* [**Metrics Explorer**](app-insights-metrics-explorer.md) pour créer des pages de mesures de votre choix ;
* [**Rechercher**](app-insights-diagnostic-search.md) pour analyser des instances spécifiques d’événements tels que les demandes, les exceptions ou les suivis de journal.
* [**Analytics**](app-insights-analytics.md) pour des requêtes puissantes sur vos données de télémétrie.


![Procédures principales d'affichage de vos données de télémétrie](./media/app-insights-dashboards/010-oview.png)


### Personnalisation du panneau Vue d’ensemble 

Choisissez ce que vous souhaitez afficher sur le panneau Vue d’ensemble. Dans la zone de personnalisation, vous pouvez insérer des titres de section, faire glisser des vignettes et des graphiques, supprimer des éléments et ajouter de nouvelles vignettes et graphiques à partir de la galerie.

![Cliquez sur Modifier. Faites glisser les vignettes et les graphiques. Ajoutez des vignettes de la galerie. Cliquez ensuite sur Terminé.](./media/app-insights-dashboards/020-customize.png)

## Tableaux de bord

La première chose que vous voyez après vous être connecté au [portail Microsoft Azure](https://portal.azure.com) est un tableau de bord. Celui-ci vous permet de rassembler les graphiques les plus importants pour vous pour toutes vos ressources Azure, y compris les données de télémétrie de [Visual Studio Application Insights](app-insights-overview.md).
 

![Un tableau de bord personnalisé.](./media/app-insights-dashboards/30.png)

1. Cliquez sur le coin supérieur à tout moment pour revenir au tableau de bord.
2. Cliquez sur un graphique ou une vignette du tableau de bord pour afficher plus de détails sur ses données.
3. Utilisez la barre de navigation pour obtenir une vue complète de toutes vos ressources.
4. Modifiez, créez et partagez des tableaux de bord à l’aide de la barre d’outils du tableau de bord.

## Ajouter des données à un tableau de bord

Lorsque vous examinez un panneau ou un ensemble de graphiques qui est particulièrement intéressant, vous pouvez en épingler une copie au tableau de bord. Celui-ci sera affiché lors de votre prochain accès au tableau de bord.

![Pour épingler un graphique, passez la souris sur celui-ci, puis cliquez sur « ... » dans l’en-tête.](./media/app-insights-dashboards/33.png)

Notez que les graphiques sont regroupés en vignettes : une vignette peut contenir plusieurs graphiques. Vous épinglez la vignette entière au tableau de bord.

## Ajustement d’une vignette sur le tableau de bord

Lorsqu’une vignette se trouve sur le tableau de bord, vous pouvez l’ajuster.

![Survolez un graphique avec la souris pour le modifier.](./media/app-insights-dashboards/36.png)

1. Ajoutez un graphique à la vignette. 
2. Définissez la mesure, la dimension et le style de regroupement (tableau, graphique) d’un graphique.
3. Définissez les propriétés de filtre et d’intervalle de temps pour les graphiques sur la vignette.
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

En savoir plus sur [les rôles et le contrôle d’accès](app-insights-resources-roles-access-control.md).

<!---HONumber=AcomDC_0427_2016-->