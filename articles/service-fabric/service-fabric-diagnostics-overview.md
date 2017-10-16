---
title: "Présentation de la surveillance et des diagnostics Azure Service Fabric | Microsoft Docs"
description: "Découvrez la surveillance et les diagnostics pour les clusters, applications et services Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Surveillance et diagnostics pour Azure Service Fabric

La surveillance et les diagnostics sont essentiels au développement, au test et au déploiement d’applications et de services dans tout environnement. Les solutions Service Fabric conviennent mieux pour planifier et implémenter une surveillance et des diagnostics afin de s’assurer que les applications et services fonctionnent comme prévu dans un environnement de développement local ou de production.

Les principaux objectifs de la surveillance et des diagnostics sont de :
* Détecter et diagnostiquer les problèmes de matériel et d’infrastructure
* Détecter les problèmes des logiciels et applications, et réduire les temps d’arrêt de service
* Comprendre la consommation des ressources et aider aux prises de décision relatives aux opérations
* Optimiser les performances des applications, des services et de l’infrastructure
* Générer des connaissances professionnelles et identifier les domaines d’amélioration


Le flux de travail global de la surveillance et des diagnostics se compose de trois étapes :

1. **Génération des événements** : cela inclut les événements (journaux, traces, événements personnalisés) au niveau de l’infrastructure (cluster), de la plateforme et de l’application/du service.
2. **Agrégation d’événements** : les événements générés doivent être collectés et agrégés avant de pouvoir être affichés
3. **Analyse** : les événements doivent être visualisé et accessibles dans un certain format, pour permettre l’analyse et l’affichage comme souhaité

Plusieurs produits couvrant ces trois domaines sont disponibles, et vous êtes libre de choisir des technologies différentes pour chacun d’eux. Vous devez vous assurer que les différents composants s’associent parfaitement afin de fournir une solution de surveillance de bout en bout pour votre cluster.

## <a name="event-generation"></a>Génération d’événement

La première étape du flux de travail de surveillance et de diagnostics consiste à créer et à générer des événements et des journaux. Ces événements, journaux et traces sont générés à deux niveaux : la couche de plate-forme (y compris le cluster, les machines ou les actions Service Fabric) ou la couche Application (n’importe quelle instrumentation ajoutée aux applications et services déployés sur le cluster). Les événements à chacun de ces niveaux sont personnalisables, bien que Service Fabric ne fournisse pas certaines instrumentations par défaut.

Pour comprendre ce qui est fourni et comment ajouter une instrumentation, vous pouvez poursuivre votre lecture sur les [événements de niveau plateforme](service-fabric-diagnostics-event-generation-infra.md) et les [événements de niveau application](service-fabric-diagnostics-event-generation-app.md).

Après avoir choisi le fournisseur de journalisation à utiliser, vous devez vérifier que vos journaux sont agrégés et stockés correctement.

## <a name="event-aggregation"></a>Agrégation des événements

