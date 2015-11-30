<properties 
	pageTitle="Application Insights pour ASP.NET 5" 
	description="Surveiller la disponibilité, les performances et l’utilisation.des applications Web." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/11/2015" 
	ms.author="awills"/>

# Application Insights pour ASP.NET 5

Visual Studio Application Insights permet de surveiller la disponibilité, les performances et l’utilisation de votre application Web. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

![Exemple](./media/app-insights-asp-net-five/sample.png)

Tout d’abord, vous avez besoin d’un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.


## Prise en main

Si vous avez créé votre projet dans Visual Studio 2015, Application Insights doit déjà être présent. Dans le cas contraire, suivez le [guide de prise en main](https://github.com/Microsoft/ApplicationInsights-aspnet5/wiki/Getting-Started).

## Utilisation d’Application Insights

Connectez-vous au [portail Microsoft Azure](https://portal.azure.com) et accédez à la ressource que vous avez créée pour surveiller votre application.

Dans une fenêtre de navigateur distincte, utilisez votre application pendant un certain temps. Des données s’affichent dans les graphiques Application Insights. (Vous devrez peut-être cliquer sur Actualiser.) Il y aura seulement une petite quantité de données pendant que vous effectuerez le développement, mais ces graphiques seront vraiment actifs lorsque vous publierez votre application et que vous aurez de nombreux utilisateurs.

La page de vue d’ensemble présente les graphiques de performances qui vous intéressent probablement le plus : temps de réponse du serveur, temps de chargement des pages et nombre de demandes ayant échoué. Cliquez sur un graphique pour afficher d’autres graphiques et données.

Les affichages dans le portail se répartissent en deux catégories principales :

* [Metrics Explorer](app-insights-metrics-explorer.md) affiche les graphiques et les tables de mesures et de nombres, comme les temps de réponse, les taux de défaillance ou les mesures que vous créez vous-même avec l’[API](app-insights-api-custom-events-metrics.md). Filtrez et segmentez les données par valeurs de propriété pour obtenir une meilleure compréhension de votre application et de ses utilisateurs.
* L’[Explorateur de recherche](app-insights-diagnostic-search.md) répertorie les événements individuels, tels que les demandes, exceptions, suivis de journal ou événements spécifiques que vous avez créés vous-même avec l’[API](app-insights-api-custom-events-metrics.md). Filtrez et recherchez dans les événements et naviguez entre les événements connexes pour étudier les problèmes.

## Alertes

* Configurez les [tests de disponibilité](app-insights-monitor-web-app-availability.md) pour tester votre site web en continu à partir d’emplacements du monde entier et recevez des notifications par courrier électronique dès qu’un test échoue.
* Configurez les [alertes de mesures](app-insights-monitor-web-app-availability.md) pour savoir si les mesures telles que les temps de réponse ou les taux d’exception dépassent les limites acceptables.


## Obtenir une télémétrie supplémentaire

* [Surveillez les dépendances](app-insights-dependencies.md) pour voir si REST, SQL ou d’autres ressources externes vous ralentissent.
* [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) vérifient votre application en permanence dans le monde entier. 


## Open source

[Lire et contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Nov15_HO4-->