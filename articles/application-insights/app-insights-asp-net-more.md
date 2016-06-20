<properties 
	pageTitle="Tirez davantage d’Application Insights" 
	description="Après avoir pris en main Application Insights, voici un résumé des fonctionnalités que vous pouvez explorer." 
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
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Plus de télémétrie dans Application Insights

Voici un résumé des fonctionnalités qu’il vous reste peut-être encore à découvrir dans [Visual Studio Application Insights](app-insights-overview.md). Nous supposons que vous avez déjà [pris en main](app-insights-asp-net.md) la solution. Application Insights permet de surveiller la disponibilité, les performances et l’utilisation de votre application Web. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

## Données de télémétrie supplémentaires

||
|---|---
|[**Tests de disponibilité**](app-insights-monitor-web-app-availability.md)<br/>Envoyez des demandes HTTP à votre application web à intervalles réguliers à partir de divers emplacements dans le monde. Nous vous alerterons si la réponse est lente ou peu fiable.| 
|[**Appels de dépendance et AJAX**](app-insights-asp-net-dependencies.md)<br/>Surveillez les requêtes SQL et les appels à REST ou d’autres ressources.|
|[**Exceptions**](app-insights-asp-net-exceptions.md)<br/>Comptez les exceptions gérées et non gérées, obtenez l’arborescence des appels de procédure et parcourez le code.|
|[**Pages Web**](app-insights-javascript.md)<br/>Surveillez l’utilisation des pages et les performances en instrumentant vos pages web.
|**Performances de l’hôte : [Diagnostics Azure](app-insights-azure-diagnostics.md), [Compteurs de performances Windows](app-insights-web-monitor-performance.md)**<br/>Consultez l’utilisation du processeur et d’autres mesures contextuelles. |![](./media/app-insights-asp-net-more/04.png)
|[**API de SDK, télémétrie personnalisée**](app-insights-api-custom-events-metrics.md)<br/>Envoyez vos propres événements et mesures pour une vue plus détaillée des performances et de l’utilisation de votre application, dans le code serveur comme client.|
|[**Intégration des journaux**](app-insights-asp-net-trace-logs.md)<br/>Si vous utilisez une infrastructure comme Log4Net, NLog ou System.Diagnostics.Trace, un adaptateur envoie les suivis à Application Insights avec les autres données télémétriques.|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>Filtrez, modifiez ou améliores les données télémétriques envoyées du SDK dans votre application. |


## Analyse puissante et présentation

||
|---|---
|[**Recherche de diagnostic pour les données d’instance**](app-insights-visual-studio.md)<br/>Cherchez et filtrez les événements, comme les requêtes, les exceptions, les appels de dépendance, les suivis de journaux et les affichages de pages. Dans Visual Studio, accédez au code à partir de l’arborescence des appels de procédure.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Metrics Explorer pour les données agrégées**](app-insights-metrics-explorer.md)<br/>Explorez, filtrez et segmentez des données agrégées, comme les taux de demandes, d’échecs et d’exceptions, les temps de réponse et les durées de chargement des pages.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Tableaux de bord**](app-insights-dashboards.md#dashboards)<br/>Combinez des données de plusieurs sources et partagez-les avec d’autres. Idéal pour les applications à composants multiples et pour un affichage en continu dans la salle de l’équipe. |![Exemple de tableaux de bord](./media/app-insights-asp-net/62.png)
|[**Flux en direct**](app-insights-metrics-explorer.md#live-stream)<br/>Lorsque vous déployez une nouvelle version, observez ces indicateurs de performance quasiment en temps réel pour vous assurer que tout fonctionne comme prévu.|![Exemple d’analyse](./media/app-insights-asp-net-more/050.png)
|[**Analyses**](app-insights-analytics.md)<br/>Répondez à des questions difficiles sur les performances et l’utilisation de votre application avec ce langage de requêtes puissant.|![Exemple d’analyse](./media/app-insights-asp-net-more/010.png)
|[**Alertes automatiques et manuelles**](app-insights-alerts.md)<br/>Les alertes automatiques s’adaptent aux habitudes télémétriques normales de votre application et se déclenchent lorsqu’un comportement inhabituel est détecté. Vous pouvez également définir des alertes sur des niveaux particuliers de mesures personnalisées ou standard.|![Exemple d’alerte](./media/app-insights-asp-net-more/020.png)

## Gestion des données

|||
|---|---|
|[**Exportation continue**](app-insights-export-telemetry.md)<br/>Copiez toutes vos données télémétriques dans le stockage pour pouvoir les analyser de la façon de votre choix.|
|**API d’accès aux données**<br/>Prochainement disponible.|
|[**Échantillonnage**](app-insights-sampling.md)<br/>Réduit le débit de données et vous permet de rester dans la limite de votre niveau tarifaire.|![Mosaïque d’échantillonnage](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0608_2016-->