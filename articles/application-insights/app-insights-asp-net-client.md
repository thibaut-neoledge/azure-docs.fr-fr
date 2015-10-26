<properties 
	pageTitle="Suivi des dépendances dans Application Insights" 
	description="Analysez l'utilisation, la disponibilité et les performances de votre application web locale ou Microsoft Azure avec Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Configurer Application Insights : pages web

*Application Insights est à l'état de version préliminaire.*


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) surveille vos applications en direct pour vous aider à [détecter et diagnostiquer les problèmes de performances et les exceptions][detect], mais aussi [découvrir comment votre application est utilisée][knowUsers].


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Découvrez les performances et les données d’utilisation de votre page web. Ajoutez Visual Studio Application Insights à vos pages, et vous connaîtrez le nombre d’utilisateurs dont vous disposez, la fréquence à laquelle ils reviennent et les pages qu’ils utilisent le plus. Vous obtiendrez également des rapports de durée de chargement et sur toutes les exceptions. Ajoutez quelques [événements et métriques personnalisés][api] pour analyser en détail les fonctionnalités les plus populaires, les erreurs les plus courantes, et ainsi adapter votre page pour combler vos utilisateurs.

![Cliquez sur Nouveau, Services de développement, Application Insights.](./media/app-insights-asp-net-client/16-page-views.png)

Si vous avez déjà configuré une télémétrie de serveur pour votre application web [ASP.NET](app-insights-asp-net.md) ou [Java](app-insights-java-get-started.md), vous obtiendrez des informations du point de vue client et serveur. Les deux flux de données seront intégrés dans le portail Application Insights.

## Ouvrir votre ressource Application Insights

