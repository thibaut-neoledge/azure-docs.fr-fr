---
title: "Détection intelligente dans Application Insights | Microsoft Docs"
description: "Application Insights réalise une analyse télémétrique approfondie automatique de votre application et vous avertit des éventuels problèmes de performances."
services: application-insights
documentationcenter: windows
author: rakefetj
manager: douge
ms.assetid: 2eeb4a35-c7a1-49f7-9b68-4f4b860938b2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 63c901529b81c75f46f1b21219054817c148063a
ms.openlocfilehash: 30124e697af5f7560e8070ce43c5a985bd2c403b


---
# <a name="smart-detection-in-application-insights"></a>Détection intelligente dans Application Insights
 La détection intelligente vous informe automatiquement des éventuels problèmes de performances dans votre application web. Elle effectue une analyse proactive des données de télémétrie que votre application envoie à [Application Insights](app-insights-overview.md). S’ils détectent une augmentation soudaine du taux d’échec, ou des modèles anormaux de performances client ou serveur, vous recevez une alerte. Cette fonctionnalité ne nécessite aucune configuration. Elle fonctionne si votre application envoie suffisamment de données de télémétrie.

Vous pouvez accéder aux alertes de détection intelligente via les courriers électroniques que vous recevez et à partir du panneau Détection intelligente.

## <a name="review-your-smart-detections"></a>Passer en revue vos détections intelligentes
Vous pouvez découvrir des détections de deux manières :

* **Vous recevez un message électronique** de la part d’Application Insights. Voici un exemple typique :
  
    ![Alerte par courrier électronique](./media/app-insights-proactive-diagnostics/03.png)
  
    Cliquez sur le grand bouton pour afficher plus de détails dans le portail.
* **La vignette Détection intelligente** figurant sur le panneau d’aperçu de votre application indique le nombre d’alertes récentes. Cliquez sur la mosaïque pour afficher la liste des alertes récentes.

![Afficher les détections récentes](./media/app-insights-proactive-diagnostics/04.png)

Sélectionnez une alerte pour afficher les détails la concernant.

## <a name="what-problems-are-detected"></a>Quels sont les problèmes détectés ?
Il existe trois types de détection :

* [Détection intelligente des anomalies de type échec](app-insights-proactive-failure-diagnostics.md). Nous utilisons l’apprentissage automatique pour définir la fréquence attendue des demandes entraînant un échec pour votre application, en la mettant en corrélation avec la charge et d’autres facteurs. Si le taux d’échec est situé en dehors de la plage attendue, nous envoyons une alerte.
* [Détection intelligente des anomalies de performances](app-insights-proactive-performance-diagnostics.md). Nous recherchons des modèles anormaux en matière de temps de réponse et de taux d’échec tous les jours. Nous mettons en corrélation ces problèmes avec des propriétés telles que l’emplacement, le navigateur, le système d’exploitation client, l’instance de serveur et le moment de la journée.
* [Détection intelligente - Dépannage de problèmes de service cloud Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Vous recevez des alertes si votre application est hébergée dans Azure Cloud Services et qu’une instance de rôle présente des échecs de démarrage, un recyclage fréquent ou des erreurs d’exécution.

(Les liens d’aide dans chaque notification vous renvoient vers les articles pertinents.)

## <a name="next-steps"></a>Étapes suivantes
Ces outils de diagnostic vous aident à inspecter les données de télémétrie à partir de votre application :

* [Metrics Explorer](app-insights-metrics-explorer.md)
* [Navigateur de recherche](app-insights-diagnostic-search.md)
* [Analytics : un puissant langage de requête](app-insights-analytics-tour.md)

La détection intelligente est entièrement automatique. Mais vous souhaitez peut-être configurer des alertes supplémentaires ?

* [Alertes de mesures configurées manuellement](app-insights-alerts.md)
* [Tests web de disponibilité](app-insights-monitor-web-app-availability.md) 




<!--HONumber=Nov16_HO3-->


