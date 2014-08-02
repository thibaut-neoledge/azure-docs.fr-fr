<properties title="How to use end user analytics" pageTitle="How to use end user analytics" description="Learn about end user analytics in Azure." authors="vladj" />

Analytics pour sites Web
========================

Vous vous demandez quel est le nombre de visites reçues par votre site ? Vous voulez savoir quelle est la durée de chargement moyenne de la page ou quels sont les navigateurs utilisés ? En ajoutant quelques lignes de script dans vos pages Web, vous pouvez collecter des données sur la manière dont vos clients utilisent le site Web.

![End User Analytics](./media/insights-usage-analytics/Insights_ConfiguredExperience.png)

Configuration de End User Analytics
-----------------------------------

1.  Cliquez sur la partie du volet **Web site** qui porte le nom **End user analytics**
2.  Sur le volet **Configuration**, sélectionnez tout le script d'instrumentation et copiez-le.

    ![Configuration](./media/insights-usage-analytics/Insights_CopyCode.png)

3.  Collez le script dans chacune de vos pages Web, juste avant la balise &lt;/head\>. Il est conseillé d'insérer ce script dans toutes vos pages Web. Si vous utilisez ASP.NET, vous pouvez le faire en insérant le script dans la page maître de votre application.

4.  Déployez votre application Web et utilisez-la. Les informations d'analyse sur l'utilisation s'affichent après 5 à 10 minutes.

Exploration des données
-----------------------

La partie Browsers vous permet de consulter en détail les informations sur les navigateurs utilisés, ainsi que leur version.

![Navigateurs](./media/insights-usage-analytics/Insights_Browsers.png)

La partie Analytics affiche les informations suivantes : - Le détail des différents types d'appareils, dont les appareils mobiles et les ordinateurs fixes. - Les 5 pages les plus consultées et un graphique présentant la durée de chargement des pages sur la semaine écoulée. Le nombre de sessions et de visites est également disponible.

![Pages les plus consultées](./media/insights-usage-analytics/Insights_TopPages.png)

-   Les pages les plus lentes de la semaine sont indiquées pour que vous puissiez les optimiser et atteindre vos objectifs.

