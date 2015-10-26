<properties 
	pageTitle="Analyse de l’utilisation des applications Windows Store et Windows Phone avec Application Insights" 
	description="Analyse de l'utilisation de votre application Windows avec Application Insights" 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>

#  Analyse de l’utilisation des applications Windows Store et Windows Phone avec Application Insights

*Application Insights est à l'état de version préliminaire.*

Découvrez le nombre de vos utilisateurs et les pages qu’ils recherchent dans votre application. Application Insights vous fournit ces données dès le départ. Et en insérant quelques lignes de code dans votre application, vous pouvez en savoir plus sur les chemins d'accès et les objectifs des utilisateurs de votre application.

Si vous ne l'avez pas encore fait, ajoutez [Application Insights à votre projet d’application][windows] et publiez-le de nouveau.


## <a name="usage"></a>Suivi de l'utilisation

À partir de la chronologie de vue d'ensemble, cliquez sur les graphiques Utilisateurs et Sessions pour consulter des analyses plus détaillées.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* Les **utilisateurs** sont suivis de façon anonyme, il est donc possible qu’un utilisateur interagissant avec différents appareils soit comptabilisé deux fois.
* Une **session** est comptée lorsque l'application est interrompue (pendant une durée supérieure à un bref laps de temps, afin d'éviter de comptabiliser des interruptions accidentelles).

#### Segmentation

Segmentez un graphique pour obtenir une répartition selon divers critères. Par exemple, pour connaître le nombre d'utilisateurs pour chaque version de votre application, ouvrez le graphique Utilisateurs et segmentez-le par Version de l'application :

![Sur le graphique Utilisateurs, basculez sur Segmentation et sélectionnez la version de l’application](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### Affichages de page

Pour découvrir les chemins d'accès empruntés par les utilisateurs de votre application, insérez [une télémétrie d’affichage de page][api] dans votre code :

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Consultez les résultats sur le graphique d’affichage de page et explorez-le en détails :

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

Cliquez sur n'importe quelle page pour afficher les détails des occurrences spécifiques.

#### Événements personnalisés

En insérant le code pour envoyer des événements personnalisés à partir de votre application, vous pouvez suivre le comportement de vos utilisateurs et l'utilisation des scénarios et fonctionnalités spécifiques.

Par exemple :

    telemetry.TrackEvent("GameOver");

Les données s’affichent dans la grille Événements personnalisés. Vous pouvez soit afficher une vue agrégée dans Metrics Explorer, soit cliquer sur n'importe quel événement pour afficher des occurrences spécifiques.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


Vous pouvez ajouter des propriétés numériques et de chaînes à n'importe quel événement.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Cliquez sur n'importe quelle occurrence pour afficher ses propriétés détaillées, y compris celles que vous avez définies.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

Pour en savoir plus sur les événements personnalisés, consultez la [Référence de l’API][api].

## Sessions

Une session est un concept fondamental dans Application Insights, qui s'efforce d'associer chaque événement de télémétrie, tels que des crashs ou des événements personnalisés que vous codez vous-même, avec une session utilisateur spécifique.

Les informations contextuelles sont collectées pour chaque session, par exemple les caractéristiques du périphérique, la situation géographique, le système d'exploitation, etc.

Lors du [diagnostic des problèmes][diagnostic], vous pouvez trouver toutes les données de télémétrie liées à la session dans laquelle un problème s'est produit, y compris l’ensemble des demandes, événements, exceptions ou traces qui ont été enregistrés.

Les sessions fournissent une bonne indication de la popularité des contextes, par exemple le périphérique, le système d'exploitation ou l’emplacement. En affichant le nombre de sessions regroupées par périphérique, par exemple, vous obtenez un nombre plus précis de la fréquence à laquelle ce périphérique est utilisé avec votre application, plutôt qu’en comptant le nombre de pages affichées. C’est une méthode efficace pour trier les éventuels problèmes spécifiques à un périphérique.


#### Qu'est-ce qu’une session ?

Une session représente une rencontre unique entre l'utilisateur et l'application. Dans sa forme la plus simple, la session commence par un utilisateur qui lance l'application et se termine lorsque l'utilisateur quitte l'application. Pour les applications mobiles, la session est terminée lorsque l'application est interrompue (déplacée en arrière-plan) pendant plus de 20 secondes. Si l'application est reprise, une nouvelle session démarre. Naturellement, un utilisateur peut avoir plusieurs sessions par jour voire par heure.

La **durée de la session** est une mesure qui représente l'intervalle de temps entre le premier et le dernier élément de télémétrie de la session. (Elle n'inclut pas le délai d'expiration).


Le **nombre de sessions** d’un certain intervalle est défini comme le nombre de sessions uniques avec une certaine activité pendant cet intervalle. Lorsque vous examinez une longue période comme le nombre de sessions quotidiennes de la semaine dernière, cette valeur équivaut généralement au nombre total de sessions.

Toutefois, lorsque vous explorez des périodes plus courtes comme une session horaire, une longue session couvrant plusieurs heures est comptée pour chaque heure pendant laquelle la session a été active.

## Utilisateurs et nombre d'utilisateurs

Chaque session utilisateur est associée à un ID d'utilisateur unique, généré lors de l'utilisation de l'application et conservé dans le stockage local du périphérique. Un utilisateur qui utilise plusieurs périphériques sera compté plusieurs fois.

La métrique du **nombre d'utilisateurs** dans un certain intervalle est définie comme le nombre d'utilisateurs uniques avec des activités enregistrées pendant cet intervalle. Par conséquent, les utilisateurs avec de longues sessions peuvent être pris en compte plusieurs fois, lorsque vous définissez une plage de temps afin que la granularité soit inférieure à une heure environ.

La valeur **Nouveaux utilisateurs** compte les utilisateurs dont les premières sessions avec l'application se sont produites au cours de cet intervalle.


## <a name="debug"></a>Comparaison entre le mode de débogage et le mode de version finale

#### Déboguer

Si vous développez en mode débogage, les événements sont envoyés dès qu'ils sont générés. Si vous êtes déconnecté d’Internet, puis que vous quittez l’application avant de vous reconnecter, la télémétrie hors connexion est ignorée.

#### Version finale

Si vous développez en configuration de version finale, les événements sont stockés dans l’appareil, puis envoyés lors de la reprise de l’application. Les données sont également envoyées lors de la première utilisation de l’application. S'il vous n’êtes pas connecté à Internet lors du démarrage, les données de télémétrie précédentes, tout comme celles du cycle de vie actuel, sont stockées, puis envoyées lors de la prochaine connexion.

## <a name="next"></a>Étapes suivantes

[Connaître vos utilisateurs][knowUsers]

[En savoir plus sur Metrics Explorer.][metrics]


[Résolution des problèmes][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


 

<!---HONumber=Oct15_HO3-->