---
title: "Analyse de l’utilisation des applications web avec Application Insights"
description: "Présentation de l’analyse de l’utilisation des applications web avec Application Insights"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/12/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 8e32014147c322e09af08e5c05d83aea13041a4c
ms.lasthandoff: 04/07/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Analyse de l’utilisation des applications web avec Application Insights
Le fait de savoir comment les personnes utilisent votre application vous permet de concentrer votre travail de développement sur les scénarios les plus importants et de vous familiariser avec les objectifs que les utilisateurs trouvent plus faciles ou plus difficiles à atteindre. 

Azure Application Insights offre deux niveaux de suivi de l'utilisation :

* **Données sur les utilisateurs, les sessions et les pages consultées** - fournies dès le départ.  
* **Télémétrie personnalisée** - Vous [écrivez du code][api] pour suivre vos utilisateurs via l’expérience utilisateur de votre application. 

## <a name="setting-up"></a>Configuration
Ouvrez une ressource Application Insights dans le [portail Azure](https://portal.azure.com), un clic sur la page vide du navigateur charge un graphique. Suivez les instructions d'installation.

[En savoir plus](app-insights-javascript.md) 

## <a name="how-popular-is-my-web-application"></a>Quel est le niveau de popularité de mon application web ?
Connectez-vous au [portail Azure][portal], accédez à votre ressource d’application, puis cliquez sur Utilisation :

![](./media/app-insights-web-track-usage/14-usage.png)

* **Utilisateurs :** nombre d'utilisateurs actifs distincts dans la plage de temps du graphique. 
* **Sessions :** nombre de sessions actives
* **Affichages de pages** compte le nombre d’appels à trackPageView(), généralement appelé une seule fois dans chaque page web.

Cliquez sur les graphiques pour plus de détails. Notez que vous pouvez modifier la plage horaire des graphiques.

### <a name="where-do-my-users-live"></a>Où se trouvent mes utilisateurs ?
Dans le panneau d’utilisation, cliquez sur le graphique Utilisateurs pour plus de détails :

![Dans le panneau d’utilisation, cliquez sur le graphique Utilisateurs](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>Quels sont les systèmes d’exploitation et les navigateurs qu’ils utilisent ?
Regroupez (segmentez) les données par une propriété, comme le navigateur, le système d'exploitation ou la ville :

![Sélectionnez un graphique qui affiche une seule mesure, passez sur le regroupement et choisissez une propriété](./media/app-insights-web-track-usage/03-browsers.png)

## <a name="sessions"></a>Sessions
Une session est un concept fondamental dans Application Insights, qui s'efforce d'associer chaque événement de télémétrie, tels que les demandes, les vues de page, les exceptions ou des événements personnalisés que vous codez vous-même, avec une session utilisateur spécifique. 

Les informations contextuelles sont collectées pour chaque session, par exemple les caractéristiques du périphérique, la situation géographique, le système d'exploitation, etc.

Si vous instrumentalisez à la fois le client et le serveur ([ASP.NET][greenbrown] ou [J2EE][java]), les SDK propageront l’ID de session entre le client et le serveur afin que les événements des deux côtés puissent être liés.

Lors du [diagnostic de problèmes][diagnostic], vous pouvez trouver toutes les données de télémétrie liées à la session dans laquelle un problème s’est produit, y compris l’ensemble des demandes, événements, exceptions ou traces qui ont été enregistrés.

Les sessions fournissent une bonne indication de la popularité des contextes, par exemple le périphérique, le système d'exploitation ou l’emplacement. En affichant le nombre de sessions regroupées par périphérique, par exemple, vous obtenez un nombre plus précis de la fréquence à laquelle ce périphérique est utilisé avec votre application, plutôt qu’en comptant le nombre de pages affichées. C’est une méthode efficace pour trier les éventuels problèmes spécifiques à un périphérique.

#### <a name="whats-a-session"></a>Qu'est-ce qu’une session ?
Une session représente une rencontre unique entre l'utilisateur et l'application. Dans sa forme la plus simple, la session commence par un utilisateur qui lance l'application et se termine lorsque l'utilisateur quitte l'application. Pour les applications web, par défaut, la session se termine après 30 minutes d'inactivité ou après 24 heures d'activité. 

Vous pouvez changer ces valeurs par défaut en modifiant l'extrait de code :

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });
    </script>

* `sessionRenewalMs` : la durée, en millisecondes, avant expiration de la session en raison de l'inactivité de l'utilisateur. Par défaut : 30 minutes.
* `sessionExpirationMs` : la longueur maximale d'une session, en millisecondes. Si l'utilisateur reste actif après ce délai, une autre session est comptée. Par défaut : 24 heures.

La **durée de la session** est une [métrique][metrics] qui enregistre l’intervalle de temps entre le premier et le dernier élément de télémétrie de la session. (Elle n'inclut pas le délai d'expiration).

