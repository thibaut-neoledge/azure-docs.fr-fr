<properties 
   pageTitle="Analyse des performances de l’application" 
   description="Chargement et temps de réponse des graphiques, informations sur les dépendances et définition d’alertes sur les performances." 
   services="azure-portal" 
   documentationCenter="na" 
   authors="alancameronwills" 
   manager="keboyd"/> 

<tags 
   ms.service="azure-portal" 
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="04/28/2015"
   ms.author="awills"/> 

# Analyse des performances de l’application 

Dans le [portail Azure](http://portal.azure.com), vous pouvez configurer l’analyse pour collecter des statistiques et informations détaillées sur les dépendances d’application dans vos applications web ou machines virtuelles. 

Azure prend en charge l’Analyse des Performances des Applications (*APM*) en tirant parti des *extensions*. Ces dernières sont installées dans votre application et collectent les données qu’elles renvoient aux services d’analyse. 

## Activation d’une extension 

1. Cliquez sur **Parcourir** et sélectionnez l’application web ou la machine virtuelle que vous souhaitez instrumenter. 
2. Cliquez sur la vignette **Analyse des applications** sous *Analyse**. 
3. Choisissez le fournisseur d’extensions à utiliser, tel que **Application Insights** ou **New Relic**. 

![Web app APM](./media/insights-perf-analytics/05-extend.png) 

Ou si vous utilisez une machine virtuelle : 

![Virtual machine](./media/insights-perf-analytics/10-vm1.png)

## Exploration des données 
Utilisez votre application pendant un certain temps pour générer quelques télémétries. 

1. Ensuite, l’extension est affichée comme étant installée dans le panneau de votre application web ou machine virtuelle. 
2. Cliquez sur la ligne qui représente votre application pour accéder à ce fournisseur : 
![Click Refresh](./media/insights-perf-analytics/06-overview.png) 

Vous pouvez également utiliser **Parcourir** pour accéder directement au composant Application Insights ou au compte New Relic que vous avez utilisé. Dans le panneau Application Insights, vous pouvez par exemple : - Ouvrir Performances : 

![Dans le panneau de vue d’ensemble Application Insights, cliquez sur la vignette Performances](./media/insights-perf-analytics/07-dependency.png) 

- Accédez aux requêtes individuelles : 

![Dans la grille, cliquez sur une dépendance pour voir les requêtes associées.](./media/insights-perf-analytics/08-requests.png)
 
- Voici un exemple montrant le temps passé dans une dépendance SQL, y compris le nombre d’appels SQL et les statistiques associées telles que la durée moyenne et l’écart standard. 
![](./media/insights-perf-analytics/01-example.png) 

### Les autres composants requis pour Application Insights 

New Relic peuvent être automatiquement installés via une instrumentation supplémentaire, mais Application Insights a une condition requise supplémentaire. 

![Dans la boîte de dialogue Nouveau projet, cochez Ajouter Application Insights](./media/insights-perf-analytics/03-add.png) 

Lorsque vous êtes invité à vous connecter, utilisez les informations d’identification de votre compte Azure. 

## Étapes suivantes 
* [Analyser les mesures d’intégrité des services](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif. 
* [Activer l’analyse et les diagnostics](insights-how-to-use-diagnostics.md) pour collecter des mesures détaillées à fréquence élevée sur votre service. 
* [Recevoir des notifications d’alerte ](insights-receive-alert-notifications.md) lorsque des événements opérationnels se produisent ou que des mesures dépassent un certain seuil. 
* Utiliser [Application Insights pour les pages web et applications JavaScript](app-insights-web-track-usage.md) pour obtenir une analyse cliente sur les navigateurs qui consultent une page web. 
* [Analyser la disponibilité et la réactivité d’une page web](app-insights-monitor-web-app-availability.md) avec Application Insights pour voir si une page est inactive.

<!--HONumber=54-->