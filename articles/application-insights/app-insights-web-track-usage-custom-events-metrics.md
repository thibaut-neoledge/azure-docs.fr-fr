<properties 
	pageTitle="Suivi des événements et de l'utilisation dans votre application web avec l'API Application Insights" 
	description="Insérez quelques lignes de code pour suivre l'utilisation et diagnostiquer les problèmes." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="awills"/>

# Suivi des événements et des métriques d'utilisation personnalisés dans votre application web

*Application Insights est à l'état de version préliminaire.*

Insérez quelques lignes de code dans votre application web pour découvrir ce qu'en font les utilisateurs. Vous pouvez suivre les événements, les métriques et les affichages de pages. Vous verrez des graphiques et des tableaux contenant les données agrégées de tous vos utilisateurs. 

> [AZURE.NOTE] L'expérience utilisateur complète n'est pas disponible pour le moment. Vous pouvez envoyer des événements personnalisés et les métriques à Application Insights et rechercher la télémétrie brute dans [Recherche de diagnostic][diagnostic]. Mais, pour le moment, les graphiques de statistiques condensées ne sont pas disponibles. Ils le seront bientôt.

<!-- Sample pic -->

* [Suivi côté client et serveur](#clientServer)
* [Avant de commencer](#prep)
* [Suivi des métriques](#metrics)
* [Suivi des événements](#events)
* [Suivi des affichages de pages](#pageViews)
* [Filtrage, recherche et segmentation des données grâce à des propriétés](#properties)
* [Combinaison de métriques et d'événements](#measurements)
* [Configuration des valeurs de propriété par défaut](#defaults)
* [Définition de contextes multiples](#contexts)
* [Activation et désactivation de la télémétrie](#disable)
* [Étapes suivantes](#next)



## <a name="clientServer"></a> Suivi côté client et serveur

Vous pouvez envoyer des données de télémétrie côté client (page web) ou serveur de votre application, ou les deux.

Les API client et serveur sont très similaires. Vous pouvez envoyer les mêmes types de données de télémétrie à partir des navigateurs web de vos utilisateurs ou depuis votre serveur web. La différence réside dans l'étendue des données que vous pouvez envoyer.

* Le suivi au niveau du client web est particulièrement utile si vous avez mis en place des pages web enrichies contenant beaucoup de JavaScript. Par exemple, vous pouvez contrôler la fréquence à laquelle les utilisateurs cliquent sur un bouton particulier ou la fréquence à laquelle ils rencontrent des erreurs de validation.
* Le suivi au niveau du serveur web est plus utile pour l'analyse des métriques et des événements métier tels que la valeur du panier d'achat d'un client ou le nombre de commandes abandonnés.

Dans une application web ASP.NET standard, vous disposez de l'appel JavaScript par défaut sur trackPageView() dans la page maître web, puis vous ajoutez des appels pour suivre des événements et des mesures dans le code du serveur. Si votre code côté client est assez riche, vous pouvez également ajouter des appels pour suivre des événements et des mesures côté client.


## <a name="prep"></a>Avant de commencer

Si vous ne l'avez pas encore fait :

* Pour obtenir la télémétrie à partir d'une application web ASP.NET :
    [Ajout de Application Insights à votre projet][greenbrown]
    Ajoutez au code votre serveur web :
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Configurez l'analyse de l'utilisation Web][usage]. Le code d'initialisation de JavaScript doit être inclus dans chaque page web où vous souhaitez écrire du code pour la surveillance ou dans une page maître. 
    Si cela fonctionne, les données doivent s'afficher sur le volet Vue d'ensemble sous Analyse de l'utilisation.

Lorsque vous exécutez votre application sur votre machine de développement en mode débogage, les résultats s'affichent dans Application Insights en quelques secondes. Lorsque vous déployez l'application, le transfert des données à partir de votre serveur et des clients dans le pipeline prend plus de temps.

<!--
## <a name="metrics"></a> Suivi des métriques

Vous n'avez rien à faire de plus pour obtenir les données d'utilisation de base telles que les affichages de pages. Mais vous pouvez écrire quelques lignes de code pour en savoir plus sur ce que vos utilisateurs font avec votre application.

Par exemple, si votre application est un jeu, vous pouvez connaître la moyenne du score des utilisateurs et savoir s'ils le trouve plus facile ou plus difficile après publication d'une nouvelle version.

Pour suivre une métrique (une valeur numérique tel qu'un score), insérez une ligne de script comme celle-ci à l'endroit adéquat de votre application :

JavaScript côté client

    appInsights.trackMetric("Alerts", notifications.Count);

C# côté serveur

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB côté serveur

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Testez l'application et utilisez-la afin d'exécuter votre appel trackMetric().


Puis accédez à votre application dans Application Insights et cliquez sur la vignette [Métriques][metrics]. Sélectionnez vos métriques pour afficher les premiers résultats.


Le graphique affiche la moyenne récentes des valeurs enregistrées pour tous vos utilisateurs. 


(Remarque : les métriques ne sont pas optimisées pour diagnostiquer des problèmes. Si c'est ce que vous souhaitez faire, consultez plutôt [Journalisation des diagnostics][diagnostic].) -->


## <a name="events"></a>Suivi des événements

Les événements indiquent la fréquence moyenne d'une occurrence pour tous vos utilisateurs. Par exemple, si vous voulez savoir à quelle fréquence les utilisateurs terminent votre jeu : Dans le code terminant le jeu, insérez une ligne comme suit :

JavaScript côté client

    appInsights.trackEvent("EndOfGame");

C# côté serveur
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB côté serveur


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Si vous envoyez les données de télémétrie à partir du client et du serveur, veillez à donner des noms différents aux événements.


## <a name="pageViews"></a>Affichages de pages (côté client uniquement)

Par défaut, le script d'initialisation dans l'en-tête de la page web journalise un affichage de la page, en nommant l'événement à l'aide de l'URL relative de la page. Ces appels fournissent les statistiques de base sur l'utilisation de la page. 

![Usage analytics on main app blade](./media/app-insights-web-track-usage-custom-events-metrics/appinsights-05-usageTiles.png)

### Données de pages personnalisées

Si vous le souhaitez, vous pouvez modifier l'appel pour changer le nom ou insérer des appels supplémentaires. Par exemple, si votre application de page web unique affiche plusieurs onglets, vous souhaiterez peut-être enregistrer un affichage de page lorsque l'utilisateur passe d'un onglet à l'autre. Par exemple :

JavaScript côté client :

    appInsights.trackPageView("tab1");

Si vous avez plusieurs onglets dans différentes pages HTML, vous pouvez aussi spécifier l'URL :

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Filtrage, recherche et segmentation des données grâce à des propriétés

Vous pouvez joindre des propriétés et des mesures à vos événements, affichages de pages et autres données de télémétrie. 

**Les propriétés** sont des valeurs de chaîne que vous pouvez utiliser pour filtrer vos données de télémétrie dans les rapports d'utilisation. Par exemple, si votre application fournit plusieurs jeux, vous pouvez joindre le nom de chaque jeu à un événement distinct, pour savoir quels sont les jeux les plus populaires.

**Les mesures** sont des valeurs numériques permettant d'obtenir des statistiques dans les rapports d'utilisation.


JavaScript côté client

    appInsights.trackEvent("EndOfGame",
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
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB côté serveur

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Vous pouvez joindre des propriétés à des affichages de pages de la même manière :

JavaScript côté client

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Veillez à ne pas journaliser des informations personnelles dans les propriétés.


## Affichages de pages et événements datés

Vous pouvez joindre des données datées à événements et aux affichages de pages. Au lieu d'appeler trackEvent ou trackPageView, utilisez ces appels :

JavaScript côté client

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Utilisez la même chaîne en tant que premier paramètre dans les appels de démarrage et d'arrêt.

## <a name="defaults"></a>Définition de valeurs de propriétés par défaut (non disponible pour le client web)

Vous pouvez définir des valeurs par défaut dans un TelemetryContext. Elles sont jointes à chaque métrique et événement envoyé à partir du contexte. 
    

C# côté serveur

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB côté serveur

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
La télémétrique individuelle peut remplacer les valeurs par défaut.

Si vous souhaitez basculer entre des groupes de valeurs de propriété par défaut, définissez plusieurs contextes.



## <a name="next"></a>Étapes suivantes


[Recherche d'événements et de journaux][diagnostic]

[Résolution des problèmes][qna]


[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=46--> 
 