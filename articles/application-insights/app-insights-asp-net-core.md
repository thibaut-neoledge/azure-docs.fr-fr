---
title: Azure Application Insights pour ASP.NET Core | Microsoft Docs
description: "Surveiller la disponibilité, les performances et l’utilisation.des applications Web."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 560db0f2a3ac6beb2664725514b0cbe4ae2be0d4
ms.contentlocale: fr-fr
ms.lasthandoff: 03/21/2017


---
# <a name="application-insights-for-aspnet-core"></a>Application Insights pour ASP.NET Core
[Application Insights](app-insights-overview.md) permet d’analyser la disponibilité, les performances et l’utilisation de votre application web. Avec les retours que vous obtenez sur les performances et l’efficacité de votre application dans la nature, vous pouvez prendre des décisions avisées sur la direction de la conception de chaque cycle de développement.

![Exemple](./media/app-insights-asp-net-core/sample.png)

Tout d’abord, vous avez besoin d’un abonnement à [Microsoft Azure](http://azure.com). Connectez-vous avec un compte Microsoft, que vous pouvez avoir pour Windows, XBox Live ou d’autres services cloud de Microsoft. Si votre équipe dispose d’un abonnement d’organisation, demandez à son propriétaire d’y ajouter votre compte Microsoft.

## <a name="getting-started"></a>Prise en main

* Dans l’Explorateur de solutions Visual Studio, cliquez avec le bouton droit sur votre projet et sélectionnez **Configurer Application Insights**, ou **Ajouter > Application Insights**. [Plus d’informations](app-insights-asp-net.md)
* Si vous ne voyez pas ces commandes de menu, suivez le [Guide de mise en route](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started). Vous devrez peut-être effectuer cette opération si votre projet a été créé avec une version de Visual Studio antérieure à 2017.

## <a name="using-application-insights"></a>Utilisation d’Application Insights
Connectez-vous au [portail Microsoft Azure](https://portal.azure.com), sélectionnez **Toutes les ressources** ou **Application Insights**, puis sélectionnez la ressource que vous avez créée pour contrôler votre application.

Dans une fenêtre de navigateur distincte, utilisez votre application pendant un certain temps. Des données s’affichent dans les graphiques Application Insights. (Vous devrez peut-être cliquer sur Actualiser.) Il y aura seulement une petite quantité de données pendant que vous effectuerez le développement, mais ces graphiques seront vraiment actifs lorsque vous publierez votre application et que vous aurez de nombreux utilisateurs. 

La page de vue d’ensemble présente les graphiques de performances clés : temps de réponse du serveur, temps de chargement des pages et nombre de demandes ayant échoué. Cliquez sur un graphique pour afficher d’autres graphiques et données.

Les affichages dans le portail se répartissent en trois catégories principales :

* [Metrics Explorer](app-insights-metrics-explorer.md) affiche les graphiques et les tables de mesures et de nombres, comme les temps de réponse, les taux de défaillance ou les mesures que vous créez vous-même avec l’[API](app-insights-api-custom-events-metrics.md). Filtrez et segmentez les données par valeurs de propriété pour obtenir une meilleure compréhension de votre application et de ses utilisateurs.
* L’[Explorateur de recherche](app-insights-diagnostic-search.md) répertorie les événements individuels, tels que les demandes, exceptions, suivis de journal ou événements spécifiques que vous avez créés vous-même avec l’[API](app-insights-api-custom-events-metrics.md). Filtrez et recherchez dans les événements et naviguez entre les événements connexes pour étudier les problèmes.
* [Analytics](app-insights-analytics.md) vous permet d’exécuter des requêtes SQL sur vos données de télémétrie. Il s’agit d’un puissant outil d’analyse et de diagnostic.

## <a name="alerts"></a>Alertes
* Vous obtenez automatiquement des [alertes de diagnostic proactives](app-insights-proactive-diagnostics.md) qui vous informent de modifications anormales des taux d’échec et d’autres mesures.
* Configurez les [tests de disponibilité](app-insights-monitor-web-app-availability.md) pour tester votre site web en continu à partir d’emplacements du monde entier et recevez des notifications par courrier électronique dès qu’un test échoue.
* Configurez les [alertes de mesures](app-insights-monitor-web-app-availability.md) pour savoir si les mesures telles que les temps de réponse ou les taux d’exception dépassent les limites acceptables.

## <a name="video"></a>Vidéo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="open-source"></a>Open source
[Lire et contribuer au code](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)


## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez la télémétrie à vos pages web](app-insights-javascript.md) pour surveiller l’utilisation et les performances des pages.
* [Surveillez les dépendances](app-insights-asp-net-dependencies.md) pour voir si REST, SQL ou d’autres ressources externes vous ralentissent.
* [Utilisez l’API](app-insights-api-custom-events-metrics.md) pour envoyer vos propres événements et mesures pour obtenir une vue plus détaillée des performances et de l’utilisation de votre application.
* [Les tests de disponibilité](app-insights-monitor-web-app-availability.md) vérifient votre application en permanence dans le monde entier. 