**nombre de sessions** d’un certain intervalle est défini comme le nombre de sessions uniques avec une certaine activité pendant cet intervalle. Lorsque vous examinez une longue période comme le nombre de sessions quotidiennes de la semaine dernière, cette valeur équivaut généralement au nombre total de sessions. 

Toutefois, lorsque vous explorez des périodes plus courtes comme une session horaire, une longue session couvrant plusieurs heures est comptée pour chaque heure pendant laquelle la session a été active. 

## <a name="users-and-user-counts"></a>Utilisateurs et nombre d'utilisateurs
Chaque session utilisateur est associée à un ID d’utilisateur unique. 

Par défaut, l'utilisateur est identifié en plaçant un cookie. Un utilisateur qui utilise plusieurs navigateurs ou périphériques sera compté plus d’une fois. (Mais consultez [Utilisateurs authentifiés](#authenticated-users))

La métrique du **nombre d'utilisateurs** dans un certain intervalle est définie comme le nombre d'utilisateurs uniques avec des activités enregistrées pendant cet intervalle. Par conséquent, les utilisateurs avec de longues sessions peuvent être pris en compte plusieurs fois, lorsque vous définissez une plage de temps afin que la granularité soit inférieure à une heure environ.

**Nouveaux utilisateurs** compte les utilisateurs dont les premières sessions avec l'application se sont produites au cours de cet intervalle. Si la méthode par défaut de comptabilisation par utilisateurs et par cookies est utilisée, elle inclura également les utilisateurs qui ont supprimé leurs cookies ou qui utilisent un nouveau périphérique ou navigateur pour accéder à votre application pour la première fois.
![Dans le panneau d’utilisation, cliquez sur le graphique Utilisateurs pour examiner les nouveaux utilisateurs.](./media/app-insights-web-track-usage/031-dual.png)

### <a name="authenticated-users"></a>Utilisateurs authentifiés
Si votre application web permet aux utilisateurs de se connecter, vous pouvez obtenir un nombre plus précis en fournissant à Application Insights un identificateur d’utilisateur unique. Il ne s’agit pas nécessairement de leur nom ou du même ID que vous utilisez dans votre application. Dès que votre application a identifié l'utilisateur, utilisez ce code :

*JavaScript côté client*

      appInsights.setAuthenticatedUserContext(userId);

Si votre application regroupe les utilisateurs par comptes, vous pouvez également fournir un identificateur pour ce compte. 

      appInsights.setAuthenticatedUserContext(userId, accountId);

Les ID utilisateur et de compte ne doivent pas contenir des espaces ou les caractères `,;=|`

Dans [Metrics Explorer](app-insights-metrics-explorer.md), vous pouvez créer un graphique des **Utilisateurs authentifiés** et des **Comptes**. 

## <a name="synthetic-traffic"></a>Trafic synthétique
Le trafic synthétique inclut les requêtes des tests de disponibilité et de charge, les robots d’indexation et autres agents. 

Application Insights essaie de déterminer et de classer automatiquement le trafic synthétique afin de le marquer de manière appropriée. Dans la plupart des cas, le trafic synthétique n'appelle pas le SDK JavaScript afin que cette activité soit exclue du calcul du nombre d’utilisateurs et de sessions. 

Toutefois, pour les [tests web][availability] Application Insights, l’ID d’utilisateur est automatiquement défini en fonction de l’emplacement POP, et un ID de session est défini en fonction de l’ID d’exécution des tests. Dans les rapports par défaut, le trafic synthétique est filtré et exclut ces utilisateurs et sessions. Mais lorsque le trafic synthétique est inclus, il peut provoquer une légère augmentation du nombre global d’utilisateurs et de sessions.

## <a name="page-usage"></a>Utilisation des pages
Cliquez sur le graphique des affichages de pages pour obtenir une version plus détaillée avec une répartition des pages les plus populaires :