Connectez-vous au [portail Azure](http://portal.azure.com).

Si vous avez déjà défini la surveillance pour le côté serveur de votre application, vous aurez déjà une ressource :

![Cliquez sur Parcourir, Services de développement, Application Insights.](./media/app-insights-asp-net-client/01-find.png)

Si vous n'en avez pas, créez-la.

![Cliquez sur Nouveau, Services de développement, Application Insights.](./media/app-insights-asp-net-client/01-create.png)


## Ajoutez le script du Kit de développement logiciel (SDK) à votre application ou vos pages web

Dans Démarrage rapide, récupérez le script pour les pages Web :

![Dans le panneau de vue d’ensemble de l’application, cliquez sur Démarrage rapide, Obtenir le code pour analyser mes pages web. Copiez le script](./media/app-insights-asp-net-client/02-monitor-web-page.png)

Insérez-le juste avant la balise &lt;/head&gt; de chaque page que vous souhaitez suivre. Si votre site Web possède une page maître, vous pouvez y placer le script. Par exemple :

* Dans un projet ASP.NET MVC, vous devez placer le script dans View\\Shared\\_Layout.cshtml
* Dans un site SharePoint, dans le panneau de configuration, ouvrez [Paramètres du site/Page maître](app-insights-sharepoint.md).

Le script contient la clé d’instrumentation qui dirige les données vers votre ressource Application Insights.

*(Si vous utilisez une infrastructure de page web connue, cherchez des adaptateurs Application Insights. Par exemple, il existe [un module AngularJS](http://ngmodules.org/modules/angular-appinsights).)*


## <a name="run"></a>Exécution de votre application

Exécutez votre application web, utilisez-la un certain temps pour générer de la télémétrie et attendez quelques secondes. Vous pouvez l’exécuter en appuyant sur la touche **F5** de votre machine de développement, ou la publier et laisser les utilisateurs s’en servir.

Si vous souhaitez vérifier la télémétrie qu’une application web envoie à Application Insights, utilisez les outils de débogage de votre navigateur (**F12** sur de nombreux navigateurs). Les données sont envoyées à dc.services.visualstudio.com.

## Exploration de vos données

Vers le haut du panneau Vue d’ensemble des applications se trouve un graphique qui affiche le temps moyen de chargement des pages dans le navigateur :


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*Pas de données pour le moment ? Cliquez sur **Actualiser** en haut de la page. Toujours rien ? Consultez la rubrique [Résolution des problèmes][qna].*

Cliquez sur le graphique pour obtenir une version plus détaillée :

![](./media/app-insights-asp-net-client/07-client-perf.png)

Il s’agit d’un graphique empilé qui présente le temps de chargement total de pages sous forme de [durées standard définies par le consortium W3C](http://www.w3.org/TR/navigation-timing/#processing-model).

![](./media/app-insights-asp-net-client/08-client-split.png)

Notez que la durée de *connexion réseau* est généralement plus faible que prévue, car c’est une moyenne de toutes les demandes du navigateur au serveur. De nombreuses requêtes individuelles ont un temps de connexion de 0, car il existe déjà une connexion active au serveur.


### Performances par page

Plus bas dans le panneau des détails, il y a une grille segmentée par URL de page :


![](./media/app-insights-asp-net-client/09-page-perf.png)

Si vous souhaitez voir les performances des pages au fil du temps, double-cliquez sur la grille et modifiez son type de graphique :

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## Vue d’ensemble de l’utilisation du client

Dans le panneau Vue d’ensemble, cliquez sur **Utilisation** :

![](./media/app-insights-asp-net-client/14-usage.png)

* **Utilisateurs :** nombre d’utilisateurs distincts dans la plage de temps du graphique. (Les cookies sont utilisés pour identifier les utilisateurs qui reviennent.)
* **Sessions :** une session est comptabilisée lorsqu’un utilisateur n’a pas effectué de requêtes pendant 30 minutes.
* **Affichages de pages** compte le nombre d’appels à trackPageView(), généralement appelé une seule fois dans chaque page web.


### Cliquez sur d’autres éléments pour afficher plus de détails

Cliquez sur les graphiques pour plus de détails. Notez que vous pouvez modifier la plage horaire des graphiques.

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


Cliquez sur un graphique pour voir les autres métriques que vous pouvez afficher, ou ajoutez un nouveau graphique et sélectionnez les métriques à afficher.

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]Les métriques peuvent uniquement être affichées selon certaines combinaisons. Lorsque vous sélectionnez une métrique, celles qui lui sont incompatibles sont désactivées.



## Compteurs de page personnalisés

Par défaut, un compteur de page est activé chaque fois qu'une nouvelle page est chargée dans le navigateur client. Mais vous pouvez vouloir consulter d'autres affichages de page. Par exemple, si une page affiche son contenu dans des onglets, il se peut que vous désiriez compter une page lorsque l'utilisateur passe d'un onglet à l'autre. Ou il se peut que le code JavaScript dans une page charge du nouveau contenu sans modifier l'URL du navigateur.

Insérez par exemple l'appel JavaScript suivant à l'emplacement approprié dans votre code client :

    appInsights.trackPageView(myPageName);

Le nom d'une page peut contenir les mêmes caractères qu'une URL, mais tout ce qui se trouve après « # » ou « ? » sera ignoré.


## Inspection des événements d’affichage de page individuels

La télémétrie de l'affichage de page est généralement analysée par Application Insights, et vous ne consultez que des rapports cumulés, avec une moyenne entre tous les utilisateurs. Toutefois, à des fins de débogage, vous pouvez également consulter des événements d'affichage de page individuels.

Dans le volet Recherche de diagnostic, définissez Filtres sur Affichage de page.

![](./media/app-insights-asp-net-client/12-search-pages.png)

Sélectionnez n'importe quel événement pour afficher plus de détails. Dans la page des détails, cliquez sur «... » pour voir davantage de détails.

> [AZURE.NOTE]Si vous utilisez [Rechercher][diagnostic], notez que vous devez faire correspondre les mots entiers : « à propo » et « propos » ne correspondent pas à « À propos », contrairement à « À propo* ». En outre, un terme de recherche ne peut pas commencer par un caractère générique. Par exemple, effectuer une recherche sur « *oncernan » ne correspondra pas à « Concernant ».

> [En savoir plus sur la recherche de diagnostic][diagnostic]

### Propriétés d'affichage de la page

* **Durée d’affichage de la page** : le temps nécessaire pour charger la page et lancer l’exécution des scripts. Plus précisément, l'intervalle entre le début du chargement de la page et l'exécution de l’élément trackPageView. Si vous avez déplacé trackPageView de sa position habituelle après l'initialisation du script, il affiche une autre valeur.

## Suivi personnalisé de l’utilisation

Vous souhaitez savoir ce que vos utilisateurs font avec votre application ? En insérant des appels dans votre code côtés client et serveur, vous pouvez envoyer vos propres données de télémétrie à Application Insights. Par exemple, vous pouvez découvrir combien d'utilisateurs ont créé des commandes sans les achever, quelles erreurs de validation surviennent le plus souvent ou quel est le score moyen d'un jeu.

* [En savoir plus sur les événements personnalisés et les API de métriques][api].
* [Référence d’API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a> Vidéo : suivi de l’utilisation

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a>Étapes suivantes

- [Installer le Kit de développement logiciel (SDK)](../article/application-insights/app-insights-asp-net.md#selector1)
- [Suivi de l’utilisation avec des événements et des métriques personnalisés][api]
- [Availability](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [Application déjà active](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


 

<!---HONumber=Oct15_HO3-->