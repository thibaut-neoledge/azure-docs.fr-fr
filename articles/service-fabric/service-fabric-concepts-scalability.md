<properties
   pageTitle="Extensibilité des services Service Fabric | Microsoft Azure"
   description="Décrit comment mettre à l'échelle les services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="aprameyr"/>

# Mise à l'échelle des applications Service Fabric
Azure Service Fabric facilite la création d'applications évolutives en équilibrant la charge des services, des partitions et des réplicas entre tous les nœuds d'un cluster. Cela permet d'optimiser l'utilisation des ressources.

Il est possible d'obtenir des applications Service Fabric à grande échelle de deux manières :

1. Mise à l'échelle au niveau de la partition

2. Mise à l'échelle au niveau du nom du service

## Mise à l'échelle au niveau de la partition
Service Fabric prend en charge le partitionnement d'un service individuel en plusieurs partitions plus petites. L'article [Vue d'ensemble du partitionnement](service-fabric-concepts-partitioning.md) fournit des informations sur les types de schémas de partitionnement pris en charge. Les réplicas de chaque partition sont répartis entre les nœuds d’un cluster. Imaginez un service qui utilise le schéma de partitionnement par plage avec une clé basse de 0, une clé haute de 99 et quatre partitions. Dans un cluster à trois nœuds, le service peut être disposé avec quatre réplicas partageant les ressources sur chaque nœud, comme illustré ici :

![Disposition de partition avec trois nœuds](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

L'augmentation du nombre de nœuds permet à Fabric Service d'exploiter les ressources sur les nouveaux nœuds en déplaçant certains réplicas vers des nœuds vides. Grâce à l'augmentation du nombre de nœuds à quatre, le service possède maintenant trois réplicas en cours d'exécution sur chaque nœud (de différentes partitions), favorisant ainsi une meilleure utilisation des ressources et de meilleures performances.

![Disposition de partition avec quatre nœuds](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## Mise à l'échelle au niveau du nom du service
Une instance de service est une instance spécifique d'un nom d'application et d'un nom de type de service (consultez [Cycle de vie d'une application Service Fabric](service-fabric-application-lifecycle.md)). Pendant la création d’un service, vous spécifiez le schéma de partition ([Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)) à utiliser.

Le premier niveau de mise à l'échelle s'effectue par nom de service. Vous pouvez créer de nouvelles instances d'un service, avec différents niveaux de partitionnement, à mesure que vos instances de service plus anciennes deviennent occupées. Cela permet aux nouveaux consommateurs de service d'utiliser des instances de service moins occupées plutôt que des instances plus occupées.

Une option pour augmenter la capacité, ou pour augmenter ou réduire le nombre de partitions, consiste à créer une nouvelle instance de service avec un nouveau schéma de partition. Mais cela augmente la complexité car tous les clients de consommation ont besoin de savoir quand et comment utiliser un service nommé différemment.

### Exemple de scénario : dates incorporées
Un scénario possible serait d'utiliser les informations de date comme composante du nom du service. Par exemple, vous pourriez utiliser une instance de service avec un nom spécifique pour tous les clients ralliés en 2013 et un autre nom pour les clients ralliés en 2014. Ce schéma d'affectation de noms permet une augmentation programmée des noms en fonction de la date (à l'approche de 2014, l'instance de service pour 2014 peut être créée sur demande).

Toutefois, cette approche est basée sur les clients utilisant des informations d'affectation de noms spécifiques à l'application qui se trouvent hors de l'étendue des connaissances de Service Fabric.

- *Utilisation d'une convention d'affectation de noms* : en 2013, quand votre application est mise en service, vous créez un service appelé fabric:/app/service2013. Vers le deuxième trimestre de l'année 2013, vous créez un autre service appelé fabric:/app/service2014. Ces deux services sont du même type de service. Dans cette approche, votre client devra faire preuve de logique pour construire le nom de service approprié en fonction de l'année.

- *Utilisation d'un service de recherche* : un autre modèle consiste à fournir un service de recherche secondaire qui peut fournir le nom du service pour une clé de votre choix. De nouvelles instances de service peuvent ensuite être créées par le service de recherche. Le service de recherche lui-même ne conserve pas les données d'application, mais uniquement les données sur les noms de service qu'il crée. Ainsi, dans l'exemple ci-dessus basé sur l'année, le client contacterait tout d'abord le service de recherche pour trouver le nom du service traitant les données pour une année donnée, puis utiliserait ce nom de service pour effectuer l'opération réelle. Le résultat de la première recherche peut être mis en cache.

## Étapes suivantes

Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Disponibilité des services Service Fabric](service-fabric-availability-services.md)

- [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

- [Définition et gestion de l'état](service-fabric-concepts-state.md)

<!---HONumber=AcomDC_0128_2016-->