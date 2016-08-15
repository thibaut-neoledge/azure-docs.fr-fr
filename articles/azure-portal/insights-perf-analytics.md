<properties
	pageTitle="Surveillance des performances d’application web Azure | Microsoft Azure"
	description="Graphique de charge et de temps de réponse, informations de dépendance et alertes sur les performances."
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="awills"/>

# Surveillance des performances d'application web Azure

Dans le [portail Azure](https://portal.azure.com), vous pouvez configurer l’analyse pour collecter des statistiques et des détails sur les dépendances de l’application dans vos [applications web Azure](../app-service-web/app-service-web-overview.md) ou vos [machines virtuelles](../virtual-machines/virtual-machines-linux-about.md).

Azure prend en charge l’analyse des performances des applications (APM) en tirant parti des extensions. Ces dernières sont installées dans votre application et collectent les données qu’elles renvoient aux services d’analyse.

**Application Insights** et **New Relic** sont deux des extensions de surveillance des performances disponibles. Pour les utiliser, vous installez un agent lors de l'exécution. Application Insights permet également de générer votre code à l’aide d’un kit de développement logiciel. Le Kit de développement logiciel (SDK) vous permet d'écrire un code pour surveiller plus en détail l'utilisation et les performances de votre application.

## Application Insights

### (Facultatif) Régénérez l’application avec le Kit de développement logiciel (SDK)...

Application Insights peut fournir des données de télémétrie détaillée par l’installation d’un SDK dans votre application.

Dans Visual Studio (2013 mise à jour 2 ou ultérieure), ajoutez le Kit de développement logiciel (SDK) Application Insights à votre projet.

![Cliquez avec le bouton droit sur le projet web et sélectionnez Ajouter Application Insights.](./media/insights-perf-analytics/03-add.png)

Si vous êtes invité à vous connecter, utilisez les informations d'identification de votre compte Azure.

Cette opération a deux effets :

1. Création d’une ressource Application Insights dans Azure, à l’endroit où vos données de télémétrie sont stockées, analysées et affichées.
2. Ajout du package NuGet Application Insights à votre code, et configuration du package pour l’envoi de données de télémétrie à la ressource Azure.

Vous pouvez tester la télémétrie en exécutant l’application sur votre ordinateur de développement (F5) ou vous pouvez simplement republier l’application.

Le Kit de développement logiciel (SDK) contient une API qui vous permet d’[écrire de la télémétrie personnalisée](../application-insights/app-insights-api-custom-events-metrics.md) pour suivre l’utilisation.

### .. .ou définissez manuellement une ressource

Si vous n’avez pas ajouté le Kit de développement logiciel dans Visual Studio, vous devez configurer la ressource Application Insights dans Azure, à l’emplacement de stockage, d’analyse et d’affichage de vos données de télémétrie.

![Cliquez sur Ajouter, Services de développement, Application Insights. Choisissez le type d’application ASP.NET.](./media/insights-perf-analytics/01-new.png)


## Activation d’une extension

1. Accédez au panneau de contrôle de l’application web ou de la machine virtuelle que vous souhaitez instrumenter.

2. Ajoutez l’extension Application Insights ou New Relic.

    Si vous instrumentez une application web :

![Paramètres, Extensions, Ajouter, Application Insights](./media/insights-perf-analytics/05-extend.png)

Ou, si vous utilisez une machine virtuelle :

![Cliquez sur la vignette Analyse](./media/insights-perf-analytics/10-vm1.png)



## Exploration des données

1. Ouvrez la ressource Application Insights (soit directement à partir du menu Parcourir, soit à l’aide de l’outil d’analyse des performances de l’application web).

2. Cliquez sur n’importe quel graphique pour afficher plus de détails :

    ![Dans le panneau de vue d’ensemble d’Application Insights, cliquez sur un graphique](./media/insights-perf-analytics/07-dependency.png)

    Vous pouvez [personnaliser les panneaux de mesures](../application-insights/app-insights-metrics-explorer.md).

3. Cliquez de nouveau pour afficher des événements ainsi que leurs propriétés :

    ![Cliquez sur un type d’événement pour ouvrir une recherche filtrée sur ce type](./media/insights-perf-analytics/08-requests.png)

    Notez le lien «...» qui permet d’ouvrir toutes les propriétés.

    Vous pouvez [personnaliser les recherches](../application-insights/app-insights-diagnostic-search.md).

Pour des recherches plus abouties sur vos données de télémétrie, utilisez le [langage de requête Analytics](../application-insights/app-insights-analytics-tour.md).


## Questions et réponses

Comment modifier la configuration pour envoyer des données vers une autre ressource Application Insights ?

* *Si vous avez ajouté Application Insights à votre code dans Visual Studio :* cliquez avec le bouton droit sur le projet, choisissez **Application Insights > Configurer** et sélectionnez la ressource de votre choix. Vous avez la possibilité de créer une nouvelle ressource. Procédez maintenant à la régénération et au redéploiement.
* *Sinon :* dans Azure, ouvrez le panneau de contrôle de l’application web, puis accédez à **Outils > Extensions**. Supprimez l’extension Application Insights. Ouvrez **Outils > Performances**, sélectionnez « cliquez ici », puis choisissez Application Insights et la ressource de votre choix. (Commencez par cela si vous souhaitez créer une nouvelle ressource Application Insights.)


## Étapes suivantes

* [Analyse des mesures d’intégrité du service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Activation de la surveillance et des diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service.
* [Réceptions de notifications d'alerte](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un seuil.
* Utilisation [d’Application Insights pour les pages Web et les applications JavaScript](../application-insights/app-insights-web-track-usage.md) pour obtenir les données de télémétrie du client à partir des navigateurs qui consultent une page web.
* [Configuration des tests de disponibilité web](../application-insights/app-insights-monitor-web-app-availability.md), pour recevoir des alertes en cas d’interruption de votre site.

<!---HONumber=AcomDC_0803_2016-->