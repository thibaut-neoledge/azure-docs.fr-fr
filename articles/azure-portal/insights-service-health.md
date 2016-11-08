---
title: Suivi de l’intégrité du service à l’aide des analyses Azure | Microsoft Docs
description: 'Déterminez à quel moment Azure a rencontré des interruptions de service ou une dégradation des performances. '
author: rboucher
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb

---
# Suivi de l’intégrité du service
Azure publie chaque interruption du service et chaque dégradation des performances. Vous pouvez parcourir ces événements dans le portail Azure, et également utiliser l’[API REST](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou le [Kit de développement logiciel (SDK) .NET](https://www.nuget.org/packages/Microsoft.Azure.Insights/) pour accéder à l’ensemble des événements par programme.

## Voir l’intégrité du service par région
1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans **Accueil**, vous devez voir une vignette appelée **État du service** ![Accueil](./media/insights-service-health/Insights_Home.png)
3. En cliquant sur cette vignette, vous obtenez une liste de toutes les régions dans Azure. Vous pouvez cliquer sur n’importe quelle région pour consulter l’historique de l’intégrité du service pour cette région. ![Accueil](./media/insights-service-health/Insights_Regions.png)
4. Vous pouvez également voir les détails de chaque incident en le sélectionnant dans la table.

## Parcourir l’intégralité des journaux de contrôle d’intégrité
1. Cliquez sur **Parcourir** et sélectionnez **Journaux d’audit**. ![Parcourir le Hub](./media/insights-service-health/Insights_Browse.png)
2. Vous ouvrirez ainsi un panneau vous indiquant tous les événements qui ont eu un impact sur l’un de vos abonnements au cours des 7 derniers jours. Les entrées du contrôle d’intégrité du service sont affichées dans cette liste, mais elles peuvent être difficiles à trouver, car cette la liste peut être volumineuse.
3. Cliquez sur la commande **Filtrer**.
4. Sélectionnez **Catégorie d’événement** et choisissez **État du service** : ![Tous les événements](./media/insights-service-health/Insights_Filter.png)
5. Cliquez sur **Mis à jour**.
6. Vous voyez maintenant tous les événements de contrôle d’intégrité de service qui ont un impact sur votre abonnement : ![Groupes de ressources](./media/insights-service-health/Insights_HealthEvent.png)
7. Vous pouvez vous rendre ainsi dans le panneau des détails pour découvrir les détails de l’événement.

## Étapes suivantes
* [Réception de notifications d'alerte](insights-receive-alert-notifications.md) lorsqu’un événement se produit.
* [Surveillance des mesures de service](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
* [Surveillance de la disponibilité et de la réactivité des pages Web](../application-insights/app-insights-monitor-web-app-availability.md) avec Application Insights pour déterminer si vos pages sont inactives.

<!---HONumber=AcomDC_0810_2016-->