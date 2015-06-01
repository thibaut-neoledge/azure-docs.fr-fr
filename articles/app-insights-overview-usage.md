<properties 
	pageTitle="Analyse de l'utilisation avec Application Insights" 
	description="Présentation de l'analyse de l'utilisation avec Application Insights" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/03/2015" 
	ms.author="awills"/>
 
# Analyse de l'utilisation avec Application Insights

Le fait de savoir comment les personnes utilisent votre application vous permet de concentrer votre travail de développement sur les scénarios les plus importants et de vous familiariser avec les objectifs que les utilisateurs trouvent plus faciles ou plus difficiles à atteindre.

Application Insights peut offrir un aperçu clair de l'utilisation de votre application, vous aider à améliorer l'expérience des utilisateurs et atteindre vos objectifs.

## Analyse prête à l'emploi
 
Ajoutez [Application Insights][start] à votre projet et sans aucun effort supplémentaire, vous obtenez des graphiques vous indiquant le nombre d'utilisateurs et bien plus encore.

![Dans Azure, sélectionnez Parcourir > Application Insights > votre projet, puis faites défiler vers le bas.](./media/app-insights-overview-usage/01-overview.png)
 
Placez le pointeur dans la partie vide au-dessus d'un graphique pour afficher les nombres à un moment donné. Dans le cas contraire, les chiffres indiquent la valeur agrégée sur la période, par exemple une moyenne, un total ou le nombre d'utilisateurs distincts sur la période.

Dans les applications web, les utilisateurs sont comptés avec les cookies. Une personne qui utilise plusieurs navigateurs, efface les cookies ou utilise la fonctionnalité de confidentialité est comptée plusieurs fois.

Une session web est comptée après 30 minutes d'inactivité. Une session sur un téléphone ou un autre appareil est comptée lorsque l'application est interrompue pendant plus de quelques secondes.

Cliquez sur n'importe quel graphique pour afficher plus de détails. Par exemple :

