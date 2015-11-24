<properties
    pageTitle="Analyse des applications iOS | Microsoft Azure"
    description="Analysez l’utilisation et les performances de votre application iOS."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Analyse des applications iOS

Visual Studio Application Insights vous permet d’analyser l’utilisation, les événements et les incidents de votre application mobile.


> [AZURE.NOTE]Nous vous recommandons [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) pour obtenir des rapports d’incident, des analyses, ainsi que des données sur la distribution et la gestion des commentaires.

## Conditions requises

Vous devez avoir :

* Un abonnement à [Microsoft Azure](http://azure.com). Vous vous connectez avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.
* Xcode 6 ou version ultérieure.
* Le Kit de développement logiciel (SDK) s’exécute sur les appareils iOS 6.0 ou version ultérieure.

## Création d’une ressource Application Insights dans Azure

Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez l’option iOS.

![Cliquez sur Nouveau, Services de développement, Application Insights](./media/app-insights-ios/11-new.png)

Dans le panneau qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l’écran d’accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

## Paramétrage

Veuillez suivre le [guide d’installation](https://github.com/Microsoft/ApplicationInsights-iOS#setup) pour installer le Kit de développement logiciel (SDK) dans votre application.

## Affichage de vos données dans Application Insights

Exécutez votre application pour générer un certain volume de télémétrie.

Revenez à http://portal.azure.com et accédez à vos ressources Application Insights.

Cliquez sur **Rechercher** pour ouvrir [Recherche de diagnostic][diagnostic] : les premiers événements y apparaissent. Si vous ne voyez rien, attendez une minute ou deux et cliquez sur **Actualiser**.

![Cliquez sur Recherche de diagnostic](./media/app-insights-ios/21-search.png)

Lorsque votre application est utilisée, les données s’affichent dans le panneau Vue d’ensemble.

![Panneau Vue d’ensemble](./media/app-insights-ios/22-oview.png)

Cliquez sur n’importe quel graphique pour obtenir plus de détails. Par exemple, pour les incidents :

![Cliquez sur le graphique de l’incident](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Étapes suivantes

[Suivi de l'utilisation de votre application][track]

[Recherche de diagnostic][diagnostic]

[Metrics Explorer][metrics]

[Résolution des problèmes][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->