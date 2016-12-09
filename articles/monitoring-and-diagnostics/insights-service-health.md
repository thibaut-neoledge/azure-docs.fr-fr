---
title: "Suivi de l’intégrité du service Azure à l’aide des journaux d’activité Azure Monitor | Microsoft Docs"
description: "Déterminez à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances. "
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 30db9258-98e6-4855-abcd-f06c06277aa8
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d31ee12c783b845e0a8ff1791d21b2ef9013eee3


---
# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Suivi de l’intégrité du service Azure à l’aide des journaux d’activité Azure Monitor
Azure publie chaque interruption du service et chaque dégradation des performances. Vous pouvez parcourir ces événements dans le portail Azure, et également utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l’ensemble des événements par programme.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Recherche des journaux d’état de service pour votre abonnement
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans **Accueil**, vous devez voir une vignette appelée **État du service**. Cliquez dessus.
   
    ![Accueil](./media/insights-service-health/Insights_Home.png)
3. Vous verrez une liste de toutes les régions dans Azure. Cliquez sur n’importe quelle région pour afficher la requête de journal d’activité qui affiche les incidents de service qui ont un impact sur un de vos abonnements au cours des 24 dernières heures.
   
    ![État du service d’abonnement de journal d’activité](./media/insights-service-health/AzureActivityLogServiceHealth3.png)
4. Vous pouvez également voir les détails de chaque incident en sélectionnant cet événement dans la table.
5. Modifiez **l’intervalle de temps** pour afficher une période plus longue.

## <a name="next-steps"></a>Étapes suivantes
* [Surveillance de la disponibilité et de la réactivité des pages Web](../application-insights/app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.




<!--HONumber=Nov16_HO3-->


