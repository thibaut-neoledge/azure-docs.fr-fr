---
title: "Spécifier des mesures et des paramètres de positionnement dans les microservices Azure | Microsoft Docs"
description: "Description d’un service Service Fabric en spécifiant des mesures, des contraintes de positionnement et d’autres stratégies de positionnement."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: c1df3b77f3fa621a60d6ab73f6dc2c24abbc3366


---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Configuration des paramètres Cluster Resource Manager pour les services Service Fabric
Service Fabric Cluster Resource Manager assure un contrôle précis des règles qui régissent chaque service nommé individuel. Chaque instance de service nommé peut spécifier des règles pour son allocation dans le cluster. Chaque service nommé peut également définir l’ensemble de mesures à inclure dans ses rapports, notamment leur importance pour ce service. La configuration des services se décompose en trois tâches :

1. Configuration des contraintes de positionnement
2. Configuration des mesures
3. Configuration des stratégies et des autres règles de positionnement avancées (moins fréquent)

## <a name="placement-constraints"></a>Contraintes de placement
Les contraintes de placement sont utilisées pour contrôler sur quels nœuds du cluster un service peut s’exécuter. En général, l’exécution d’une instance de service nommé spécifique ou de tous les services d’un type donné est limitée à un type de nœud particulier. Cela dit, les contraintes de placement sont extensibles. Vous pouvez définir n’importe quel jeu de propriétés basé sur un type de nœud, puis le sélectionner ensuite avec des contraintes lors de la création du service. Les contraintes de placement peuvent également être mises à jour de manière dynamique durant la durée de vie du service, ce qui vous permet de répondre aux modifications apportées dans le cluster. Les propriétés d’un nœud donné peuvent également être mises à jour dynamiquement dans le cluster. Vous trouverez plus d’informations sur les contraintes de placement et leur configuration dans [cet article](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties).

## <a name="metrics"></a>Mesures
Les mesures correspondent à l’ensemble de ressources dont une instance de service nommé a besoin. La configuration de mesures d’un service détermine notamment la quantité d’une ressource que chaque réplica avec état ou instance sans état de ce service consomme par défaut. Les mesures comprennent également un poids qui indique l’importance de l’équilibrage de la mesure pour le service, dans le cas où un compromis est nécessaire.

## <a name="other-placement-rules"></a>Autres règles de placement
Il existe d’autres types de règles de positionnement qui sont utiles dans les clusters répartis géographiquement ou dans d’autres scénarios moins courants. La configuration des autres règles de positionnement s’effectue via Corrélations ou Stratégies.

## <a name="next-steps"></a>Étapes suivantes
* Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces mesures et sur leur configuration, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md).
* L’affinité est un mode que vous pouvez configurer pour vos services. Ce n’est pas courant, mais si nécessaire, vous trouverez plus d’informations [ici](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* Il existe de nombreuses règles de positionnement différentes qui peuvent être configurées sur votre service pour gérer des scénarios supplémentaires. Vous trouverez plus d’informations sur ces différentes stratégies de positionnement [ici](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
* Commencer au début et [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
* Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur [l’équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
* Cluster Resource Manager offre de nombreuses options pour la description du cluster. Pour en savoir plus sur ces options, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md).



<!--HONumber=Jan17_HO4-->