![Dans le panneau Vue d'ensemble, cliquez sur le graphique des pages vues.](./media/app-insights-web-track-usage/05-games.png)

L'exemple ci-dessus vient d'un site web de jeux. Nous constatons instantanément que :

* L'utilisation ne s'est pas améliorée au cours de la semaine écoulée. Peut-être que nous devrions envisager une optimisation pour les moteurs de recherche ?
* Les personnes qui voient les jeux sont bien moins nombreuses que celles qui visitent la page d'accueil. Pourquoi notre page d'accueil n'attire-t-elle pas les utilisateurs sur les jeux ?
* « Crossword » est le jeu le plus populaire. Nous devons donner la priorité à de nouvelles améliorations et de nouvelles idées.

## <a name="custom-tracking"></a>Suivi personnalisé
Supposons qu'au lieu d'implémenter chaque jeu sur une page web distincte, vous décidiez les placer dans une seule application sur une page unique, avec la plupart des fonctionnalités codées en Javascript dans la page web. Cela permet à l'utilisateur de passer rapidement d'un jeu à l'autre ou même d'avoir plusieurs jeux sur une seule page. 

Mais vous voulez qu'Application Insights continue de consigner le nombre de fois où chaque jeu est ouvert, de la même façon que lorsqu'ils se trouvaient sur des pages web distinctes. C'est très simple : insérez simplement un appel au module de télémétrie dans votre code JavaScript où vous souhaitez enregistrer l'ouverture d'une « page » :

    appInsights.trackPageView(game.Name);

## <a name="custom-events"></a>Événements personnalisés
Écrivez une télémétrie personnalisée pour consigner des événements spécifiques. En particulier dans une application à page unique, vous pourrez connaître la fréquence à laquelle l’utilisateur effectue des actions particulières ou atteint certains objectifs : 

    appInsights.trackEvent("GameEnd");

Par exemple, pour ouvrir une session en cliquant sur un lien :

    <a href="target.htm" onclick="appInsights.trackEvent('linkClick');return true;">my link</a>


## <a name="view-counts-of-custom-events"></a>Nombre d’événements personnalisés affichés
Ouvrez Metrics Explorer et ajoutez un graphique pour afficher les événements. Segment par nom :

![Sélectionnez un graphique qui affiche une seule mesure. Basculez sur le regroupement. Sélectionnez une propriété. Toutes les propriétés ne sont pas disponibles.](./media/app-insights-web-track-usage/06-eventsSegment.png)

## <a name="drill-into-specific-events"></a>Explorer des événements spécifiques
Pour une meilleure compréhension de la session type, vous pouvez vous concentrer sur une session utilisateur qui contient un type particulier d'événement. 

Dans cet exemple, nous avons codé un événement personnalisé « NoGame » qui est appelé si l'utilisateur se déconnecte sans lancer de jeu. Pourquoi l'utilisateur fait-il ça ? Peut-être que si nous explorons certaines occurrences spécifiques, nous obtiendrons des indices. 

Les événements personnalisés provenant de l'application sont répertoriés par nom dans le panneau Vue d'ensemble :

![Dans le panneau Vue d'ensemble, cliquez sur un des types d'événements personnalisés.](./media/app-insights-web-track-usage/07-clickEvent.png)

Cliquez sur l'événement qui vous intéresse, puis sélectionnez une occurrence spécifique récente :

![Dans la liste sous le graphique de synthèse, cliquez sur un événement.](./media/app-insights-web-track-usage/08-searchEvents.png)

Voyons toute la télémétrie de la session dans laquelle cet événement NoGame particulier s'est produit. 

![Cliquez sur toute la télémétrie de la session](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

Il n'y avait pas d'exceptions, l'utilisateur n'a pas été bloqué dans son jeu par un quelconque échec.

Nous pouvons filtrer tous les types de télémétrie, à l'exception des affichages de page pour cette session :

![](./media/app-insights-web-track-usage/10-filter.png)

Et nous pouvons voir que cet utilisateur s'est connecté pour tout simplement voir les derniers résultats. Peut-être devrions envisager de développer un parcours utilisateur qui facilite cette opération. (Et nous devrons alors mettre en place un événement personnalisé pour signaler ce qui se produit quand ce parcours est activé.)

## <a name="filter-search-and-segment-your-data-with-properties"></a>Filtrage, recherche et segmentation des données grâce à des propriétés
Vous pouvez joindre des balises arbitraires et des valeurs numériques aux événements.

*JavaScript côté client*

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

*JavaScript côté client*

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

## <a name="a--b-testing"></a>Test A | B
Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des balises à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard. 

Dans le portail Application Insights, vous pourrez ensuite filtrer et regrouper (segmenter) vos données sur les balises, afin de comparer les différentes versions.

*C# côté serveur*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*VB côté serveur*

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
* Faites le lien entre la mesure clé et les autres mesures. Par exemple, si vous ajoutez une fonctionnalité de « favoris », vous souhaitez connaître la fréquence à laquelle les utilisateurs ajoutent des favoris. Mais il est peut-être plus intéressant de savoir avec quelle fréquence ils reviennent à leurs favoris. Et, plus important encore, les clients qui utilisent les favoris achètent-ils plus votre produit ?
* Test du canari. Configurez une fonctionnalité qui ne sera visible que pour certains utilisateurs. Utilisez Application Insights pour voir si cette nouvelle fonctionnalité est utilisée comme vous l'aviez envisagé. Apportez les ajustements nécessaires, puis publiez-la pour un public plus large.
* Parlez à vos utilisateurs ! L'analyse en soi n'est pas suffisante, mais elle vient compléter une bonne relation client.

## <a name="references"></a>Références
* [Utilisation de l’API : vue d’ensemble][api]
* [Référence de l’API JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md



