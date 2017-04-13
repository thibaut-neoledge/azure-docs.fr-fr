---
title: "Journaux et diagnostics d’ADP.NET dans Azure Application Insights | Microsoft Docs"
description: "Diagnostiquez les problèmes dans les applications web ASP.NET en recherchant les requêtes, les exceptions et les journaux générés avec Trace, NLog ou Log4Net."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 99860c53-0324-4a3a-9aa9-83f5dffba835
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/08/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 874e9abb7ae7e06808645ae2ab7cd5b3c0d36e04
ms.lasthandoff: 01/25/2017


---
# <a name="logs-exceptions-and-custom-diagnostics-for-aspnet-in-application-insights"></a>Journaux, exceptions et diagnostics personnalisés pour ASP.NET dans Application Insights
[Application Insights][start] inclut un puissant outil, [Recherche de diagnostic][diagnostic], qui vous permet d’explorer et d’examiner la télémétrie envoyée par le kit de développement logiciel (SDK) Application Insights depuis votre application. De nombreux événements tels que les affichages de pages utilisateur sont automatiquement envoyés par le kit de développement logiciel (SDK).

Vous pouvez également écrire du code pour envoyer des événements personnalisés, des rapports d’exception et des suivis. Et si vous utilisez déjà un framework de journalisation, tel que log4J, log4net, NLog, ou System.Diagnostics.Trace, vous pouvez capturer ces journaux et les inclure dans la recherche. Cela simplifie la corrélation des suivis des journaux avec les actions utilisateur, les exceptions et les autres événements.

## <a name="send"></a>Avant d’écrire une télémétrie personnalisée
Si ce n’est déjà fait, [configurez Application Insights pour votre projet][start].

Lorsque vous exécutez votre application, celle-ci envoie des données de télémétrie qui s’affichent dans Recherche de diagnostic, y compris des demandes reçues par le serveur, des affichages de pages consignées au niveau du client et des exceptions non interceptées.

Exécutez la fonction Recherche de diagnostic pour afficher les données de télémétrie envoyées automatiquement par le Kit SDK.

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

Les informations varient selon le type d’application. Vous pouvez cliquer dans n’importe quel événement individuel pour obtenir plus de détails.

## <a name="sampling"></a>Échantillonnage
Si votre application envoie des données en grand nombre et si vous utilisez le Kit SDK Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie. [En savoir plus sur l’échantillonnage.](app-insights-sampling.md)

## <a name="events"></a>Événements personnalisés
Les événements personnalisés apparaissent à la fois dans [Recherche de diagnostic][diagnostic] et [Metrics Explorer][metrics]. Vous pouvez les envoyer à partir d’appareils, de pages web et d’applications de serveur. Ils peuvent servir à des fins de diagnostic et à [comprendre les modèles d’utilisation][track].

Un événement personnalisé a un nom et peut également comporter des propriétés que vous pouvez filtrer, ainsi que des mesures numériques.

JavaScript côté client

    appInsights.trackEvent("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# côté serveur

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);


VB côté serveur

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### <a name="run-your-app-and-view-the-results"></a>Exécutez votre application et affichez les résultats.
Ouvrez Recherche de diagnostic.

Sélectionnez l’événement personnalisé et un nom d’événement particulier.

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)

Filtrez davantage les données en saisissant un terme de recherche sur une valeur de propriété.  

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

Explorez un événement pour afficher ses propriétés détaillées.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

## <a name="pages"></a> Affichages de pages
La télémétrie des affichages de pages est envoyée par l’appel trackPageView() dans l’[extrait de code JavaScript que vous insérez dans vos pages web][usage]. Son principal objectif est de contribuer au nombre d’affichages de pages que vous voyez sur la page de présentation.

En général, il est appelé une fois dans chaque page HTML, mais vous pouvez insérer plusieurs appels. Par exemple, si vous disposez d’une application d’une seule page et que vous souhaitez enregistrer une nouvelle page chaque fois que l’utilisateur obtient plus de données.

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

Il est parfois utile de joindre des propriétés que vous pouvez utiliser comme filtres lors de la recherche de diagnostic :

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


## <a name="trace"></a> Télémétrie de trace
La télémétrie de trace est le code que vous insérez de manière spécifique pour créer des journaux de diagnostic. 

Par exemple, vous pouvez insérer des appels tels que :

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


#### <a name="install-an-adapter-for-your-logging-framework"></a>Installation d’un adaptateur pour votre infrastructure de journalisation
Vous pouvez également rechercher des journaux générés à l’aide d’un framework de journalisation, comme log4Net, NLog ou System.Diagnostics.Trace. 

1. Si vous prévoyez d'utiliser log4Net ou NLog, installez-le dans votre projet. 
2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis sélectionnez **Gérer les packages NuGet**.
3. Sélectionnez En ligne > Tout, puis **Inclure la version préliminaire** et recherchez « Microsoft.ApplicationInsights »
   
    ![Get the prerelease version of the appropriate adapter](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)
