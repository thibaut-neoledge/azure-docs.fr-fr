---
title: "Surveillance de l’utilisation et des statistiques dans un service Recherche Azure | Microsoft Docs"
description: "Suivez la consommation de ressource et la taille de l'index pour Azure Search, un service de recherche cloud hébergé sur Microsoft Azure."
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---
# <a name="monitoring-an-azure-search-service"></a>Surveillance d’un service Azure Search

Azure Search propose diverses ressources pour le suivi des performances et de l’utilisation des services de recherche. Il vous donne accès aux mesures, journaux, statistiques d’index et capacités d’analyse étendues sur Power BI. Cet article décrit comment activer les différentes stratégies de surveillance et comment interpréter les données résultantes.

## <a name="azure-search-metrics"></a>Mesures Azure Search
Les mesures vous donnent une visibilité en quasi temps réel sur votre service de recherche et sont disponibles pour chaque service, sans configuration supplémentaire. Elles vous permettent de suivre les performances de votre service pendant jusqu’à 30 jours.

Azure Search collecte les données de trois mesures différentes :

* Latence de recherche : délai nécessaire au service de recherche pour traiter les requêtes de recherche, agrégé par minute.
* Requêtes de recherche par seconde (QPS) : nombre de requêtes de recherche reçues par seconde, agrégé par minute.
* Pourcentage de requêtes de recherche limitées : pourcentage de requêtes de recherche qui ont été limitées, agrégé par minute.

![Capture d’écran de l’activité RPS][1]

### <a name="set-up-alerts"></a>Configurer des alertes
Dans la page de détails des métriques, vous pouvez configurer des alertes pour déclencher une notification par e-mail ou une action automatisée lorsqu’une mesure dépasse un seuil que vous avez défini.

Pour plus d’informations sur les mesures, consultez la documentation complète sur une analyse d’Azure.  

## <a name="how-to-track-resource-usage"></a>Suivi de l’utilisation d’une ressource
Le suivi de la croissance des index et de la taille des documents peut vous aider à ajuster la capacité de manière proactive avant d’atteindre la limite supérieure définie pour votre service. Vous pouvez le faire sur le portail ou par programme à l’aide de l’API REST.

### <a name="using-the-portal"></a>Utiliser le portail

