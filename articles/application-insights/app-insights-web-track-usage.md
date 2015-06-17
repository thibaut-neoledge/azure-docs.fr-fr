<properties 
	pageTitle="Suivi de l'utilisation dans les applications web" 
	description="Enregistrez les activités de l'utilisateur." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/09/2015" 
	ms.author="awills"/>
 
# Suivi de l'utilisation des applications web

Découvrez comment votre application web est utilisée. Configurez l'analyse d'utilisation pour savoir quelles sont pages consultées par les utilisateurs, combien d'utilisateurs reviennent sur votre site et la fréquence à laquelle ils le visitent. Ajoutez quelques [événements et métriques personnalisés][track] pour analyser en détail les fonctionnalités les plus populaires, les erreurs les plus courantes, et ainsi adapter votre application pour combler vos utilisateurs.

Les données de télémétrie sont collectées côtés client et serveur. Les données client sont collectées à partir de tous les navigateurs web actuels, tandis que les données serveur peuvent être collectées si votre plateforme est ASP.NET. (Toutefois, il n'est pas nécessaire qu'elle soit exécutée sur Azure.) 

* [Configuration de l'analyse de l'utilisation web](#webclient)
* [Analyse de l'utilisation](#usage)
* [Nombre de pages personnalisées pour les applications de page unique](#spa)
* [Inspection des événements de pages individuelles](#inspect)
* [Suivi détaillé avec des événements et des mesures personnalisés](#custom)
* [Vidéo](#video)

## <a name="webclient"></a> Configuration de l'analyse du client web

#### Obtention d'une ressource Application Insights dans Azure

**Si vous développez une application ASP.NET** et si vous ne l'avez pas encore fait, [ajoutez Application Insights à votre projet web][start]. 

**Si la plateforme de votre site web n'est pas ASP.NET :** connectez-vous à [Microsoft Azure](http://azure.com), accédez au [portail en version préliminaire](https://portal.azure.com), puis ajoutez une ressource Application Insights.

![](./media/app-insights-web-track-usage/appinsights-11newApp.png)

Vous pouvez y revenir plus tard en utilisant le bouton Parcourir.



#### Ajout de notre script dans vos pages web

Dans Démarrage rapide, récupérez le script pour les pages web.

![](./media/app-insights-web-track-usage/appinsights-06webcode.png)

Insérez-le juste avant la balise &lt;/head&gt; de chaque page que vous voulez suivre. Si votre site Web possède une page maître, vous pouvez y placer le script. Par exemple, dans un projet ASP.NET MVC, vous devez placer le script dans View\Shared_Layout.cshtml

## <a name="usage"></a>Analyse de l'utilisation

Exécutez votre site Web, utilisez-le un instant pour générer des données de télémétrie, puis attendez quelques minutes. Vous pouvez l'exécuter en appuyant sur F5 sur votre machine de développement, ou le déployer sur votre serveur.

Dans le volet d'aperçu de l'application, les vignettes d'utilisation suivantes s'affichent :

![](./media/app-insights-web-track-usage/appinsights-47usage.png)

*Aucune donnée pour le moment ? Cliquez sur **Actualiser** en haut de la page.*

* **Sessions par navigateur**

    Une *session* est une période qui débute quand un utilisateur ouvre une page de votre site web. Elle prend fin après un délai d'expiration de 30 minutes sans envoi de requête web de la part de l'utilisateur. 

    Cliquez sur d'autres éléments pour faire un zoom dans le graphique.

* **Principaux affichages de page**

    Affiche les décomptes totaux des 24 dernières heures.

    Cliquez sur la vignette des affichages de page pour obtenir un historique plus détaillé. Pour obtenir un historique plus long, vous pouvez changer l'intervalle de temps du rapport.

![](./media/app-insights-web-track-usage/appinsights-49usage.png)


Cliquez sur un graphique pour voir les autres métriques que vous pouvez afficher, ou ajoutez un nouveau graphique et sélectionnez les métriques à afficher.

![](./media/app-insights-web-track-usage/appinsights-63usermetrics.png)

> [AZURE.NOTE] Décochez la case *toutes* pour activer toutes les métriques. Les métriques peuvent uniquement être affichées selon certaines combinaisons. Lorsque vous sélectionnez une métrique, celles qui lui sont incompatibles sont désactivées.



## <a name="spa"></a> Nombre de pages personnalisées pour les applications de page unique

Par défaut, un compteur de page est activé chaque fois qu'une nouvelle page est chargée dans le navigateur client.  Mais vous pouvez vouloir consulter d'autres affichages de page. Par exemple, si une page affiche son contenu dans des onglets, il se peut que vous désiriez compter une page lorsque l'utilisateur passe d'un onglet à l'autre. Ou il se peut que le code JavaScript dans une page charge du nouveau contenu sans modifier l'URL du navigateur. 

Insérez par exemple l'appel JavaScript suivant à l'emplacement approprié dans votre code client :

    appInsights.trackPageView(myPageName);

Le nom d'une page peut contenir les mêmes caractères qu'une URL, mais tout ce qui se trouve après " # " ou " ? " sera ignoré.


## <a name="inspect"></a> Inspection des événements d'affichage de page individuels

La télémétrie de l'affichage de page est généralement analysée par Application Insights, et vous ne consultez que des rapports cumulés, avec une moyenne entre tous les utilisateurs. Toutefois, à des fins de débogage, vous pouvez également consulter des événements d'affichage de page individuels.

Dans le volet Recherche de diagnostic, définissez Filtres sur Affichage de page.

![](./media/app-insights-web-track-usage/appinsights-51searchpageviews.png)

Sélectionnez n'importe quel événement pour afficher plus de détails.

> [AZURE.NOTE] Si vous utilisez [Rechercher][diagnostic], notez que les mots en entier doivent correspondre : " Concernan " et " oncernant " ne correspondront pas à " Concernant ", mais " Concernan* " correspondra. En outre, un terme de recherche ne peut pas commencer par un caractère générique. Par exemple, effectuer une recherche sur " *oncernan " ne correspondra pas à " Concernant ". 

> [En savoir plus sur la recherche de diagnostic][diagnostic]

## <a name="custom"></a> Suivi détaillé avec des événements et des mesures personnalisés

Vous souhaitez savoir ce que vos utilisateurs font avec votre application ? En insérant des appels dans votre code côtés client et serveur, vous pouvez envoyer vos propres données de télémétrie à Application Insights. Par exemple, vous pouvez découvrir combien d'utilisateurs ont créé des commandes sans les achever, quelles erreurs de validation surviennent le plus souvent ou quel est le score moyen d'un jeu.

[En savoir plus sur les événements personnalisés et les API de métriques][track].

## <a name="video"></a> Vidéo : Suivi de l'utilisation

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Étapes suivantes

[Suivi de l'utilisation avec des événements et des métriques personnalisés][track]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]





<!--HONumber=35.2-->

<!--HONumber=46--> 
 