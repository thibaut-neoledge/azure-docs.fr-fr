<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Suivi de l'utilisation

## <a name="webclient"></a>Configuration de l'analyse de l'utilisation Web

Si vous ne l'avez pas encore fait, [ajoutez Application Insights à votre projet Web][start].

## <a name="usage"></a>Analyse de l'utilisation

Dans le volet d'aperçu de l'application, les vignettes d'utilisation suivantes s'affichent :

![](./media/appinsights/appinsights-47usage.png)

### Sessions par navigateur

Une *session* est une période qui débute lorsqu'un utilisateur ouvre n'importe quelle page de votre site Web et se termine après un délai d'expiration de 30 minutes sans que l'utilisateur ait envoyé aucune demande Web.

Cliquez sur d'autres éléments pour faire un zoom dans le graphique.

### Principaux affichages de page

Affiche les décomptes totaux des 24 dernières heures.

Cliquez sur la vignette des affichages de page pour obtenir un historique plus détaillé.

![](./media/appinsights/appinsights-49usage.png)

Cliquez sur Plage de temps pour afficher un historique plus long, jusqu'à sept jours.

Cliquez sur un graphique pour voir les autres métriques qui peuvent être affichées.

![](./media/appinsights/appinsights-63usermetrics.png)

## Compteurs de page personnalisés

Par défaut, un compteur de page est activé chaque fois qu'une nouvelle page est chargée dans le navigateur client. Mais vous pouvez vouloir consulter d'autres affichages de page. Par exemple, si une page affiche son contenu dans des onglets, il se peut que vous désiriez compter une page lorsque l'utilisateur passe d'un onglet à l'autre. Ou il se peut que le code JavaScript dans une page charge du nouveau contenu sans modifier l'URL du navigateur.

Insérez par exemple l'appel JavaScript suivant à l'emplacement approprié dans votre code client :

    appInsights.trackPageView(myPageName);

Le nom d'une page peut contenir les mêmes caractères qu'une URL, mais tout ce qui se trouve après « \# » ou « ? » sera ignoré.

## Inspection des événements d'affichage de page individuels

La télémétrie de l'affichage de page est généralement analysée par Application Insights, et vous ne consultez que des rapports cumulés, avec une moyenne entre tous les utilisateurs. Toutefois, à des fins de débogage, vous pouvez également consulter des événements d'affichage de page individuels.

Dans le volet Recherche de diagnostic, définissez Filtres sur Affichage de page.

![](./media/appinsights/appinsights-51searchpageviews.png)

Sélectionnez n'importe quel événement pour afficher plus de détails.

> [WACOM.NOTE] Si vous utilisez [Rechercher][diagnostic], notez que les mots en entier doivent correspondre : « Concernan » et « oncernant » ne correspondront pas à « Concernant », mais « Concernan\* » correspondra. En outre, un terme de recherche ne peut pas commencer par un caractère générique. Par exemple, effectuer une recherche sur « \*oncernan » ne correspondra pas à « Concernant ».

> [En savoir plus sur la recherche de diagnostic][diagnostic]

## Suivi de l'utilisation

> [AZURE.VIDEO tracking-usage-with-application-insights]

## En savoir plus

-   [Application Insights - prise en main][start]
-   [Analyse d'un serveur Web en direct dès maintenant][redfield]
-   [Analyse des performances dans les applications Web][perf]
-   [Recherche des journaux de diagnostic][diagnostic]
-   [Suivi de la disponibilité avec les tests Web][availability]
-   [Suivi de l'utilisation][usage]
-   [Questions et réponses, et résolution des problèmes][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
