<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Application Insights - Prise en main

*Application Insights est disponible en version préliminaire.*

Application Insights permet de surveiller la disponibilité, les performances et l'utilisation de votre application en activité. (Ce n'est pas nécessairement une application Microsoft Azure). Sa configuration est très facile : vous afficherez les résultats en quelques minutes.

-   **Disponibilité** - Vérifiez que votre application web est disponible et répond aux demandes. Nous testons vos URL à quelques minutes d'intervalle à partir de divers emplacements dans le monde entier et vous informons en cas de problème.
-   **Performances** - Diagnostiquez d'éventuels problèmes de performances ou exceptions se produisant dans votre service web. Constatez comme les temps de réponse varient avec le nombre de demandes, recherchez si le processeur ou d'autres ressources sont surchargés, obtenez des suivis de la pile à partir des exceptions et effectuez facilement des recherches dans les journaux.
-   **Utilisation** - Recherchez ce que les utilisateurs font avec vos applications de façon à concentrer votre développement là où c'est le plus utile. Actuellement, vous pouvez surveiller des applications web, Windows Store et Windows Phone.

## Prise en main

Il existe deux façons de démarrer avec Application Insights :

-   [Ajout de Application Insights à votre projet dans Visual Studio][Ajout de Application Insights à votre projet dans Visual Studio]

    Ajoutez Application Insights à vos projets pour en suivre l'utilisation, les performances et la disponibilité, et analyser les journaux de diagnostics. Vous pouvez afficher les données en quelques minutes en mode débogage et déployer ensuite votre projet pour obtenir des données en direct.

    Utilisez cette option si vous mettez à jour ou créez un projet.

    [Prise en main en ajoutant Application Insights à votre projet.][Ajout de Application Insights à votre projet dans Visual Studio]

-   [Diagnostiquez maintenant les problèmes rencontrés dans un service web en activité][Diagnostiquez maintenant les problèmes rencontrés dans un service web en activité]

    Installez l'agent Application Insights sur votre serveur IIS et examinez les performances en quelques minutes. Examinez le nombre de demandes, les temps de réponse, la charge des ressources et obtenez les traces des exceptions.

    Utilisez cette option si vous devez comprendre ce qui se passe actuellement dans votre serveur web. Vous n'avez pas besoin de redéployer votre code, mais vous avez besoin de l'accès administrateur à votre serveur et d'un compte Microsoft Azure.

    À tout moment, vous pouvez ajouter la surveillance de la disponibilité.

    Par la suite, vous pouvez utiliser l'autre option pour ajouter Application Insights à votre projet pour analyser les journaux de diagnostics et suivre l'utilisation de votre projet.

    [Prise en main en installant Application Insights sur votre serveur web.][Diagnostiquez maintenant les problèmes rencontrés dans un service web en activité]

> [WACOM.NOTE] Il existe une [ancienne version de Application Insights][ancienne version de Application Insights] dans Visual Studio Online. Nous la remanions entièrement dans Microsoft Azure : ce que vous lisez ici concerne cette nouvelle version.

![Example application monitor in Application Insights][Example application monitor in Application Insights]

## En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][Ajout de Application Insights à votre projet dans Visual Studio]
-   [Surveillance d'un serveur web en activité][Diagnostiquez maintenant les problèmes rencontrés dans un service web en activité]
-   [Exploration des mesures dans Application Insights][Exploration des mesures dans Application Insights]
-   [Recherche dans un journal de diagnostics][Recherche dans un journal de diagnostics]
-   [Suivi de la disponibilité avec des tests web][Suivi de la disponibilité avec des tests web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Questions et réponses - Résolution des problèmes]

<!--Link references-->

  [Ajout de Application Insights à votre projet dans Visual Studio]: ../app-insights-monitor-application-health-usage/
  [Diagnostiquez maintenant les problèmes rencontrés dans un service web en activité]: ../app-insights-monitor-performance-live-website-now/
  [ancienne version de Application Insights]: http://msdn.microsoft.com/fr-fr/library/dn481095.aspx
  [Example application monitor in Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [Exploration des mesures dans Application Insights]: ../app-insights-explore-metrics/
  [Recherche dans un journal de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Suivi de la disponibilité avec des tests web]: ../app-insights-monitor-web-app-availability/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-web-track-usage-custom-events-metrics/
  [Questions et réponses - Résolution des problèmes]: ../app-insights-troubleshoot-faq/
