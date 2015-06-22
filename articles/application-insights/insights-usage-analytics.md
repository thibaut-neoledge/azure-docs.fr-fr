<properties 
	pageTitle="Utilisation de Analyses de l'utilisateur final" 
	description="Analyse d'utilisateur final pour les sites web Microsoft Azure." 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-09" 
	ms.author="awills"/>

# Analyses pour les sites web Microsoft Azure

Vous vous demandez combien de visiteurs ont consulté votre site ?  Vous voulez savoir quelle est la durée de chargement moyenne de la page ou quels sont les navigateurs utilisés ?  En ajoutant quelques lignes de script à vos pages web, vous pouvez collecter des données sur la manière dont vos clients utilisent votre site web. 

*Vous pouvez également effectuer cela pour les sites web non-Azure : [Surveillance de l'utilisation des applications web avec Application Insights](../app-insights-web-track-usage.md).*

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

## Configuration de End User Analytics

1. Cliquez sur la partie du panneau **Site web** intitulée **Analyses de l'utilisateur final.**
2. Dans le panneau **Configuration**, sélectionnez tout le script d'instrumentation et copiez-le.  
    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)
3. Collez le script dans chacune de vos pages web, juste avant la fermeture de la balise </head>. Il est préférable d'insérer le script dans toutes vos pages web. Si vous utilisez ASP.NET, vous pouvez le faire en insérant le script dans la page maître de votre application.
4. Déployez votre application web et utilisez-la. Les informations d'analyse concernant l'utilisation s'affichent après 5 à 10 minutes.

## Exploration des données

La partie Browsers vous permet de consulter en détail les informations sur les navigateurs utilisés, ainsi que leur version.

![Browsers](./media/insights-usage-analytics/Insights_Browsers.png)

La partie Analytics affiche les informations suivantes :

- Le détail des différents types d'appareils, dont les appareils mobiles et les ordinateurs fixes.
- Les 5 pages les plus consultées et un graphique présentant la durée de chargement des pages durant la semaine écoulée.  Le nombre de sessions et de visites est également disponible.  
    ![Top Pages](./media/insights-usage-analytics/Insights_TopPages.png)
- Les pages les plus lentes de la semaine sont indiquées pour que vous puissiez les optimiser et atteindre vos objectifs.


<!--HONumber=46--> 
 