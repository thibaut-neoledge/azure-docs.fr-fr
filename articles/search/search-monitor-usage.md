---
title: "Surveillance de l’utilisation et des statistiques dans un service Recherche Azure | Microsoft Docs"
description: "Suivez la consommation de ressource et la taille de l&quot;index pour Azure Search, un service de recherche cloud hébergé sur Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Surveillance de l’utilisation et des métriques de requêtes dans un service Recherche Azure
La Recherche Azure collecte des statistiques sur l’exécution des requêtes au niveau du service, notamment sur les requêtes par seconde (RPS), la latence et le pourcentage de requêtes supprimées en cas de dépassement de la capacité. Vous pouvez consulter les métriques dans le portail via le panneau Surveillance.

   ![Capture d’écran de l’activité RPS][5]

Cet article décrit les métriques pour l’ensemble du service. Pour obtenir des informations sur l’activité au niveau de l’index, activez l’analyse du trafic des recherches et utilisez Power BI pour afficher les résultats de l’analyse. Pour démarrer, consultez [Activation et utilisation de la fonctionnalité Rechercher l’analyse du trafic](search-traffic-analytics.md) .

## <a name="view-query-throughput-statistics"></a>Afficher les statistiques de débit des requêtes
Dans le tableau de bord du service, cliquez sur la mosaïque Surveillance pour ouvrir le panneau Surveillance.

   ![Mosaïque Surveillance][2]

L’activité des requêtes au niveau du service est enregistrée pour l’exécution des requêtes, la latence et la limitation. Les métriques sont continuellement collectées. Toutefois, la mise à jour du portail peut prendre plusieurs minutes. 

Cliquez sur une mosaïque de métriques pour afficher les détails, ajouter des alertes, activer les diagnostics ou modifier le graphique.

  ![Commandes du panneau Métrique][4]

### <a name="set-up-alerts"></a>Configurer des alertes
Dans la page de détails des métriques, vous pouvez configurer des alertes pour déclencher une notification par e-mail lorsque l’activité associée à l’exécution des requêtes, à la latence ou à la limitation dépasse les critères définis pour celle-ci.

### <a name="enable-diagnostics"></a>Activation des diagnostics
Lorsque vous activez les diagnostics, vous pouvez configurer l’emplacement de stockage des données de diagnostic, décider ou non d’inclure les journaux des opérations et les métriques, ainsi que définir la durée de conservation des données.

### <a name="change-chart-type-and-data-collection-interval"></a>Modifier le type de graphique et l’intervalle de collecte des données
Pour chaque métrique, vous pouvez cliquer sur **Modifier** pour remplacer le graphique linéaire par un graphique à barres, ou modifier l’intervalle de temps de l’axe des abscisses.

  ![Configuration des intervalles de temps][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>Afficher les chiffres et l’utilisation des ressources dans le portail
Le suivi de la croissance des index et de la taille des documents peut vous aider à ajuster la capacité de manière proactive avant d’atteindre la limite supérieure définie pour votre service. 

Pour surveiller l’utilisation des ressources, affichez les chiffres et les statistiques de votre service dans le [portail](https://portal.azure.com). Vous pouvez également obtenir ces informations par programmation en créant un outil d’administration de service personnalisé.

1. Connectez-vous au [portail](https://portal.azure.com). 
2. Cliquez sur le tableau de bord des services de votre service Azure Search. Les mosaïques associées au service se trouvent sur la page d’accueil. Vous pouvez aussi accéder au service à partir de l’option de navigation de la barre de lancement. 

La partie Utilisation comprend un indicateur affichant les ressources disponibles en cours d’utilisation. Pour plus d’informations sur les limites par service des index, des documents et du stockage, consultez [Limites du service](search-limits-quotas-capacity.md).

  ![Mosaïque Utilisation][1]

> [!NOTE]
> La capture d’écran ci-dessus correspond au service Gratuit, qui peut comprendre au maximum un réplica et une partition, et peut uniquement héberger trois index, 10 000 documents ou 50 Mo de données, selon ce qui se présente en premier. Les services créés avec un niveau De base ou Standard ont des limites de service bien plus étendues. Pour plus d’informations sur le choix des niveaux, consultez [Choisir une référence (SKU) ou un niveau tarifaire](search-sku-tier.md).
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>Obtention de statistiques d’index avec l’API REST
L’API REST Azure Search et le Kit de développement .NET fournissent un accès par programme aux mesures de service.  Si vous utilisez des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) pour charger un index à partir d’Azure SQL Database ou DocumentDB, une API supplémentaire vous permet d’obtenir les chiffres requis. 

* [Obtention de statistiques d’index](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [Nombre de documents](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [Obtention de l’état d’indexeur](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils sur la façon d’équilibrer l’allocation des partitions et des réplicas d’un service, consultez [Mise à l’échelle des réplicas et des partitions](search-limits-quotas-capacity.md). 

Pour plus d’informations sur l’administration des services, consultez [Gestion du service Recherche sur Microsoft Azure](search-manage.md). Pour obtenir de l’aide sur le paramétrage, consultez [Performances et optimisation](search-performance-optimization.md).

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


