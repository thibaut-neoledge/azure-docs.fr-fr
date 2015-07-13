<properties 
	pageTitle="Analyse des performances d’une application" 
	description="Graphique de charge et de temps de réponse, informations de dépendance et alertes sur les performances." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# Analyse des performances d’une application

Dans le [portail Azure](http://portal.azure.com), vous pouvez configurer l’analyse pour collecter des statistiques et des détails sur les dépendances de l’application dans vos applications web ou vos machines virtuelles.

Azure prend en charge l’Analyse des performances des applications (*APM*) en tirant parti des *extensions*. Ces dernières sont installées dans votre application et collectent les données qu’elles renvoient aux services d’analyse.

## Activation d’une extension

1. Cliquez sur **Parcourir** et sélectionnez l’application web ou la machine virtuelle que vous souhaitez instrumenter.

2. Cliquez sur la vignette **Analyse des applications** sous **Analyse**.

3. Choisissez le fournisseur d’extensions que vous souhaitez utiliser, par exemple **Application Insights** ou **New Relic**.

![Analyse des performances des applications web](./media/insights-perf-analytics/05-extend.png)

Ou, si vous utilisez une machine virtuelle :

![Machine virtuelle](./media/insights-perf-analytics/10-vm1.png)

### Pour Application Insights : régénérez avec le Kit de développement logiciel (SDK)

New Relic peut être installé automatiquement sans aucune instrumentation supplémentaire, mais Application Insights a d’autres exigences.

Dans Visual Studio, ajoutez le Kit de développement logiciel (SDK) Application Insights à votre projet.

![Cliquez avec le bouton droit sur le projet web et sélectionnez Ajouter Application Insights.](./media/insights-perf-analytics/03-add.png)

Lorsque vous êtes invité à vous connecter, utilisez les informations d’identification de votre compte Azure.

Vous pouvez tester la télémétrie en exécutant l’application sur votre ordinateur de développement ou vous pouvez simplement la republier.

Le Kit de développement logiciel (SDK) contient une API qui vous permet d’[écrire de la télémétrie personnalisée](../app-insights-api-custom-events-metrics.md) pour suivre l’utilisation.

## Exploration des données

Utilisez votre application pendant un certain temps afin de générer un certain volume de télémétrie.

1. Ensuite, l’extension est affichée comme étant installée dans le panneau de votre application web ou machine virtuelle.
2. Cliquez sur la ligne qui représente votre application pour accéder à ce fournisseur : ![Cliquer sur Actualiser.](./media/insights-perf-analytics/06-overview.png)

Vous pouvez également utiliser **Parcourir** pour accéder directement au composant Application Insights ou au compte New Relic que vous avez utilisé.

Une fois que vous arrivez sur le panneau, pour Application Insights par exemple, vous pouvez : Ouvrir Performances.

![Dans le panneau de vue d’ensemble d’Application Insights, cliquez sur la vignette des performances;](./media/insights-perf-analytics/07-dependency.png)

- Extraire les différentes demandes :

![Dans la grille, cliquez sur une dépendance pour voir les demandes connexes.](./media/insights-perf-analytics/08-requests.png)

- Voici un exemple qui montre le temps passé dans une dépendance SQL, notamment le nombre d’appels SQL et les statistiques associées comme la durée moyenne et l’écart-type. 

![](./media/insights-perf-analytics/01-example.png)



## Étapes suivantes

* [Analyse des mesures d’intégrité du service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service.
* [Réceptions de notifications d’alerte](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisez [Application Insights pour les pages Web et les applications JavaScript](../app-insights-web-track-usage.md) pour obtenir une analyse client des navigateurs qui consultent une page Web.
* [Surveillance de la disponibilité et de la réactivité des pages Web](../app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.
 

<!---HONumber=62-->