Pour surveiller l’utilisation des ressources, affichez les chiffres et les statistiques de votre service dans le [portail](https://portal.azure.com).

1. Connectez-vous au [portail](https://portal.azure.com).
2. Cliquez sur le tableau de bord des services de votre service Azure Search. Les mosaïques associées au service se trouvent sur la page d’accueil. Vous pouvez aussi accéder au service à partir de l’option de navigation de la barre de lancement.

La partie Utilisation comprend un indicateur affichant les ressources disponibles en cours d’utilisation. Pour plus d’informations sur les limites par service des index, des documents et du stockage, consultez [Limites du service](search-limits-quotas-capacity.md).

  ![Mosaïque Utilisation][2]

> [!NOTE]
> La capture d’écran ci-dessus correspond au service Gratuit, qui peut comprendre au maximum un réplica et une partition, et peut uniquement héberger trois index, 10 000 documents ou 50 Mo de données, selon ce qui se présente en premier. Les services créés avec un niveau De base ou Standard ont des limites de service bien plus étendues. Pour plus d’informations sur le choix des niveaux, consultez [Choisir une référence (SKU) ou un niveau tarifaire](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Utilisation de l’API REST
L’API REST Azure Search et le Kit de développement .NET fournissent un accès par programme aux mesures de service.  Si vous utilisez des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx) pour charger un index à partir d’Azure SQL Database ou d’Azure Cosmos DB, une API supplémentaire vous permet d’obtenir les chiffres requis.

* [Obtention de statistiques d’index](/rest/api/searchservice/get-index-statistics)
* [Nombre de documents](/rest/api/searchservice/count-documents)
* [Obtention de l’état d’indexeur](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Comment exporter des journaux et des mesures

Vous pouvez exporter les journaux des opérations pour votre service et les données brutes pour les mesures décrites dans la section précédente. Les journaux des opérations vous indiquent le service utilisé et peuvent être consommés à partir de Power BI lorsque les données sont copiées vers un compte de stockage. Azure Search fournit un pack de contenu Power BI de surveillance à cet effet.


### <a name="enabling-monitoring"></a>Activation de la surveillance
Ouvrez votre service de recherche Azure dans le [portail Azure](http://portal.azure.com) sous l’option Activer la surveillance.

Choisissez les données que vous souhaitez exporter : journaux, mesures ou les deux. Vous pouvez les copier sur un compte de stockage, les envoyer à un Event Hub ou les exporter vers Log Analytics.

![Comment activer la surveillance dans le portail][3]

Pour activer à l’aide de PowerShell ou de l’interface de ligne de commande Azure, consultez la documentation qui se trouve [ici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Journaux et schémas de mesure
Lorsque les données sont copiées vers un compte de stockage, les données sont au format JSON et se trouvent dans deux conteneurs :

* journaux-Insights-operationlogs : pour les journaux de trafic de recherche
* mesures-Insights-pt1m : pour les mesures

Il y a un seul objet blob par heure et par conteneur.

Exemple de chemin d’accès : `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Schéma du journal
Les objets blob de journaux contiennent les journaux du trafic de votre service de recherche.
Chaque objet blob a un objet racine appelé **enregistrements** qui contient un tableau d’objets du journal.
Chaque objet blob comporte des enregistrements relatifs à l’ensemble de l’opération qui s’est déroulée au cours de la même heure.

| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| time |datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| operationName |string |« Query.Search » |Nom de l’opération |
| operationVersion |string |« 2015-02-28 » |Version d’API utilisée |
| category |string |« OperationLogs » |constant |
| resultType |string |« Success » |Valeurs possibles : Réussite ou Échec |
| resultSignature |int |200 |Code de résultat HTTP |
| durationMS |int |50 |Durée de l’opération en millisecondes |
| properties |objet |consultez le tableau suivant |Objet contenant des données propres à l’opération |

**Schéma de propriétés**
| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| Description |string |« GET /indexes(’content’)/docs » |Point de terminaison de l’opération |
| Interroger |string |« ?search=AzureSearch&$count=true&api-version=2015-02-28 » |Paramètres de requête |
| Documents |int |42 |Nombre de documents traités |
| IndexName |string |« testindex » |Nom de l’index associé à l’opération |

#### <a name="metrics-schema"></a>Schéma de mesures
| Nom | Type | Exemple | Remarques |
| --- | --- | --- | --- |
| resourceId |string |«/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE » |Votre ID de ressource |
| metricName |string |« Latency » |Nom de la mesure |
| time |datetime |« 2015-12-07T00:00:43.6872559Z » |Horodatage de l’opération |
| average |int |64 |Valeur moyenne des échantillons bruts dans l’intervalle de temps de la mesure |
| minimum |int |37 |Valeur minimale des échantillons bruts dans l’intervalle de temps de la mesure |
| maximum |int |78 |Valeur maximale des échantillons bruts dans l’intervalle de temps de la mesure |
| total |int |258 |Valeur totale des échantillons bruts dans l’intervalle de temps de la mesure |
| count |int |4 |Nombre d’échantillons bruts utilisés pour générer la mesure |
| timegrain |string |« PT1M » |Fragment de temps de la mesure au format ISO 8601 |

Toutes les mesures sont consignées dans des intervalles d’une minute. Chaque mesure expose des valeurs minimales, maximales et moyennes par minute.

Dans le cas de la mesure SearchQueriesPerSecond, la valeur minimale correspondra à la valeur la plus faible des requêtes de recherche par seconde qui a été enregistrée pendant cette minute. Il en va de même pour la valeur maximale. La moyenne représentera l’agrégat de ces valeurs pour toute la minute.
Imaginez ce scénario : pendant une minute, vous pouvez avoir 58 secondes de charge très élevée, qui représentera votre valeur SearchQueriesPerSecond maximale, puis 58 secondes de charge moyenne, et enfin une seconde avec une seule requête, qui représentera la valeur minimale.

Pour ThrottledSearchQueriesPercentage, les valeurs minimales, maximales, moyennes et totales seront identiques : il s’agit du pourcentage de requêtes de recherche qui ont été limitées, en fonction du nombre total de requêtes de recherche pendant une minute.

## <a name="analyzing-your-data-with-power-bi"></a>Analyse de vos données avec Power Bi

Nous vous recommandons d’utiliser [Power BI](https://powerbi.microsoft.com) pour explorer et visualiser vos données. Vous pouvez facilement vous connecter à votre compte de Stockage Azure et rapidement commencer à analyser vos données.

Azure Search fournit un [Pack de contenu Power BI](https://app.powerbi.com/getdata/services/azure-search) qui vous permet de surveiller et de comprendre votre trafic avec des tables et graphiques prédéfinis. Il contient un ensemble de rapports Power BI qui se connectent automatiquement à vos données et fournissent un éclairage visuel sur votre service de recherche. Pour plus d’informations, consultez la [Page d’aide du pack de contenu](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Tableau de bord Power BI pour Azure Search][4]

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des conseils sur la façon d’équilibrer l’allocation des partitions et des réplicas d’un service, consultez [Mise à l’échelle des réplicas et des partitions](search-limits-quotas-capacity.md).

Pour plus d’informations sur l’administration des services, consultez [Gestion du service Recherche sur Microsoft Azure](search-manage.md). Pour obtenir de l’aide sur le paramétrage, consultez [Performances et optimisation](search-performance-optimization.md).

En savoir plus sur la création de rapports exceptionnels. Pour plus d’informations, consultez [Prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/).

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png

