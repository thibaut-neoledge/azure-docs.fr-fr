<properties
	pageTitle="Analyse des applications du Windows Phone et du Windows Store | Microsoft Azure"
	description="Analysez l’utilisation et les performances de votre application pour appareil Windows."
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
	ms.date="11/11/2015"
	ms.author="awills"/>

# Analyse des applications du Windows Phone et du Windows Store



Visual Studio Application Insights vous permet d’analyser les points suivants de vos applications publiées pour l’utilisation et les performances.


> [AZURE.NOTE]Nous vous recommandons [HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps) pour obtenir des rapports d’incident, des analyses, ainsi que des données sur la distribution et la gestion des commentaires.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Configuration d’Application Insights pour votre projet d’appareils Windows

Vous devez avoir :

* Un abonnement à [Microsoft Azure][azure].
* Visual Studio 2013 ou une version ultérieure.

**Applications C++ UAP** - Consultez le [guide de configuration en C++ d’Application Insights](https://github.com/Microsoft/ApplicationInsights-CPP)

### <a name="new"></a> Si vous créez un projet d’application Windows...

Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Application Insights**.

Si vous êtes invité à vous connecter, utilisez les informations d'identification de votre compte Azure.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>ou s’il s’agit d’un projet existant

Ajoutez Application Insights à partir de l'Explorateur de solutions.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Applications Windows universelles** : répétez les étapes pour les projets Windows Phone et Windows Store. [Exemple d’application universelle Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. Activation de l'accès réseau pour votre application

Si votre application n’a pas déjà [demandé un accès à Internet](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), vous devez ajouter ceci à son manifeste en tant que [capacité requise](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Exécution de votre projet

[Exécutez votre application en appuyant sur F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) et utilisez-la pour générer des données de télémétrie.

Un décompte des événements qui ont été reçus s'affiche dans Visual Studio.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

En mode débogage, les données de télémétrie sont envoyées dès qu'elles sont générées. En mode version finale, les données de télémétrie sont stockées sur le périphérique et envoyées uniquement lors de la reprise de l'application.


## <a name="monitor"></a>5. Affichage des données surveillées

Sur le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights que vous avez créée précédemment.

Au début, seuls un ou deux points s'affichent. Par exemple :

![Cliquez sur d'autres éléments pour afficher plus de données](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Après quelques secondes, cliquez sur **Actualiser** pour obtenir des données supplémentaires.

Cliquez sur n'importe quel graphique pour afficher plus de détails.


## <a name="deploy"></a>5. Publication de votre application dans Windows Store

[Publiez votre application](http://dev.windows.com/publish) et observez les données s'accumuler lorsque les utilisateurs la téléchargent et l'utilisent.

## Personnalisation de votre télémétrie

#### Sélection des collecteurs

Le SDK Application Insights inclut plusieurs collecteurs, qui recueillent automatiquement différents types de données à partir de votre application. Par défaut, ils sont tous actifs. Mais vous pouvez choisir les collecteurs à initialiser dans le constructeur de l’application :

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session
       | WindowsCollectors.UnhandledException);

#### Envoi de votre propre télémétrie

Utilisez l’[API][api] pour envoyer des événements, des métriques et des données de diagnostic à Application Insights. En résumé :

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

Pour plus d’informations, consultez [Présentation de l’API : événements et métriques personnalisés][api].

## Et ensuite ?

* [Détection et diagnostic des incidents touchant votre application][windowsCrash]
* [En savoir plus sur les métriques][metrics]
* [En savoir plus sur Diagnostic Search][diagnostic]




## Mettre à niveau vers une nouvelle version du Kit de développement logiciel (SDK)

Quand un nouveau [Kit de développement logiciel (SDK) est publié](app-insights-release-notes-windows.md) :

* Cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet.
* Sélectionnez les packages d’Application Insights installés et choisissez **Action : Mettre à niveau**.


## <a name="usage"></a>Étapes suivantes


[Détection et diagnostic des incidents touchant votre application][windowsCrash]

[Capture et recherche des journaux de diagnostic][diagnostic]


[Suivi de l'utilisation de votre application][windowsUsage]

[Utiliser l'API pour envoyer des données de télémétrie personnalisées][api]

[Résolution des problèmes][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=Nov15_HO4-->