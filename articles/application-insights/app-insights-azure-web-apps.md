---
title: "Surveillance des performances d’application web Azure | Microsoft Docs"
description: "Analyse des performances des applications pour les applications web Azure. Graphique de charge et de temps de réponse, informations de dépendance et alertes sur les performances."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: b25f3dd41c8dc381258bb42d8802ba4b56e05903
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---
# <a name="monitor-azure-web-app-performance"></a>Surveillance des performances d'application web Azure
Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer la surveillance des performances d’application pour vos [applications web Azure](../app-service-web/app-service-web-overview.md). [Azure Application Insights](app-insights-overview.md) instrumente votre application afin qu’elle envoie des données de télémétrie concernant ses activités au service Application Insights, où elles sont stockées et analysées. Les graphiques de mesure et les outils de recherche peuvent alors être utilisés pour aider à diagnostiquer les problèmes, améliorer les performances et évaluer l’utilisation.

## <a name="run-time-or-build-time"></a>En cours d’exécution ou en cours de création
Vous pouvez configurer la surveillance par l’instrumentation de l’application de deux manières :

* **En cours d’exécution** : vous pouvez sélectionner une extension de surveillance des performances lorsque votre application est déjà active. Il n’est pas nécessaire de reconstruire ou de réinstaller votre application. Vous obtenez un ensemble standard de packages qui analyse les temps de réponse, les taux de réussite, les exceptions, les dépendances, etc. 
* **En cours de création** : vous pouvez installer un package dans votre application en cours de développement. Cette option est plus souple. Outre les mêmes packages standard, vous pouvez écrire du code pour personnaliser les données de télémétrie ou pour envoyer vos propres données de télémétrie. Vous pouvez consigner des activités spécifiques ou enregistrer les événements en fonction de la sémantique de votre domaine d’application. 

## <a name="run-time-instrumentation-with-application-insights"></a>Instrumentation d’exécution avec Application Insights
Si vous exécutez déjà une application web dans Azure, vous obtenez déjà certains éléments de surveillance, tels que le taux de demande et d’erreur. Ajoutez Application Insights pour bénéficier d’un plus grand nombre de fonctionnalités, telles que les temps de réponse, la surveillance des appels vers les dépendances, la détection intelligente et le puissant langage de requête Log Analytics. 

1. **Sélectionnez Application Insights** dans le panneau de configuration Azure pour votre application web.
   
    ![Sous Surveillance, choisissez Application Insights](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Choisissez de créer une nouvelle ressource, sauf si vous avez déjà configuré une ressource Application Insights pour cette application par un autre itinéraire.
2. **Instrumentez votre application web** après l’installation d’Application Insights. 
   
    ![Instrumenter votre application web](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Activer l’analyse côté client** pour la consultation de page et la télémétrie de l’utilisateur.

   * Cliquez sur Paramètres > Paramètres de l’application
   * Sous Paramètres de l’application, ajoutez une nouvelle paire clé/valeur : 
   
    Clé :`APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Valeur: `true`
   * **Enregistrez** les paramètres et **Redémarrez** votre application.
3. **Surveillez votre application web**.  [Explorez les données](#explore-the-data).

Par la suite, vous pourrez, si vous le souhaitez, générer l’application avec Application Insights.

*Comment supprimer Application Insights, ou basculer vers l’envoi vers une autre ressource ?*

* Dans Azure, ouvrez le panneau de contrôle de l’application web, puis sous Outils, ouvrez **Extensions**. Supprimez l’extension Application Insights. Puis sous Surveillance, sélectionnez Application Insights et créez ou sélectionnez la ressource souhaitée.

## <a name="build-the-app-with-application-insights"></a>Générez l’application avec Application Insights
Application Insights peut fournir des données de télémétrie détaillée par l’installation d’un SDK dans votre application. En particulier, vous pouvez collecter les journaux de suivi, [écrire une télémétrie personnalisée](app-insights-api-custom-events-metrics.md), et obtenir des rapports d’exception plus détaillées.

1. **Dans Visual Studio** (2013 mise à jour 2 ou ultérieure), configurez Application Insights pour votre projet.

    Cliquez avec le bouton droit sur le projet web et sélectionnez **Ajouter > Application Insights** ou **Configurer Application Insights**.
   
    ![Cliquez avec le bouton droit sur le projet web et sélectionnez Ajouter ou Configurer Application Insights.](./media/app-insights-azure-web-apps/03-add.png)
   
    Si vous êtes invité à vous connecter, utilisez les informations d'identification de votre compte Azure.
   
    Cette opération a deux effets :
   
   1. Création d’une ressource Application Insights dans Azure, à l’endroit où vos données de télémétrie sont stockées, analysées et affichées.
   2. Ajout du package NuGet Application Insights à votre code (si ce n’est pas déjà fait), et configuration du package pour l’envoi de données de télémétrie à la ressource Azure.
2. **Testez la télémétrie** en exécutant l’application sur votre ordinateur de développement (F5).
3. **Publiez l’application** sur Azure comme d’habitude. 

*Comment modifier la configuration pour envoyer des données vers une autre ressource Application Insights ?*

* Dans Visual Studio, cliquez avec le bouton droit sur le projet, choisissez **Configurer Application Insights** et sélectionnez la ressource de votre choix. Vous avez la possibilité de créer une nouvelle ressource. Procédez maintenant à la régénération et au redéploiement.

## <a name="explore-the-data"></a>Exploration des données
1. Dans le panneau Application Insights du panneau de configuration de l’application web, des mesures actives, indiquant les demandes et les échecs ou les deux survenant au cours d’une seconde, s’affichent. L’affichage de ces informations est très utile lorsque vous republiez votre application. Vous pouvez voir immédiatement les problèmes.
2. Cliquez sur la ressource Application Insights complète.

    ![Cliquez sur](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Vous pouvez également y accéder directement à partir de la navigation dans les ressources Azure.

1. Cliquez sur n’importe quel graphique pour afficher plus de détails :
   
    ![Dans le panneau de vue d’ensemble d’Application Insights, cliquez sur un graphique](./media/app-insights-azure-web-apps/07-dependency.png)
   
    Vous pouvez [personnaliser les panneaux de mesures](app-insights-metrics-explorer.md).
2. Cliquez de nouveau pour afficher des événements ainsi que leurs propriétés :
   
    ![Cliquez sur un type d’événement pour ouvrir une recherche filtrée sur ce type](./media/app-insights-azure-web-apps/08-requests.png)
   
    Notez le lien «...» qui permet d’ouvrir toutes les propriétés.
   
    Vous pouvez [personnaliser les recherches](app-insights-diagnostic-search.md).

Pour des recherches plus abouties sur vos données de télémétrie, utilisez le [langage de requête Log Analytics](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>Données de télémétrie supplémentaires

* [Données de chargement de pages web](app-insights-javascript.md)
* [Télémétrie personnalisée](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Étapes suivantes
* [Exécuter le profileur sur une application dynamique](app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - analyse les fonctions Azure avec Application Insights
* [Autorisation de l’envoi de diagnostics Azure](app-insights-azure-diagnostics.md) vers Application Insights.
* [Analyse des mesures d’intégrité du service](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Réceptions de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](app-insights-javascript.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](app-insights-monitor-web-app-availability.md) , pour recevoir des alertes en cas d’interruption de votre site.


