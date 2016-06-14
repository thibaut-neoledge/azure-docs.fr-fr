<properties 
	pageTitle="Configurer des analyses d’application web pour ASP.NET avec Application Insights" 
	description="Configurez les performances, la disponibilité et l’analyse de l’utilisation de votre site web ASP.NET, hébergé en local ou dans Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="awills"/>


# Configurer Application Insights pour ASP.NET

[Visual Studio Application Insights](app-insights-overview.md) surveille vos applications en direct pour vous aider à [détecter et diagnostiquer les problèmes de performances et les exceptions](app-insights-detect-triage-diagnose.md), mais aussi [découvrir comment votre application est utilisée](app-insights-overview-usage.md). Il fonctionne pour les applications hébergées sur vos propres serveurs locaux IIS ou sur les machines virtuelles dans le cloud, ainsi que les applications web Azure.


## Avant de commencer

Ce dont vous avez besoin :

* Visual Studio 2013 Update 3 ou version ultérieure. Il est préférable d’utiliser une version ultérieure.
* Un abonnement à [Microsoft Azure](http://azure.com). Si votre équipe ou votre organisation dispose d’un abonnement Azure, le propriétaire peut vous y ajouter à l’aide de votre [compte Microsoft](http://live.com). 

Vous pouvez consulter d’autres articles selon les aspects qui vous intéressent :

* [Instrumentation d’une application web au moment de l’exécution](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Ajouter le kit de développement logiciel (SDK) Application Insights


### S'il s'agit d'un nouveau projet...

Assurez-vous qu’Application Insights est sélectionné lorsque vous créez un projet dans Visual Studio.


![Création d'un projet ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ... ou s'il s'agit d'un projet existant

Cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Ajouter la télémétrie Application Insights** ou **Configurer Application Insights**.

![Sélection de Ajouter Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)




## <a name="run"></a> 2. Exécutez l'application.

Exécutez votre application à l'aide de la touche F5 et essayez-la : ouvrez différentes pages pour générer des données de télémétrie.

Un décompte des événements consignés s’affiche dans Visual Studio.

![Dans Visual Studio, le bouton Application Insights apparaît pendant le débogage.](./media/app-insights-asp-net/54.png)

## 3\. Affichez vos données de télémétrie...

### ... dans Visual Studio

Ouvrez la fenêtre Application Insights dans Visual Studio. Pour cela, cliquez sur le bouton Application Insights, ou cliquez avec le bouton droit sur votre projet dans l’Explorateur de solutions :

![Dans Visual Studio, le bouton Application Insights apparaît pendant le débogage.](./media/app-insights-asp-net/55.png)

Cette vue affiche la télémétrie générée du côté serveur de votre application. Faites des essais avec les filtres, puis cliquez sur n’importe quel événement pour afficher plus de détails.

[En savoir plus sur les outils Application Insights dans Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### ... dans le portail

Sauf si vous avez choisi *Installer le Kit de développement logiciel (SDK) uniquement*, vous pouvez également afficher les données de télémétrie dans le portail web Application Insights.

Le portail offre plus de graphiques, d’outils d’analyse et de tableaux de bord que Visual Studio.


Ouvrez votre ressource Application Insights dans le [portail Azure](https://portal.azure.com/).

![Cliquez avec le bouton droit de la souris sur votre projet et ouvrez le portail Azure](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Lorsque le portail s’ouvre, il affiche les données de télémétrie de votre application : ![](./media/app-insights-asp-net/66.png)

* Les événements individuels apparaissent sous **Recherche** (1). Les données apparaissent à cet endroit en premier (et dans le flux live). Cliquez sur n’importe quel événement pour afficher ses propriétés. 
* Les métriques agrégées apparaissent dans les graphiques (2). L’affichage des données à cet endroit peut prendre une minute ou deux. Cliquez sur un graphique pour ouvrir un panneau plus détaillé.

[En savoir plus sur l’utilisation d’Application Insights dans le Portail Azure](app-insights-dashboards.md).

##<a name="land"></a> Quelle est la fonction de la commande « Ajouter Application Insights » ?

Application Insights envoie les données de télémétrie de votre application au portail Application Insights (qui est hébergé dans Microsoft Azure) :

![](./media/app-insights-asp-net/01-scheme.png)

La commande assure donc trois fonctions :

1. Elle ajoute le package NuGet du Kit de développement logiciel (SDK) web Application Insights à votre projet. Pour le visualiser dans Visual Studio, cliquez avec le bouton droit sur votre projet et choisissez Gérer les packages NuGet.
2. Elle crée une ressource Application Insights dans [le Portail Azure](https://portal.azure.com/). Il s’agit de l’endroit où s’afficheront vos données. Elle récupère la *clé d’instrumentation*, qui identifie la ressource.
3. Elle insère la clé d’instrumentation dans `ApplicationInsights.config` pour permettre au SDK d’envoyer les données de télémétrie au portail.

Si vous le souhaitez, vous pouvez [effectuer ces étapes manuellement](app-insights-asp-net-manual.md).


## Et ensuite ?

| | 
|---|---
|**[Utilisation d’Application Insights dans Visual Studio](app-insights-visual-studio.md)**<br/>Débogage avec la télémétrie, recherche de diagnostic, accès au code.|![Visual Studio](./media/app-insights-asp-net/61.png)
|**[Utilisation du portail Application Insights](app-insights-dashboards.md)**<br/>Tableaux de bord, puissants outils de diagnostic et d’analyses, alertes, mappage direct des dépendances de votre application et exportation des données de télémétrie. |![Visual Studio](./media/app-insights-asp-net/62.png)
|**[Ajout de données supplémentaires](app-insights-asp-net-more.md)**<br/>Analyser l’utilisation, la disponibilité, les dépendances et les exceptions. Intégrer des traces à partir des frameworks de journalisation. Écrire des données de télémétrie personnalisées. | ![Visual Studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0608_2016-->