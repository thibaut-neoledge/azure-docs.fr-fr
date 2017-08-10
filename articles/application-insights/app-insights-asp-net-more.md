---
title: "Tirer parti d’Azure Application Insights | Microsoft Docs"
description: "Après avoir pris en main Application Insights, voici un résumé des fonctionnalités que vous pouvez explorer."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: sewhee
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f9d586a140b27f672f8cff463ba0607e2bd844f
ms.openlocfilehash: 6f2a184242f3f69bdc4a15ac02c095a45c723565
ms.contentlocale: fr-fr
ms.lasthandoff: 02/08/2017

---
# <a name="more-telemetry-from-application-insights"></a>Plus de télémétrie dans Application Insights
Une fois que vous avez [ajouté Application Insights à votre code ASP.NET](app-insights-asp-net.md), vous pouvez encore suivre quelques étapes supplémentaires pour obtenir davantage de données de télémétrie. 

| Action | Ce que vous obtenez|
|---|---|
|(serveurs IIS) [Installer Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648) sur chaque ordinateur serveur.<br/>(Applications web Azure) Dans le panneau de configuration Azure de l’application web, ouvrez le panneau Application Insights.| [**Compteurs de performance**](app-insights-performance-counters.md)<br/>[**Exceptions**](app-insights-asp-net-exceptions.md) - arborescences détaillées des appels de procédure<br/>[**Dépendances**](app-insights-asp-net-dependencies.md)|
|[Ajouter l’extrait de code JavaScript à vos pages web](app-insights-javascript.md)|[Performances des pages](app-insights-web-track-usage.md), exceptions du navigateur, performances AJAX. Télémétrie côté client personnalisée.|
|[Créer des tests web de disponibilité](app-insights-monitor-web-app-availability.md)|Recevoir des alertes si votre site est indisponible|
|[Vérifier que buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) est généré par MSBuild|[Créer des annotations dans les graphiques de mesures ](https://blogs.msdn.microsoft.com/visualstudioalm/2013/11/14/implementing-deployment-markers-in-application-insights/)
|[Écrire des mesures et des événements personnalisés](app-insights-api-custom-events-metrics.md)|Compter des mesures et des événements commerciaux, suivre l’utilisation détaillée et plus encore.|
|[Profiler votre site en ligne](https://aka.ms/AIProfilerPreview)|Minutage de fonctions précis à partir de votre application web en ligne|







