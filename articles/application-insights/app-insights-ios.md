<properties
    pageTitle="Application Insights pour les applications iOS | Microsoft Azure"
    description="Analysez l’utilisation et les performances de votre application iOS avec Application Insights."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="ronmart"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/27/2015"
    ms.author="awills"/>

# Application Insights pour les applications iOS

Visual Studio Application Insights vous permet d’analyser l’utilisation, les événements et les incidents de votre application mobile.

## Configuration requise

Vous devez avoir :

* Un abonnement à [Microsoft Azure](http://azure.com). Vous vous connectez avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft.
* Xcode 6 ou version ultérieure.
* Le Kit de développement logiciel (SDK) s’exécute sur les appareils iOS 6.0 ou version ultérieure.

## Création d’une ressource Application Insights dans Azure

Dans le [portail Azure][portal], créez une ressource Application Insights. Choisissez l’option iOS.

![Cliquez sur Nouveau, Services de développement, Application Insights](./media/app-insights-ios/11-new.png)

Dans le panneau qui s’ouvre, vous trouverez des données relatives à l’utilisation et aux performances de votre application. Vous devriez trouver une vignette sur l'écran d'accueil pour accéder à ces informations, la prochaine fois que vous vous connecterez à Azure. Sinon, cliquez sur Parcourir.

## Téléchargez Application Insights pour Mac

(Si vous ne l’avez pas encore fait.)

1. Téléchargez [Application Insights pour Mac](http://go.microsoft.com/fwlink/?LinkID=533209).

2. Extrayez le fichier zip.

3. Cliquez sur l’icône de l’application pour démarrer Application Insights pour Mac.

## <a name="signin"></a>Connexion à Azure

1. Cliquez sur **Se connecter**.

2. Connectez-vous à votre compte Azure.

## Installation du Kit de développement logiciel (SDK) dans votre application

1. Cliquez sur **Intégrer** pour démarrer l’intégration du Kit de développement logiciel (SDK).

2. Sélectionnez votre projet Xcode dans la liste ou cliquez sur **Ouvrir autre** pour trouver votre projet, puis cliquez sur **Intégrer**.

3. Choisissez le dossier pour le Kit de développement logiciel (SDK) Application Insights, puis cliquez sur **Installer**.

4. Ajoutez ce script d’exécution à vos phases de génération : [Ajout d’une phase de script d’exécution](http://hockeyapp.net/help/runscriptbuildphase/).

5. Ajoutez les infrastructures manquantes à votre projet Xcode.

6. Faites glisser l’infrastructure Application Insights dans votre projet Xcode, puis cliquez sur **Suivant**.

7. Sélectionnez **Intégrer le Kit de développement logiciel (SDK) dans cible** pour votre cible.

8. Cliquez sur **Créer un nouveau composant** pour créer votre application dans le portail Application Insights.

9. Sélectionnez votre abonnement, le groupe de ressources et entrez un nom de composant. Dans la plupart des cas, il doit correspondre au nom de votre application. Confirmez avec le bouton **Créer une ressource**.

10. Assurez-vous que le composant approprié est sélectionné, puis cliquez sur **Suivant**.

11. Modifiez votre code source comme indiqué dans l’Assistant, puis cliquez sur **Terminer**.

12. Lancez votre application dans le simulateur iOS avec **Générer et exécuter**.

## Insertion d’appels de télémétrie

Dès lors que `[MSAIApplicationInsights start]` est appelé, le Kit de développement logiciel (SDK) démarre le suivi des sessions, des affichages de pages, ainsi que des incidents et des exceptions non gérées.

Vous pouvez ajouter des événements supplémentaires comme suit :

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric"
                                        value:42.2];

## Affichage de vos données dans Application Insights

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

<!---HONumber=Oct15_HO3-->