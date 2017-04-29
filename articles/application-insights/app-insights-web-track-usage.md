---
title: "Analyse de l’utilisation des applications web avec Azure Application Insights | Microsoft Docs"
description: "Présentation de l’analyse de l’utilisation avec Application Insights"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Analyse de l’utilisation des applications web avec Application Insights
Le fait de savoir comment les personnes utilisent votre application vous permet de concentrer votre travail de développement sur les scénarios les plus importants pour vos utilisateurs et de vous familiariser avec les objectifs qu’ils trouvent plus faciles ou plus difficiles à atteindre.

[Azure Application Insights](app-insights-overview.md) offre deux niveaux de suivi de l’utilisation :

* **Données sur les utilisateurs, les sessions et les pages consultées** - fournies dès le départ.  
* **Télémétrie personnalisée** - Vous [écrivez du code](app-insights-api-custom-events-metrics.md) pour suivre vos utilisateurs via l’expérience utilisateur de votre application. 


## <a name="get-started"></a>Prise en main

La meilleure expérience est obtenue en installant Application Insights à la fois dans votre code serveur d’applications et dans vos pages web. Les composants client et serveur de votre application envoient la télémétrie au portail Azure pour analyse.

1. **Code serveur :** installez le module approprié pour votre [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) ou une [autre](app-insights-platforms.md) application.

    * *Vous ne voulez pas installer de code serveur ? Vous pouvez simplement [créer une ressource Azure Application Insights](app-insights-create-new-resource.md).*

