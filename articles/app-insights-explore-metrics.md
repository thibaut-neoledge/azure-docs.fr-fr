<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Exploration de vos mesures

Si vous n'avez pas encore [configuré votre projet web pour Application Insights][configuré votre projet web pour Application Insights], faites-le maintenant.

Voici les rapports que vous pouvez afficher dans le volet de votre application dans Application Insights. Pour obtenir la page depuis Visual Studio, cliquez avec le bouton droit sur votre projet web et sélectionnez Ouvrir Application Insights. Pour l'obtenir depuis le tableau de démarrage de la version préliminaire de Microsoft Azure, sélectionnez Parcourir, Application Insights, puis sélectionnez votre projet.

-   [Intégrité des applications][Intégrité des applications]
-   [Analyse de l'utilisation][Analyse de l'utilisation]
-   [Étapes suivantes][Étapes suivantes]

## <a name="health"></a>Intégrité des applications

L'instrumentation du code de votre application gère l'intégrité de l'application.

![][]

### Temps de réponse moyen

Mesure le temps écoulé entre une demande web soumise à votre application et la réponse renvoyée.

Les points indiquent une moyenne mobile. Si les demandes sont nombreuses, certaines peuvent s'écarter notablement de la moyenne sans que le graphique indique une pointe ou une chute évidente.

Recherchez des pointes inhabituelles. En général, il faut s'attendre à une augmentation du temps de réponse lorsque les demandes augmentent. Si l'augmentation est disproportionnée, votre application peut atteindre les limites des ressources (comme le processeur ou la capacité d'un service qu'elle utilise).

### Demandes

Nombre de demandes reçues pendant une période donnée. Comparez-le aux résultats d'autres graphiques pour connaître le comportement de votre application lorsque la charge varie.

### Tests Web

![][1]

Les [tests web][tests web] affichent les résultats des demandes web envoyées à intervalles réguliers à votre serveur depuis les serveurs Application Insights répartis dans le monde.

Vérifiez si les résultats sont variables avec le nombre de demandes.

[Configuration de tests web][tests web].

### Demandes lentes

![][2]

Indique les demandes qui peuvent nécessiter un réglage des performances.

### Recherche de diagnostic

![][3]

Si vous avez [configuré la journalisation des diagnostics][configuré la journalisation des diagnostics], cliquez pour afficher les derniers événements.

### Demandes ayant échoué

![][4]

Nombre de demandes qui ont renvoyé des exceptions non interceptées.

## <a name="usage"></a>Analyse de l'utilisation

![][5]

L'utilisation des données provient en partie du serveur et en partie des [scripts dans les pages web][configuré votre projet web pour Application Insights].

### Sessions par navigateur

Une *session* est une période qui commence lorsqu'un utilisateur ouvre une page de votre site web et se termine si l'utilisateur n'a pas envoyé de demande après 30 minutes.

Cliquez pour agrandir le graphique.

### Vues de la page principale

Affiche les totaux pour les dernières 24 heures.

Cliquez pour afficher les graphiques d'affichage des pages au cours de la dernière semaine.

### Réorganiser les vignettes

![Choisissez les paramètres et personnalisez][Choisissez les paramètres et personnalisez]

## <a name="next"></a>Étapes suivantes

[Configuration de tests web][tests web]

[Capture et recherche dans les journaux de diagnostics][configuré la journalisation des diagnostics]

[Résolution des problèmes][Résolution des problèmes]

## En savoir plus

-   [Application Insights][Application Insights]
-   [Ajout de Application Insights à votre projet][configuré votre projet web pour Application Insights]
-   [Surveillance d'un serveur web en activité][Surveillance d'un serveur web en activité]
-   [Exploration des mesures dans Application Insights][Exploration des mesures dans Application Insights]
-   [Recherche dans un journal de diagnostics][configuré la journalisation des diagnostics]
-   [Suivi de la disponibilité avec des tests web][tests web]
-   [Suivi de l'utilisation avec des événements et des mesures][Suivi de l'utilisation avec des événements et des mesures]
-   [Questions et réponses - Résolution des problèmes][Résolution des problèmes]

<!--Link references-->

  [configuré votre projet web pour Application Insights]: ../app-insights-monitor-application-health-usage/
  [Intégrité des applications]: #health
  [Analyse de l'utilisation]: #usage
  [Étapes suivantes]: #next
  []: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [tests web]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [configuré la journalisation des diagnostics]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Choisissez les paramètres et personnalisez]: ./media/appinsights/appinsights-21-customizeblade.png
  [Résolution des problèmes]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Surveillance d'un serveur web en activité]: ../app-insights-monitor-performance-live-website-now/
  [Exploration des mesures dans Application Insights]: ../app-insights-explore-metrics/
  [Suivi de l'utilisation avec des événements et des mesures]: ../app-insights-track-usage-custom-events-metrics/
