<properties
   pageTitle="Configuration de services avec Service Fabric Cluster Resource Manager | Microsoft Azure"
   description="Description d’un service Service Fabric en spécifiant des mesures, des contraintes de placement et d’autres stratégies de positionnement."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="masnider"/>


# Configuration des paramètres Cluster Resource Manager pour les services Service Fabric
Service Fabric Cluster Resource Manager permet d’obtenir un contrôle détaillé des règles qui régissent chaque service nommé individuel. Chaque instance de service peut spécifier des règles pour son allocation dans le cluster. Chaque instance peut également définir l’ensemble de mesures qu’elle souhaite inclure dans un rapport, y compris leur importance pour ce service. En général, la configuration des services se décompose en trois tâches :

1. Configuration des contraintes de positionnement
2. Configuration des mesures
3. Configuration des règles de placement avancées (moins fréquent)

Examinons chacun d’elles :

## Contraintes de placement
Les contraintes de placement sont utilisées pour contrôler sur quels nœuds du cluster un service peut s’exécuter. En général, une instance de service nommé spécifique ou tous les services d’un type doivent s’exécuter sur un type particulier de nœud, mais les contraintes de placement sont extensibles : vous pouvez définir n’importe quel jeu de propriétés sur la base d’un type de nœud, puis les sélectionner avec les contraintes lors de la création du service. Les contraintes de placement peuvent également être mises à jour de manière dynamique durant la durée de vie du service, ce qui vous permet de répondre aux modifications apportées dans le cluster. Vous trouverez plus d’informations sur les contraintes de placement et leur configuration dans [cet article](service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-properties).

## Mesures
Les mesures sont la liste des ressources que ce service doit prendre en compte, notamment des informations sur la quantité de la ressource que chaque réplica ou instance de ce service utilise par défaut. Les mesures comprennent également une pondération qui indique l’importance de la mesure pour le service, en cas de compromis nécessaire.

## Autres règles de placement
Il existe d’autres types de règles de placement qui sont principalement utiles dans les clusters qui sont répartis géographiquement ou dans d’autres scénarios moins courants. Celles-ci sont configurées via Corrélations ou Stratégies. Même si elles ne sont pas couramment utilisées, nous allons les décrire par souci d’exhaustivité.

## Étapes suivantes
- Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
- L’affinité est un mode que vous pouvez configurer pour vos services. Ce n’est pas courant, mais si nécessaire, vous trouverez plus d’informations [ici](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Il existe de nombreuses règles de positionnement différentes qui peuvent être configurées sur votre service pour gérer des scénarios supplémentaires. Vous trouverez plus d’informations sur ces différentes stratégies de positionnement [ici](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Commencer au début et [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
- Pour en savoir plus sur la façon dont Cluster Resource Manager gère et équilibre la charge du cluster, consultez l’article sur l’[équilibrage de la charge](service-fabric-cluster-resource-manager-balancing.md)
- Cluster Resource Manager comporte de nombreuses options permettant de décrire le cluster. Pour en savoir plus sur celles-ci, consultez cet article sur la [description d’un cluster Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)

<!---HONumber=AcomDC_0525_2016-->