Pour la collecte des journaux et des événements générés par vos applications et cluster, nous recommandons généralement d’utiliser [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (qui s’apparente davantage à la collecte de journaux en fonction de l’agent) ou [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (collecte de journaux In-process).

La collecte des journaux d’application à l’aide de l’extension Azure Diagnostics s’avère efficace pour Service Fabric si l’ensemble de sources et de destinations des journaux ne change pas souvent et s’il existe un mappage simple entre les sources et les destinations. En effet, comme la configuration d’Azure Diagnostics se produit au niveau de la couche du Gestionnaire des ressources, apporter des modifications importantes à la configuration nécessite la mise à jour/le redéploiement du cluster. En outre, son utilisation est particulièrement appropriée pour s’assurer que les journaux sont stockés dans un endroit un peu plus permanent, à partir duquel ils sont accessibles par différentes plateformes d’analyse. Cela signifie qu’elle finit par être moins efficace comme pipeline que l’utilisation d’EventFlow.

Grâce à [EventFlow](https://github.com/Azure/diagnostics-eventflow), vous pouvez faire en sorte que les services envoient leurs journaux directement à une plateforme d’analyse et de visualisation, et/ou au stockage. D’autres bibliothèques (ILogger, Serilog, etc.) peuvent être utilisées dans le même but, mais EventFlow présente l’avantage d’avoir été conçu spécifiquement pour la collecte de journaux In-process et pour prendre en charge les services Service Fabric. Il peut en découler plusieurs avantages :

* Simplicité de configuration et de déploiement
    * La configuration de la collecte des données de diagnostic s’inscrit simplement dans le cadre de la configuration du service. Elle reste toujours facilement « synchronisée » avec le reste de l’application.
    * Vous pouvez facilement réaliser une configuration par application ou par service.
    * La configuration des destinations de données par le biais d’EventFlow consiste simplement à ajouter le package NuGet approprié et à modifier le fichier *eventFlowConfig.json*.
* Flexibilité
    * L’application peut envoyer les données partout où elles sont nécessaires, tant qu’il existe une bibliothèque cliente capable de prendre en charge le système de stockage ciblé. Vous pouvez ajouter de nouvelles destinations selon vos besoins.
    * Vous pouvez également implémenter des règles de capture, de filtrage et d’agrégation de données complexes.
* Accès aux données d’application internes et contexte
    * Le sous-système de diagnostic exécuté à l’intérieur du processus d’application/service peut facilement enrichir les traces d’informations contextuelles.

Notez que ces deux options ne sont pas mutuellement exclusives, et que bien que vous puissiez effectuer un travail aussi bien avec l’une qu’avec l’autre, vous pouvez configurer les deux si cela vous semble pertinent. Dans la plupart des cas, combiner un agent dans la collection In-process peut aboutir à un flux de travail de surveillance plus fiable. Vous pouvez choisir l’extension Azure Diagnostics (agent) pour les journaux de niveau plateforme et EventFlow (collection In-process) pour les journaux de niveau application. Après avoir déterminé ce qui vous convient le mieux, vous pouvez vous pencher sur l’affichage et l’analyse de vos données.

## <a name="event-analysis"></a>Analyse des événements

Plusieurs plateformes formidables sont disponibles sur le marché pour l’analyse et la visualisation des données de surveillance et de diagnostic. Nous recommandons particulièrement [OMS](service-fabric-diagnostics-event-analysis-oms.md) et [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) en raison de leur meilleure intégration à Service Fabric, mais vous devez également vous pencher sur la [Suite Elastic](https://www.elastic.co/products) (surtout si vous envisagez d’exécuter un cluster dans un environnement hors connexion), [Splunk](https://www.splunk.com/) ou toute autre plateforme de votre choix.

Quelle que soit la plateforme que vous choisissez, ses points clés doivent inclure la convivialité de l’interface utilisateur et des options de requêtes, la possibilité de visualiser les données et de créer des tableaux de bord faciles à lire, ainsi que les outils supplémentaires qu’elle met à votre disposition pour améliorer la surveillance, tels que l’alerte automatisée.

Outre la plateforme choisie, quand vous configurez un cluster Service Fabric en tant que ressource Azure, vous disposez immédiatement des fonctionnalités de surveillance d’Azure pour les machines, ce qui peut être utile pour la surveillance du niveau de performance et de métriques spécifiques.

### <a name="azure-monitor"></a>Azure Monitor

Vous pouvez utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) pour surveiller un grand nombre des ressources Azure sur lesquelles repose un cluster Service Fabric. Un ensemble de mesures pour le [groupe de machines virtuelles identiques](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) et les [machines virtuelles](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuelles est automatiquement collecté et affiché dans le portail Azure. Pour consulter les informations collectées, dans le portail Azure, sélectionnez le groupe de ressources qui contient le cluster Service Fabric. Ensuite, sélectionnez le groupe de machines virtuelles identiques à afficher. Dans la section **Surveillance**, sélectionnez **Mesures** pour afficher un graphe des valeurs.

![Vue des informations de mesure collectées sur le portail Azure](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Pour personnaliser les graphiques, suivez les instructions présentées dans la [vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Vous pouvez également créer des alertes basées sur ces mesures, comme décrit dans l’article [Créer des alertes dans Azure Monitor pour les services Azure](../monitoring-and-diagnostics/insights-alerts-portal.md). Vous pouvez envoyer des alertes à un service de notification à l’aide de webhooks, comme décrit dans l’article [Configurer un webhook sur une alerte de métrique Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure Monitor ne prend en charge qu’un seul abonnement. Si vous avez besoin de surveiller plusieurs abonnements ou de fonctionnalités supplémentaires, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), composant de Microsoft Operations Management Suite, offre une solution de gestion informatique holistique aussi bien pour les infrastructures locales que cloud. Vous pouvez acheminer les données d’Azure Monitor directement dans Log Analytics afin d’afficher les mesures et les journaux pour l’ensemble de votre environnement dans un seul et même emplacement.

## <a name="next-steps"></a>Étapes suivantes

### <a name="watchdogs"></a>Agents de surveillance

Un agent de surveillance est un service distinct qui peut surveiller l’intégrité et la charge des services, et créer des rapports d’intégrité pour n’importe quel élément de la hiérarchie du modèle d’intégrité. Cela permet d’éviter des erreurs qui ne seraient pas détectées à partir de l’affichage d’un service unique. Les agents de surveillance sont également un bon emplacement pour héberger du code qui exécute des actions correctives sans intervention de l’utilisateur (par exemple, le nettoyage de fichiers journaux dans le stockage à intervalles réguliers). Vous trouverez un exemple d’implémentation de service d’agent de surveillance [ici](https://github.com/Azure-Samples/service-fabric-watchdog-service).

Découvrez comment les événements et les journaux sont générés au [niveau plateforme](service-fabric-diagnostics-event-generation-infra.md) et au [niveau application](service-fabric-diagnostics-event-generation-app.md).