2. **Code de page Web :** ouvrez le [portail Azure](https://portal.azure.com), ouvrez la ressource Application Insights pour votre application, puis ouvrez **Mise en route > Monitor and Diagnose Client-Side (Surveiller et diagnostiquer côté client)**. 

    ![Copiez le script dans l’en-tête de votre page web maître.](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **Obtenir la télémétrie :** exécutez votre projet en mode débogage pendant quelques minutes, puis examinez les résultats dans le panneau Vue d’ensemble dans Application Insights.

    Publiez votre application pour surveiller les performances de votre application et découvrir ce que vos utilisateurs font avec votre application.

## <a name="usage-analysis-out-of-the-box"></a>Analyse de l’utilisation prête à l’emploi
Ouvrez le panneau d’utilisation.

![Graphiques des utilisateurs, sessions et affichages de page](./media/app-insights-web-track-usage/14-usage.png)

Placez le pointeur dans la partie vide au-dessus d'un graphique pour afficher les nombres à un moment donné. Dans le cas contraire, les chiffres indiquent la valeur agrégée sur la période, par exemple une moyenne, un total ou un nombre d’utilisateurs distincts sur la période.

Que montrent ces graphiques ?

* **Utilisateurs :** nombre d'utilisateurs actifs distincts dans la plage de temps du graphique. Dans les applications web, les utilisateurs sont comptés avec les cookies. Une personne qui utilise plusieurs navigateurs, efface les cookies ou utilise la fonctionnalité de confidentialité est comptée plusieurs fois.
* Les **utilisateurs authentifiés** sont comptés si vous avez inséré un appel à [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) dans votre code.
* **Sessions :** nombre de sessions actives. Une session web est comptée après 30 minutes d'inactivité. 
* **Affichages de pages** compte le nombre d’appels à trackPageView(), généralement appelé une seule fois dans chaque page web.

Cliquez sur les graphiques pour plus de détails. Notez que vous pouvez modifier la plage horaire des graphiques.

### <a name="where-do-my-users-live"></a>Où se trouvent mes utilisateurs ?
Cliquez sur une partie vide du graphique Utilisateurs pour ouvrir un autre panneau qui fournit plus de détails :

![Dans le panneau d’utilisation, cliquez sur le graphique Utilisateurs](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>Quels sont les systèmes d’exploitation et les navigateurs qu’ils utilisent ?

Cliquez sur **Modifier** dans le graphique Utilisateurs et regroupez (segmentez) les données en fonction d’une propriété, comme le navigateur, le système d’exploitation ou la ville : 

![Sélectionnez un graphique qui affiche une seule mesure, passez sur le regroupement et choisissez une propriété](./media/app-insights-web-track-usage/03-browsers.png)

Pour visualiser l’ensemble des chiffres, passez au type de graphique **Grille**. Vous pouvez également choisir d’afficher des mesures supplémentaires :

![Graphique de grille à plusieurs colonnes](./media/app-insights-web-track-usage/multi-column-grid.png)

Pour les types de graphiques, vous pouvez regrouper par propriété ou sélectionner plusieurs mesures, mais pas les deux. Ce graphique compare deux mesures, les utilisateurs et les [utilisateurs authentifiés](app-insights-api-custom-events-metrics.md#authenticated-users). 

![Sélectionnez un graphique, recherchez des mesures et activez les mesures ou désactivez-les.](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>Affichages de page
Dans le panneau Utilisation, cliquez sur le graphique des affichages de pages pour obtenir une version plus détaillée avec une répartition des pages les plus populaires :

![Dans le panneau Vue d'ensemble, cliquez sur le graphique des pages vues.](./media/app-insights-web-track-usage/05-games.png)

L'exemple ci-dessus vient d’un site web de jeux. Dans les graphiques, nous pouvons voir instantanément :

* L'utilisation ne s'est pas améliorée au cours de la semaine écoulée. Peut-être que nous devrions envisager une optimisation pour les moteurs de recherche ?
* Tennis est la page de jeu la plus populaire. Concentrons-nous sur d’autres améliorations de cette page.
* En moyenne, les utilisateurs visitent la page Tennis environ trois fois par semaine. (Il y a près de trois fois plus de sessions que d’utilisateurs.)
* La plupart des utilisateurs visitent le site au cours de la semaine de travail aux États-Unis, et pendant les heures de travail. Il serait peut-être envisageable de fournir un bouton de masquage rapide sur la page web.
* Les [annotations](app-insights-annotations.md) sur le graphique montrent à quel moment les nouvelles versions du site web ont été déployées. Aucun des déploiements récents n’a eu d’effet notable sur l’utilisation.

## <a name="custom-tracking"></a>Suivi personnalisé
Supposons qu'au lieu d'implémenter chaque jeu sur une page web distincte, vous décidiez les placer dans une seule application sur une page unique, avec la plupart des fonctionnalités codées en Javascript dans la page web. Cela permet à l'utilisateur de passer rapidement d'un jeu à l'autre ou même d'avoir plusieurs jeux sur une seule page.

Mais vous voulez qu'Application Insights continue de consigner le nombre de fois où chaque jeu est ouvert, de la même façon que lorsqu'ils se trouvaient sur des pages web distinctes. C'est très simple : insérez simplement un appel au module de télémétrie dans votre code JavaScript où vous souhaitez enregistrer l'ouverture d'une « page » :

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
Cet appel simule la télémétrie qui journalise un affichage de page.  Toutefois, vous ne souhaitez pas toujours mélanger les messages avec les affichages de page. Utilisez plutôt des événements personnalisés. Vous pouvez les envoyer à partir de pages web ou d’un serveur web :


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

Les [données de télémétrie personnalisées insérées dans votre code de serveur ou web ](app-insights-api-custom-events-metrics.md#trackevent) peuvent être utilisées de différentes manières pour comprendre comment votre application est utilisée.

Pour afficher les événements envoyés par TrackEvent() : ouvrez Metrics Explorer, ajoutez un nouveau graphique et modifiez-le. Vos mesures apparaissent sous Mesures personnalisées. 

![Graphique affichant les événements personnalisés](./media/app-insights-web-track-usage/06-eventsSegment.png)

Si vous définissez [les valeurs de propriété](app-insights-api-custom-events-metrics.md#properties) (également appelées dimensions) dans vos événements, vous pouvez les regrouper et filtrer en fonction de celles-ci.

Créez plusieurs graphiques pour mettre en corrélation les modifications dans les autres événements et mesures. Par exemple, lorsque plusieurs jeux sont lancés, vous vous attendez à voir aussi une hausse du nombre de jeux abandonnés. Mais l'augmentation des jeux abandonnés est disproportionnée, vous souhaitez savoir si la charge élevée est à l'origine de problèmes que les utilisateurs considèrent inacceptables.

## <a name="drill-into-specific-events"></a>Explorer des événements spécifiques
Pour une meilleure compréhension de la session type, vous pouvez vous concentrer sur une session utilisateur qui contient un type particulier d'événement.

Dans une grille d’événements, cliquez sur l'événement qui vous intéresse, puis sélectionnez une occurrence spécifique récente :

![Dans la liste sous le graphique de synthèse, cliquez sur un événement.](./media/app-insights-web-track-usage/08-searchEvents.png)

Voyons toute la télémétrie de la session dans laquelle cet événement NoGame particulier s'est produit.

![Cliquez sur toute la télémétrie de la session](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

Il n'y avait pas d'exceptions, l'utilisateur n'a pas été bloqué dans son jeu par un quelconque échec.

Nous pouvons filtrer tous les types de télémétrie, à l'exception des affichages de page pour cette session :

![](./media/app-insights-web-track-usage/10-filter.png)

Et nous pouvons voir que cet utilisateur s'est connecté pour tout simplement voir les derniers résultats. Peut-être devrions envisager de développer un parcours utilisateur qui facilite cette opération. (Et nous devrons alors mettre en place un événement personnalisé pour signaler ce qui se produit quand ce parcours est activé.)

## <a name="filter-search-and-segment-your-data-with-properties"></a>Filtrage, recherche et segmentation des données grâce à des propriétés
Vous pouvez joindre des balises arbitraires et des valeurs numériques aux événements.

*JavaScript dans une page web*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# côté serveur*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB côté serveur*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Vous pouvez joindre des propriétés à des affichages de pages de la même manière :

*JavaScript dans une page web*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Dans la recherche de diagnostic, affichez les propriétés en cliquant sur via les occurrences d'un événement.

![Dans la liste des événements, ouvrez un événement, puis cliquez sur « ... » pour voir d’autres propriétés](./media/app-insights-web-track-usage/11-details.png)

Utilisez le champ de recherche pour voir les occurrences de l'événement présentant une valeur de propriété particulière.

![Tapez une valeur dans le champ de recherche](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>Modifier et créer des requêtes via vos données de télémétrie

Cliquez sur l’icône Azure Analytics dans n’importe quel graphique pour ouvrir une requête équivalente que vous pouvez modifier.
Faites défiler pour voir s’il existe d’autres requêtes générées. Placez le curseur sur n’importe quelle requête et cliquez sur **OK**. 

Autrement, ouvrez Analytics à partir de l’icône dans le panneau Vue d’ensemble et écrivez vos propres requêtes, ou essayez certains exemples de requêtes sous l’onglet Accueil d’Analytics.


![Fenêtre Analytics avec la requête générée](./media/app-insights-web-track-usage/open-analytics.png)

[En savoir plus sur le langage de requête Azure Analytics](app-insights-analytics.md).

Pour l’analyse de l’utilisation, vous serez peut-être très intéressé par ces tables :

* `customEvents` - Résultats des appels [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).
* `pageViews` - Nombre de pages ouvertes dans les navigateurs clients ou d’appels à [trackPageView()](app-insights-api-custom-events-metrics.md#page-views).


## <a name="a--b-testing"></a>Test A | B
Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des balises à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard.

Dans le portail Application Insights, vous pourrez ensuite filtrer et regrouper (segmenter) vos données sur les balises, afin de comparer les différentes versions.


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

La télémétrique individuelle peut remplacer les valeurs par défaut.

Vous pouvez configurer un initialiseur universel afin que tous les TelemetryClients utilisent automatiquement votre contexte.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Dans l'initialiseur de l'application, par exemple Global.asax.cs :

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>Créer, mesurer, apprendre
Lorsque vous utilisez l'analyse, elle devient partie intégrante de votre cycle de développement, et plus uniquement un élément auquel vous avez recours pour résoudre les problèmes. Voici quelques conseils :

* Déterminez la mesure clé de votre application. Voulez-vous le plus d'utilisateurs possible ou préférez un petit groupe d'utilisateurs heureux ? Voulez-vous optimiser les visites ou les ventes ?
* Prévoyez de mesurer chaque parcours. Lorsque vous concevez un nouveau parcours utilisateur ou une nouvelle fonctionnalité ou que vous appliquez une mise à jour, envisagez toujours comment vous allez mesurer la réussite de cet élément. Avant de commencer à créer votre code, posez-vous la question « Quel effet cela aura sur nos mesures, si ça fonctionne ? Est-ce que nous devons suivre tous les nouveaux événements ?
  » Et bien sûr, une fois la fonctionnalité active, veillez à examiner l'analyse et agir en fonction des résultats.
* Faites le lien entre la mesure clé et les autres mesures. Par exemple, si vous ajoutez une fonctionnalité de favoris, vous souhaitez connaître la fréquence à laquelle les utilisateurs ajoutent des favoris. Mais il est peut-être plus intéressant de savoir avec quelle fréquence ils reviennent à leurs favoris. Et, plus important encore, les clients qui utilisent les favoris achètent-ils plus votre produit ?
* Test du canari. Configurez une fonctionnalité qui ne sera visible que pour certains utilisateurs. Utilisez Application Insights pour voir si cette nouvelle fonctionnalité est utilisée comme vous l'aviez envisagé. Apportez les ajustements nécessaires, puis publiez-la pour un public plus large.
* Parlez à vos utilisateurs ! L'analyse en soi n'est pas suffisante, mais elle vient compléter une bonne relation client.

## <a name="learn-more"></a>En savoir plus
* [Détecter, trier et diagnostiquer les pannes et les problèmes de performances dans votre application](app-insights-detect-triage-diagnose.md)
* [Prise en main d’Application Insights sur de nombreuses plateformes](app-insights-detect-triage-diagnose.md)
* [Utilisation de l’API : vue d’ensemble](app-insights-api-custom-events-metrics.md)
* [Référence de l’API JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



