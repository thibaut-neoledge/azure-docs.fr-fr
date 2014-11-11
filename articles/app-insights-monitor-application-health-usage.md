<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Surveillance de l'intégrité et de l'utilisation de votre application

*Application Insights est disponible en version préliminaire.*

Vérifiez que votre application fonctionne correctement et qu'elle donne satisfaction à vos utilisateurs. Application Insights vous alerte en cas de problèmes de performances et vous aide à trouver et à diagnostiquer leur origine. Ce package permet de suivre les activités des utilisateurs de façon à adapter votre application à leurs besoins.

Application Insights surveille les applications Windows Phone, Windows Store et web hébergées localement ou sur des machines virtuelles, ainsi que sur les sites web Microsoft Azure.

Vous avez besoin de [Visual Studio Update 3][Visual Studio Update 3] et d'un compte [Microsoft Azure][Microsoft Azure] (période d'évaluation gratuite).

1.   [Ajout de Application Insights][Ajout de Application Insights]
-   [Exécution de votre projet][Exécution de votre projet]
-   [Affichage des données surveillées][Affichage des données surveillées]
-   [Déploiement de votre application][Déploiement de votre application]
-   [Étapes suivantes][Étapes suivantes]

*Si vous voulez surveiller un service web existant sans le redéployer ou utiliser Visual Studio, vous pouvez également [installer un agent sur le serveur][installer un agent sur le serveur].*

## <a name="add"></a>1. Ajout de Application Insights

### S'il s'agit d'un nouveau projet...

Lorsque vous créez un projet dans Visual Studio 2013, vérifiez que Application Insights est sélectionné.

![Create an ASP.NET project][Create an ASP.NET project]

La première fois, vous êtes invité à fournir vos informations d'identification pour la version préliminaire de Microsoft Azure ou à vous inscrire. (Ce compte est différent de votre compte Visual Studio Online).

> Il n'y a pas d'option Application Insights ? Vérifiez que vous utilisez Visual Studio 2013 Update 3 et que vous créez un projet web, Windows Store ou Windows Phone.

### ... ou s'il s'agit d'un projet existant

Pour ajouter Application Insights à un nouveau projet, cliquez avec le bouton droit dans l'Explorateur de solutions et sélectionnez Ajouter Application Insights.

![Choose Add Application Insights][Choose Add Application Insights]

## <a name="run"></a>2. Exécution de votre projet

Appuyez sur la touche F5 pour exécuter votre application et essayez-la ; ouvrez différentes pages.

Dans Visual Studio, vous affichez le nombre d'événements reçus.

![][0]

## <a name="monitor"></a>3. Affichage des données surveillées

Ouvrez Application Insights depuis votre projet.

![Right-click your project and open the Azure portal][Right-click your project and open the Azure portal]

Examinez les données dans les vignettes Intégrité des applications et Analyse de l'utilisation. Par exemple :

![Click through to more data][Click through to more data]

Cliquez sur une vignette pour afficher plus d'informations. [En savoir plus sur les vignettes et les rapports.][En savoir plus sur les vignettes et les rapports.]

> [WACOM.NOTE] De nombreuses vignettes affichent des informations limitées dans cette version préliminaire.

## <a name="deploy"></a>4. Déploiement de votre application

Déployez votre application et constatez l'accumulation des données.

## <a name="next"></a>Étapes suivantes

[En savoir plus sur les vignettes et les rapports][En savoir plus sur les vignettes et les rapports.]

[Tests Web][Tests Web]

[Capture et recherche dans les journaux de diagnostics][Capture et recherche dans les journaux de diagnostics]

[Résolution des problèmes][Résolution des problèmes]

## En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][Ajout de Application Insights à votre projet]
-   [Surveillance d'un serveur web en activité][installer un agent sur le serveur]
-   [Exploration des mesures dans Application Insights][En savoir plus sur les vignettes et les rapports.]
-   [Recherche dans un journal de diagnostics][Capture et recherche dans les journaux de diagnostics]
-   [Suivi de la disponibilité avec des tests web][Tests Web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Ajout de Application Insights]: #add
  [Exécution de votre projet]: #run
  [Affichage des données surveillées]: #monitor
  [Déploiement de votre application]: #deploy
  [Étapes suivantes]: #next
  [installer un agent sur le serveur]: ../app-insights-monitor-performance-live-website-now/
  [Create an ASP.NET project]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Choose Add Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  [0]: ./media/appinsights/appinsights-09eventcount.png
  [Right-click your project and open the Azure portal]: ./media/appinsights/appinsights-04-openPortal.png
  [Click through to more data]: ./media/appinsights/appinsights-05-usageTiles.png
  [En savoir plus sur les vignettes et les rapports.]: ../app-insights-explore-metrics/
  [Tests Web]: ../app-insights-monitor-web-app-availability/
  [Capture et recherche dans les journaux de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Ajout de Application Insights à votre projet]: ../app-insights-monitor-application-health-usage/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
