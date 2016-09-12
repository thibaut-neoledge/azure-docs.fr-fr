<properties 
	pageTitle="Créer une ressource Application Insights | Microsoft Azure" 
	description="Configurez la surveillance d’Application Insights pour une nouvelle application en direct. Approche basée sur le web." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/26/2016" 
	ms.author="awills"/>

# Création d’une ressource Application Insights dans Azure

Visual Studio Application Insights affiche les données relatives à votre application dans une *ressource* Microsoft Azure. La création d’une nouvelle ressource fait, par conséquent, partie de la [configuration d’Application Insights pour surveiller une nouvelle application][start]. Dans de nombreux cas, cela peut être effectué automatiquement par l’IDE. Il s’agit de la méthode recommandée, lorsqu’elle est disponible. Mais dans certains cas, vous créez manuellement une ressource.

Après avoir créé la ressource, vous obtenez sa clé d’instrumentation et l’utilisez pour configurer le kit de développement logiciel (SDK) dans l’application. Cela envoie les données de télémétrie à la ressource.

## S’inscrire à Microsoft Azure

Si vous n’avez pas de [compte Microsoft, procurez-vous en un dès maintenant](http://live.com). (Si elle utilise des services comme Outlook.com, OneDrive, Windows Phone ou XBox Live, elle dispose d’un compte Microsoft.)

Vous devez également vous abonner à [Microsoft Azure](http://azure.com). Si votre équipe ou votre organisation dispose d’un abonnement Azure, le propriétaire peut vous y ajouter à l’aide de votre Windows Live ID.

Vous avez également la possibilité de créer un nouvel abonnement. Le compte gratuit vous permet d’essayer toutes les fonctionnalités d’Azure. Après l’expiration de la période d’essai, il se peut que la formule d’abonnement proposant un paiement à l’utilisation vous semble appropriée. Celle-ci vous permet de ne pas être facturé pour les services gratuits.

Dès que vous êtes abonné, connectez-vous à Application Insights à l’adresse [http://portal.azure.com](https://portal.azure.com), et utilisez votre Live ID pour vous connecter.


## Création d’une ressource Application Insights dans Azure
  

Dans le portail [portal.azure.com](https://portal.azure.com), ajoutez une ressource Application Insights :

![Cliquez sur Nouveau > Application Insights](./media/app-insights-create-new-resource/01-new.png)


* Le **type d’application** définit le contenu du panneau de présentation et les propriétés disponibles dans [Metrics Explorer][metrics]. Si vous ne voyez pas votre type d’application, choisissez ASP.NET.
* **Groupe de ressources** facilite la gestion des propriétés telles que le contrôle d’accès. Si vous avez déjà créé d’autres ressources Azure, vous pouvez choisir de placer cette nouvelle ressource dans le même groupe.
* **Abonnement** est votre compte de paiement dans Azure.
* **Emplacement** correspond à l’endroit où nous conservons vos données. Actuellement, il n’est pas possible de le modifier.
* **Ajouter au tableau d’accueil** place une vignette d’accès rapide à votre ressource sur votre page d’accueil Azure. Recommandé.

Une fois votre application créée, un nouveau panneau s’ouvre. Vous y trouverez des données relatives à l’utilisation et aux performances concernant votre application.

Pour y revenir lors de votre prochaine connexion à Azure, recherchez la vignette d’accès rapide à votre application située sur le tableau d’accueil (écran d’accueil). Sinon, cliquez sur Parcourir.


## Copie de la clé d’instrumentation

La clé d'instrumentation identifie la ressource que vous avez créée. Vous devrez la fournir au SDK.

![Cliquez sur Essentials, sur la clé d'instrumentation, puis appuyez sur CTRL+C](./media/app-insights-create-new-resource/02-props.png)

## Installation du Kit de développement logiciel (SDK) dans votre application

Installez le Kit de développement logiciel (SDK) Application Insights dans votre application. Cette étape repose en grande partie sur votre type d’application.

La clé d’instrumentation permet de configurer le [kit de développement logiciel (SDK) que vous avez installé dans votre application][start].

Le SDK inclut des modules standard qui envoient des données de télémétrie sans avoir à écrire du code. Pour suivre les actions des utilisateurs ou diagnostiquer des problèmes plus en détail,[utilisez l'API][api] pour envoyer votre propre télémétrie.


## <a name="monitor"></a>Reportez-vous aux données de télémétrie

Fermez le panneau de démarrage rapide pour revenir au panneau de votre application dans le portail Azure.

Cliquez sur la vignette de recherche pour afficher [Recherche de diagnostic][diagnostic] : les premiers événements y apparaissent.

Après quelques secondes, cliquez sur Actualiser pour obtenir des données supplémentaires.

## Création automatique d’une ressource

Vous pouvez écrire un [script PowerShell](app-insights-powershell-script-create-resource.md) pour créer automatiquement une ressource.

## Étapes suivantes

* [Création d’un tableau de bord](app-insights-dashboards.md)
* [Recherche de diagnostic](app-insights-diagnostic-search.md)
* [Exploration des mesures](app-insights-metrics-explorer.md)
* [Écriture de requêtes Analytics](app-insights-analytics.md)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_0831_2016-->