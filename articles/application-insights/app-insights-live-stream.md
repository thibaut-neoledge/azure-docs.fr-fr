---
title: "Flux de métriques temps réel dans Azure Application Insights | Microsoft Docs"
description: "Surveillez les performances de votre application web en temps réel, afin d’observer les effets d’une mise en production ou d’autres modifications."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 11b9f52801e9a62b9c52f0f622fdcae761cc122a
ms.lasthandoff: 04/18/2017


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Live Metrics Stream : mesures instantanées pour une analyse détaillée
Le flux de métriques temps réel vous montre vos mesures [Application Insights](app-insights-overview.md) à ce moment précis, avec une latence d’une seconde, proche du temps réel. Cette surveillance immédiate est très utile lorsque vous sortez une nouvelle version et que vous souhaitez vous assurer que tout fonctionne comme prévu, ou que vous enquêtez sur un incident en temps réel.

Contrairement à [Metrics Explorer](app-insights-metrics-explorer.md), le flux de métriques temps réel affiche un ensemble fixe de mesures. Les données persistent uniquement tant qu’elles se trouvent sur le graphique et sont ensuite ignorées.

Le flux de métriques temps réel est gratuit : il ne s’ajoute pas à votre facture. Il est disponible pour les applications ASP.NET et Java.

![Vidéo sur le flux de métriques temps réel](./media/app-insights-live-stream/youtube.png) [Vidéo sur le flux de métriques temps réel](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

![Dans le panneau Vue d'ensemble, cliquez sur Live Stream](./media/app-insights-live-stream/live-stream.png)



## <a name="live-failures"></a>Échecs dynamiques

Si des échecs ou des exceptions sont enregistrés, le flux temps réel en sélectionne un échantillon. Cliquez sur **Pause** pour conserver un échantillon spécifique, puis sélectionnez un événement pour en afficher les détails.

![Échecs dynamiques échantillonnés](./media/app-insights-live-stream/live-stream-failures.png)


Le flux de métriques temps réel est disponible avec la dernière version du [Kit de développement logiciel (SDK) Application Insights pour le web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).

## <a name="filter-by-server-instance"></a>Filtrer par instance de serveur

Si vous voulez surveiller une instance de rôle serveur spécifique, vous pouvez appliquer un filtre par serveur.

![Échecs dynamiques échantillonnés](./media/app-insights-live-stream/live-stream-filter.png)


## <a name="troubleshooting"></a>Résolution des problèmes

Pas de données ? Le flux de métriques temps réel utilise un port différent de celui des autres données de télémétrie Application Insights. Assurez-vous que [ces ports](app-insights-ip-addresses.md) sont ouverts dans votre pare-feu.



## <a name="next-steps"></a>Étapes suivantes
* [Surveillance de l’utilisation avec Application Insights](app-insights-web-track-usage.md)
* [Utilisation de Diagnostic Search](app-insights-diagnostic-search.md)


