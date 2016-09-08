<properties
	pageTitle="Analyse des applications du Windows Phone et du Windows Store | Microsoft Azure"
	description="Analysez l’utilisation et les pannes de votre application pour appareil Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/26/2016"
	ms.author="awills"/>

# Analyse des applications du Windows Phone et du Windows Store

Microsoft propose deux solutions pour les appareils devOps : [HockeyApp](http://hockeyapp.net/) pour l’analyse côté client ; et [Application Insights](app-insights-overview.md) pour le côté serveur.

[HockeyApp](http://hockeyapp.net/) est notre solution Mobile DevOps pour les applications pour appareils iOS, OS X, Android ou Windows, ainsi que pour les applications inter-plateformes sur Xamarin, Cordova et Unity. Avec elle, vous pouvez distribuer des versions pour les testeurs de versions béta, collecter des données liées aux pannes et obtenir des mesures et retours utilisateur. Elle est intégrée à Visual Studio Team Services, ce qui permet d’activer des déploiements de versions simples et d’intégrer des éléments de travail.

## Prise en main avec HockeyApp

Accédez à :

* [HockeyApp](http://support.hockeyapp.net/kb)
* [HockeyApp pour Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)
* [Blog HockeyApp](http://hockeyapp.net/blog/)
* Rejoignez le programme [HockeyApp Preseason](http://hockeyapp.net/preseason/) pour bénéficier de versions préliminaires.

Si votre application dispose d’un côté serveur, utilisez [Application Insights](app-insights-overview.md) pour l’analyse du côté serveur web de votre application sur [ASP.NET](app-insights-asp-net.md) ou [J2EE](app-insights-java-get-started.md).


Vous pouvez également utiliser [Application Insights pour les applications de bureau Windows](app-insights-windows-desktop.md).

## Analyse, exportation et accès d’API aux données HockeyApp 

[Configurez un pont HockeyApp](app-insights-hockeyapp-bridge-app.md) dans Application Insights. Vous pouvez ainsi effectuer les opérations suivantes :

* Utiliser le puissant langage de requête d’[analyse](app-insights-analytics.md) sur vos données de télémétrie.
* [Exporter les données de télémétrie](app-insights-export-telemetry.md) vers le stockage d’objets blob Azure.

## Étapes suivantes

* [Prise en main de HockeyApp pour Windows](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone)

<!---HONumber=AcomDC_0831_2016-->