<properties
   pageTitle="Configuration de services avec Service Fabric Cluster Resource Manager"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Configuration des services
Service Fabric Cluster Resource Manager permet d’obtenir un contrôle détaillé des règles qui régissent chaque service nommé individuel. Chaque instance de service peut spécifier des règles pour son allocation dans le cluster. Chaque instance peut également définir l’ensemble de mesures qu’elle souhaite inclure dans un rapport, y compris leur importance pour ce service. En général, la configuration des services se décompose en trois tâches :

1. Configuration des contraintes de placement
2. Configuration des mesures
3. Configuration des règles de placement avancées (moins fréquent)

Examinons chacun d’elles :

## Contraintes de placement
Les contraintes de placement sont utilisées pour contrôler sur quels nœuds du cluster un service peut s’exécuter. En général, une instance de service nommé spécifique ou tous les services d’un type doivent s’exécuter sur un type particulier de nœud, mais les contraintes de placement sont extensibles : vous pouvez définir n’importe quel jeu de propriétés sur la base d’un type de nœud, puis les sélectionner avec les contraintes lors de la création du service. Les contraintes de placement peuvent également être mises à jour de manière dynamique durant la durée de vie du service, ce qui vous permet de répondre aux modifications apportées dans le cluster.

## Mesures
Les mesures sont la liste des ressources que ce service doit prendre en compte, notamment des informations sur la quantité de la ressource que chaque réplica ou instance de ce service utilise par défaut. Les mesures comprennent également une pondération qui indique l’importance de la mesure pour le service, en cas de compromis nécessaire.

## Autres règles de placement
Il existe d’autres types de règles de placement qui sont principalement utiles dans les clusters qui sont répartis géographiquement ou dans d’autres scénarios moins courants. Celles-ci sont configurées via Corrélations ou Stratégies. Même si elles ne sont pas couramment utilisées, nous allons les décrire par souci d’exhaustivité.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur les mesures](service-fabric-cluster-resource-manager-metrics.md)
- [En savoir plus sur l’Affinité de service](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- [En savoir plus sur les stratégies de positionnement du service](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- [Obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

<!------HONumber=AcomDC_0309_2016-->