4. Sélectionnez le package approprié parmi :
   
   * Microsoft.ApplicationInsights.TraceListener (pour capturer les appels System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

Le package NuGet installe les assemblys nécessaires et modifie également le fichier web.config ou app.config.

#### <a name="pepper"></a>Insérer des appels de journaux de diagnostic
Si vous utilisez System.Diagnostics.Trace, un appel standard serait :

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Si vous préférez log4net ou NLog :

    logger.Warn("Slow response - database01");

Exécutez votre application en mode débogage, ou déployez-la.

Les messages s’affichent dans Recherche de diagnostic lorsque vous sélectionnez le filtre Suivi.

### <a name="exceptions"></a>Exceptions
La fonctionnalité d’obtention de rapports d’exception dans Application Insights est très puissante, d’autant plus qu’elle vous permet de parcourir les demandes ayant échoué et les exceptions, et de lire la pile d’exceptions.

Dans certains cas, vous devez [insérer quelques lignes de code][exceptions] pour vous assurer que vos exceptions sont détectées automatiquement.

Vous pouvez également écrire du code explicite pour l’envoi de données de télémétrie concernant les exceptions :

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Les paramètres de propriétés et les mesures sont facultatifs, mais sont utiles pour filtrer et ajouter des informations supplémentaires. Par exemple, si vous avez une application qui peut exécuter plusieurs jeux, vous pouvez rechercher tous les rapports d'exception liés à un jeu particulier. Vous pouvez ajouter autant d'éléments que vous le souhaitez à chaque dictionnaire.

#### <a name="viewing-exceptions"></a>Affichage des exceptions
Vous verrez un résumé des exceptions signalées sur le panneau Vue d’ensemble sur lequel vous pouvez cliquer pour afficher de plus amples informations. Par exemple :

![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)[]

Cliquez sur n’importe quel type d’exception pour voir les occurrences spécifiques :

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)[]

Vous pouvez aussi ouvrir directement Recherche de diagnostic, filtrer sur les exceptions et choisir le type d’exception que vous souhaitez afficher.

### <a name="reporting-unhandled-exceptions"></a>Rapport des exceptions non traitées
Application Insights signale les exceptions non traitées où il peut : sur des appareils, des [navigateurs web][usage] ou des serveurs web à l’aide de [Status Monitor][redfield] du [kit de développement logiciel (SDK) Application Insights][greenbrown]. 

Toutefois, il n’est pas toujours en mesure d’effectuer cette opération. Notamment, lorsque le .NET Framework intercepte les exceptions.  Pour vous assurer que d’afficher toutes les exceptions, vous devez développer un petit gestionnaire d’exceptions. La meilleure procédure à utiliser varie en fonction de la technologie. Pour plus d’informations, consultez [Télémétrie des exceptions pour ASP.NET][exceptions]. 

### <a name="correlating-with-a-build"></a>Corrélation avec un build
Au moment où vous lisez les journaux de diagnostic, il est probable que votre code source ait changé depuis le déploiement du code dynamique.

Il est donc utile de placer des informations de build, telles que l’URL de la version actuelle, dans une propriété, ainsi que chaque exception ou suivi. 

Au lieu d’ajouter la propriété séparément pour chaque appel d’exception, vous pouvez définir les informations dans le contexte par défaut. 

    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }

Dans l'initialiseur de l'application, par exemple Global.asax.cs :

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }

### <a name="requests"></a> Requêtes de serveur web
Les données de télémétrie concernant les requêtes sont envoyées automatiquement lorsque vous [installez Status Monitor sur votre serveur web][redfield] ou lorsque vous [ajoutez Application Insights à votre projet web][greenbrown]. Elles sont également introduites dans les graphiques relatifs aux requêtes et aux temps de réponse dans Metrics Explorer et sur la page de présentation.

Si vous souhaitez envoyer des événements supplémentaires, vous pouvez utiliser l’API TrackRequest().

## <a name="questions"></a>Questions et réponses
### <a name="emptykey"></a>J’obtiens une erreur « La clé d’instrumentation ne peut pas être vide ».
Vous avez peut-être installé le package Nuget de l'enregistreur de données sans installer Application Insights.

Dans l’Explorateur de solutions, cliquez avec le bouton droit sur `ApplicationInsights.config` , puis sélectionnez **Mettre à jour Application Insights**. Une boîte de dialogue vous invite à vous connecter à Azure et à créer une ressource Application Insights, ou à réutiliser une ressource existante. Ceci devrait résoudre le problème.

### <a name="limits"></a>Quelle est la quantité de données conservée ?
Jusqu'à 500 événements par seconde pour chaque application. Les événements sont conservés pendant sept jours.

### <a name="some-of-my-events-or-traces-dont-appear"></a>Certains événements ou certaines traces n’apparaissent pas
Si votre application envoie des données en grand nombre et si vous utilisez le Kit SDK Application Insights pour ASP.NET version 2.0.0-beta3 ou ultérieure, la fonctionnalité d’échantillonnage adaptatif peut fonctionner et transmettre uniquement un pourcentage de vos données de télémétrie. [En savoir plus sur l’échantillonnage.](app-insights-sampling.md)

## <a name="add"></a>Étapes suivantes
* [Configuration des tests de disponibilité et de réactivité][availability]
* [Résolution des problèmes][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md



