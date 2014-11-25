<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-29" ms.author="awills" />

# Analyse des sites web

Vous vous demandez quel est le nombre de visites reçues par votre site ? Vous voulez savoir quelle est la durée de chargement moyenne de la page ou quels sont les navigateurs utilisés ? En ajoutant quelques lignes de script dans vos pages web, vous pouvez collecter des données sur la manière dont vos clients utilisent le site web.

![End User Analytics][End User Analytics]

## Configuration de End User Analytics

1.  Cliquez sur la partie du volet **Web site** qui porte le nom **End user analytics**
2.  Dans le volet **Configuration**, sélectionnez tout le script d'instrumentation et copiez-le.  
    ![Configuration][Configuration]
3.  Collez le script dans chacune de vos pages web, juste avant la fermeture de la balise
    . Il est préférable d'insérer le script dans toutes vos pages web. Si vous utilisez ASP.NET, vous pouvez le faire en insérant le script dans la page maître de votre application.
4.  Déployez votre application Web et utilisez-la. Les informations d'analyse sur l'utilisation s'affichent après 5 à 10 minutes.

## Exploration des données

La partie Browsers vous permet de consulter en détail les informations sur les navigateurs utilisés, ainsi que leur version.

![Navigateurs][Navigateurs]

La partie Analytics affiche les informations suivantes :

-   Le détail des différents types d'appareils, dont les appareils mobiles et les ordinateurs fixes.
-   Les 5 pages les plus consultées et un graphique présentant la durée de chargement des pages durant la semaine écoulée. Le nombre de sessions et de visites est également disponible.  
    ![Pages les plus consultées][Pages les plus consultées]
-   Les pages les plus lentes de la semaine sont indiquées pour que vous puissiez les optimiser et atteindre vos objectifs.

  [End User Analytics]: ./media/insights-usage-analytics/Insights_ConfiguredExperience.png
  [Configuration]: ./media/insights-usage-analytics/Insights_CopyCode.png
  [Navigateurs]: ./media/insights-usage-analytics/Insights_Browsers.png
  [Pages les plus consultées]: ./media/insights-usage-analytics/Insights_TopPages.png