![Dans le panneau Vue d'ensemble, cliquez sur le graphique des sessions.](./media/app-insights-overview-usage/02-sessions.png)
 
(Cet exemple est issu d'un site web, mais les graphiques sont similaires pour les applications qui s'exécutent sur des appareils).

Comparez avec la semaine précédente pour voir si les choses évoluent :

![Sélectionnez un graphique qui affiche une seule mesure, affichez la semaine précédente](./media/app-insights-overview-usage/021-prior.png)

Comparez deux mesures, par exemple les utilisateurs et les nouveaux utilisateurs :

![Sélectionnez un graphique, recherchez des mesures et activez les mesures ou désactivez-les.](./media/app-insights-overview-usage/031-dual.png)

Regroupez (segmentez) les données par une propriété, comme le navigateur, le système d'exploitation ou la ville :

![Sélectionnez un graphique qui affiche une seule mesure, passez sur le regroupement et choisissez une propriété](./media/app-insights-overview-usage/03-browsers.png)

 
## Utilisation des pages

Cliquez sur le graphique des affichages de pages pour obtenir une version plus détaillée avec une répartition des pages les plus populaires :


![Dans le panneau Vue d'ensemble, cliquez sur le graphique des pages vues.](./media/app-insights-overview-usage/05-games.png)
 
L'exemple ci-dessus vient d'un site web de jeux. Nous constatons instantanément que :

* L'utilisation ne s'est pas améliorée au cours de la semaine écoulée. Peut-être que nous devrions envisager une optimisation pour les moteurs de recherche ?
* Les personnes qui voient les jeux sont bien moins nombreuses que celles qui visitent la page d'accueil. Pourquoi notre page d'accueil n'attire-t-elle pas les utilisateurs sur les jeux ?
* « Crossword » est le jeu le plus populaire. Nous devons donner la priorité à de nouvelles améliorations et de nouvelles idées.

## Suivi personnalisé

Supposons qu'au lieu d'implémenter chaque jeu sur une page web distincte, vous décidiez les placer dans une seule application sur une page unique, avec la plupart des fonctionnalités codées en Javascript dans la page web. Cela permet à l'utilisateur de passer rapidement d'un jeu à l'autre ou même d'avoir plusieurs jeux sur une seule page.

Mais vous voulez qu'Application Insights continue de consigner le nombre de fois où chaque jeu est ouvert, de la même façon que lorsqu'ils se trouvaient sur des pages web distinctes. C'est très simple : insérez simplement un appel au module de télémétrie dans votre code JavaScript où vous souhaitez enregistrer l'ouverture d'une « page » :

	telemetryClient.trackPageView(game.Name);

## Événements personnalisés

Vous pouvez utiliser la télémétrie de différentes manières pour comprendre l'utilisation de votre application. Mais vous ne voulez pas mélanger les messages avec les affichages de page. Utilisez plutôt des événements personnalisés. Vous pouvez les envoyer à partir d'applications de l'appareil, des pages web ou du serveur web :

(JavaScript)

    telemetryClient.trackEvent("GameEnd");

(C#)

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

(VB)

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


Les événements personnalisés les plus fréquents sont répertoriés dans le panneau Vue d'ensemble.

![Dans le panneau Vue d'ensemble, faites défiler vers le bas et cliquez sur les événements personnalisés.](./media/app-insights-overview-usage/04-events.png)

Cliquez sur l'en-tête de la table pour voir le nombre total d'événements. Vous pouvez segmenter le graphique en fonction de divers attributs, comme le nom de l'événement :

![Sélectionnez un graphique qui affiche une seule mesure. Basculez sur le regroupement. Sélectionnez une propriété. Toutes les propriétés ne sont pas disponibles.](./media/app-insights-overview-usage/06-eventsSegment.png)

La chronologie est une fonctionnalité particulièrement utile avec laquelle vous pouvez mettre en corrélation les modifications avec d'autres mesures et d'autres événements. Par exemple, lorsque plusieurs jeux sont lancés, vous vous attendez à voir aussi une hausse du nombre de jeux abandonnés. Mais l'augmentation des jeux abandonnés est disproportionnée, vous souhaitez savoir si la charge élevée est à l'origine de problèmes que les utilisateurs considèrent inacceptables.

## Explorer des événements spécifiques

Pour une meilleure compréhension de la session type, vous pouvez vous concentrer sur une session utilisateur qui contient un type particulier d'événement.

Dans cet exemple, nous avons codé un événement personnalisé « NoGame » qui est appelé si l'utilisateur se déconnecte sans lancer de jeu. Pourquoi l'utilisateur fait-il ça ? Peut-être que si nous explorons certaines occurrences spécifiques, nous obtiendrons des indices.

Les événements personnalisés provenant de l'application sont répertoriés par nom dans le panneau Vue d'ensemble :


![Dans le panneau Vue d'ensemble, cliquez sur un des types d'événements personnalisés.](./media/app-insights-overview-usage/07-clickEvent.png)
 
Cliquez sur l'événement qui vous intéresse, puis sélectionnez une occurrence spécifique récente :


![Dans la liste sous le graphique de synthèse, cliquez sur un événement.](./media/app-insights-overview-usage/08-searchEvents.png)
 
Voyons toute la télémétrie de la session dans laquelle cet événement NoGame particulier s'est produit.


![Cliquez sur toute la télémétrie de la session](./media/app-insights-overview-usage/09-relatedTelemetry.png)
 
Il n'y avait pas d'exceptions, l'utilisateur n'a pas été bloqué dans son jeu par un quelconque échec.
 
Nous pouvons filtrer tous les types de télémétrie, à l'exception des affichages de page pour cette session :


![](./media/app-insights-overview-usage/10-filter.png)
 
Et nous pouvons voir que cet utilisateur s'est connecté pour tout simplement voir les derniers résultats. Peut-être devrions envisager de développer un parcours utilisateur qui facilite cette opération. (Et nous devrons alors mettre en place un événement personnalisé pour signaler ce qui se produit quand ce parcours est activé.)

## Filtrage, recherche et segmentation des données grâce à des propriétés
Vous pouvez joindre des balises arbitraires et des valeurs numériques aux événements.
 

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

Vous pouvez joindre des propriétés à des affichages de pages de la même manière :

JavaScript côté client

    appInsights.trackPageView("Win", 
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});

Dans la recherche de diagnostic, affichez les propriétés en cliquant sur via les occurrences d'un événement.


![Dans la liste des événements, ouvrez un événement, puis cliquez sur « ... » pour voir d'autres propriétés](./media/app-insights-overview-usage/11-details.png)
 
Utilisez le champ de recherche pour voir les occurrences de l'événement présentant une valeur de propriété particulière.


![Tapez une valeur dans le champ de recherche](./media/app-insights-overview-usage/12-searchEvents.png)


## Test A | B

Si vous ne savez pas quelle variante de la fonctionnalité sera la plus efficace, publiez les deux versions et mettez-les à disposition de différents utilisateurs. Mesurer le taux de réussite de chaque version et créez-en une version unifiée.

Pour cette technique, vous joignez des balises à toute la télémétrie envoyée par chaque version de votre application. Pour cela, vous définissez des propriétés dans le contexte TelemetryContext actif. Ces propriétés par défaut sont ajoutées à chaque message de télémétrie que l'application envoie, non seulement vos messages personnalisés, mais aussi la télémétrie standard.

Dans le portail Application Insights, vous pourrez ensuite filtrer et regrouper (segmenter) vos données sur les balises, afin de comparer les différentes versions.

C# côté serveur

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");

VB côté serveur

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")

La télémétrique individuelle peut remplacer les valeurs par défaut.

Vous pouvez configurer un initialiseur universel afin que tous les TelemetryClients utilisent automatiquement votre contexte.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

Dans l'initialiseur de l'application, par exemple Global.asax.cs :

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }


## Créer, mesurer, apprendre

Lorsque vous utilisez l'analyse, elle devient partie intégrante de votre cycle de développement, et plus uniquement un élément auquel vous avez recours pour résoudre les problèmes. Voici quelques conseils :

* Déterminez la mesure clé de votre application. Voulez-vous le plus d'utilisateurs possible ou préférez un petit groupe d'utilisateurs heureux ? Voulez-vous optimiser les visites ou les ventes ?
* Prévoyez de mesurer chaque parcours. Lorsque vous concevez un nouveau parcours utilisateur ou une nouvelle fonctionnalité ou que vous appliquez une mise à jour, envisagez toujours comment vous allez mesurer la réussite de cet élément. Avant de commencer à créer votre code, posez-vous la question « Quel effet cela aura sur nos mesures, si ça fonctionne ? Est-ce que nous devons suivre tous les nouveaux événements ? » Et bien sûr, une fois la fonctionnalité active, veillez à examiner l'analyse et agir en fonction des résultats. 
* Faites le lien entre la mesure clé et les autres mesures. Par exemple, si vous ajoutez une fonctionnalité de favoris, vous souhaitez connaître la fréquence à laquelle les utilisateurs ajoutent des favoris. Mais il est peut-être plus intéressant de savoir avec quelle fréquence ils reviennent à leurs favoris. Et, plus important encore, les clients qui utilisent les favoris achètent-ils plus votre produit ?
* Test du canari. Configurez une fonctionnalité qui ne sera visible que pour certains utilisateurs. Utilisez Application Insights pour voir si cette nouvelle fonctionnalité est utilisée comme vous l'aviez envisagé. Apportez les ajustements nécessaires, puis publiez-la pour un public plus large.
* Parlez à vos utilisateurs ! L'analyse en soi n'est pas suffisante, mais elle vient compléter une bonne relation client.





<!--Link references-->

[start]: app-insights-get-started.md


<!--HONumber=54-->