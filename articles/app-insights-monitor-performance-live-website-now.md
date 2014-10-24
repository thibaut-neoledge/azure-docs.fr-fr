<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

<!-- Required end of Sprint 69 - AUX48 -->

# Surveillance des performances sur un site web en activité

*Application Insights est disponible en version préliminaire.*

Vous avez une application web qui ne fonctionne pas correctement ? Diagnostiquez rapidement les exceptions et les problèmes de performances sans la régénérer ni la redéployer. Installez l'agent Application Insights sur le serveur : il vous permettra de trouver les goulots d'étranglement nuisant aux performances et d'obtenir des suivis de la pile pour les exceptions.

#### Quand dois-je utiliser cette méthode pour configurer Application Insights ?

Cette approche est principalement destinée à diagnostiquer les problèmes de performances dans un site web IIS en activité.

Il suffit d'installer un agent dans le serveur et d'examiner les données des performances dans Application Insights.

-   Vous pouvez appliquer cette méthode à une application ASP.NET hébergée sur un serveur IIS.

-   Vous avez besoin d'un [compte Microsoft Azure][compte Microsoft Azure] pour afficher vos données.

-   Vous avez besoin de l'accès administrateur au serveur qui exécute votre application web.

-   Vous *n'avez pas* besoin du code de l'application et ne devez pas la régénérer ni la redéployer.

-   Cette méthode instrumente l'application web telle quelle. Vous n'insérez pas de code pour le suivi ou la journalisation. (Mais vous pouvez le faire plus tard si vous le souhaitez).

Si vous voulez insérer des traces de journalisation ou de diagnostic, ne continuez pas à partir de maintenant. À la place, [ajoutez Application Insights à votre projet][existant] et redéployez-le.

## Installation de l'agent Application Insights sur votre serveur web

1.  Sur votre serveur web, connectez-vous avec vos informations d'identification d'administrateur.

2.  Vérifiez que vous disposez de la version 5.0 ou d'une version ultérieure de [Web Platform Installer][Web Platform Installer].
3.  Installez l'agent Application Insights au moyen de Web Platform Installer.

    ![][]

4.  Dans l'Assistant Installation, connectez-vous à Microsoft Azure.

    ![][1]

5.  Sélectionnez l'application web installée ou le site web que vous voulez surveiller, puis configurez le nom sous lequel vous voulez afficher les résultats dans le portail Application Insights. Cliquez ensuite sur le bouton Ajouter.

    ![][2]

    Vous devez normalement choisir de créer une ressource.

    Vous pouvez utiliser une ressource existante si, par exemple, vous avez déjà configuré des [tests web][tests web] pour votre site.

6.  Notez que ApplicationInsights.config est inséré dans les sites web que vous voulez surveiller.

    ![][3]

	web.config a également été légèrement modifié.

### Vous voulez (re)configurer plus tard ?

Lorsque l'Assistant est terminé, vous pouvez reconfigurer l'agent à tout moment. Vous pouvez également le faire si vous avez installé l'agent et avez rencontré des problèmes avec la configuration initiale.

![Click the Application Insights icon on the task bar][Click the Application Insights icon on the task bar]

## Affichage de vos données

Ouvrez votre compte dans Azure, accédez à Application Insights et ouvrez la ressource que vous avez créée.

![][4]

Les données sont affichées sous Intégrité des applications.

![][5]

#### Pas de données ?

-   Utilisez votre site pour créer des données.
-   Attendez quelques minutes que les données arrivent.
-   Vérifiez que le pare-feu du serveur autorise le trafic sortant sur le port 443 vers dc.services.visualstudio.com

## <a name="next"></a>Étapes suivantes

[Affichage des données][Affichage des données]

[Recherche dans les journaux de diagnostics][Recherche dans les journaux de diagnostics]

[Tests Web][tests web]

[Configuration de la surveillance de l'utilisation][Configuration de la surveillance de l'utilisation]

[Résolution des problèmes][Résolution des problèmes]

## En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][Configuration de la surveillance de l'utilisation]
-   [Surveillance d'un serveur web en activité][Surveillance d'un serveur web en activité]
-   [Exploration des mesures dans Application Insights][Affichage des données]
-   [Recherche dans un journal de diagnostics][Recherche dans les journaux de diagnostics]
-   [Suivi de la disponibilité avec des tests web][tests web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [compte Microsoft Azure]: http://azure.com
  [Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  []: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [tests web]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Click the Application Insights icon on the task bar]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [Affichage des données]: ../app-insights-explore-metrics/
  [Recherche dans les journaux de diagnostics]: ../app-insights-search-diagnostic-logs/
  [Configuration de la surveillance de l'utilisation]: ../app-insights-monitor-application-health-usage/
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Surveillance d'un serveur web en activité]: ../app-insights-monitor-performance-live-website-now/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
