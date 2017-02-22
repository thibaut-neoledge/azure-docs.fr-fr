---
title: "Extensibilité des services Service Fabric | Microsoft Docs"
description: "Décrit comment mettre à l&quot;échelle les services Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/30/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 52dcf341a34478bf4e800d8f1b9d44867b5feaf6


---
# <a name="scaling-service-fabric-applications"></a>Mise à l'échelle des applications Service Fabric
Azure Service Fabric facilite la création d’applications évolutives en gérant les services, les partitions et les réplicas sur tous les nœuds d’un cluster. Cela permet d'optimiser l'utilisation des ressources.

Il est possible d'obtenir des applications Service Fabric à grande échelle de deux manières :

1. Mise à l’échelle au niveau de la partition de service
2. Mise à l’échelle au niveau de l’instance de service nommé

## <a name="scaling-at-the-partition-level"></a>Mise à l'échelle au niveau de la partition
Service Fabric prend en charge le partitionnement. Le partitionnement permet à un service d’être fractionné en plusieurs partitions indépendantes, chacune avec une partie de l’état du service global. L'article [Vue d'ensemble du partitionnement](service-fabric-concepts-partitioning.md) fournit des informations sur les types de schémas de partitionnement pris en charge. Les réplicas de chaque partition sont répartis entre les nœuds d’un cluster. Imaginez un service qui utilise un schéma de partitionnement par plage avec une clé basse de 0, une clé haute de 99 et 4 partitions. Dans un cluster à trois nœuds, le service peut être disposé avec quatre réplicas partageant les ressources sur chaque nœud, comme illustré ici :

<center>
![Disposition de partition avec trois nœuds](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Si vous augmentez le nombre de nœuds, Service Fabric exploite les ressources sur les nouveaux nœuds en déplaçant certains réplicas vers ces nouveaux nœuds. Grâce à l’augmentation du nombre de nœuds à quatre, le service possède maintenant trois réplicas en cours d’exécution sur chaque nœud (chacun appartenant à différentes partitions), favorisant ainsi une meilleure utilisation des ressources et de meilleures performances.

<center>
![Disposition de partition avec quatre nœuds](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-at-the-service-name-level"></a>Mise à l'échelle au niveau du nom du service
Une instance de service est une instance spécifique d'un nom d'application et d'un nom de type de service (consultez [Cycle de vie d'une application Service Fabric](service-fabric-application-lifecycle.md)). Pendant la création d’un service, vous spécifiez le schéma de partition ( [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)) à utiliser.

Le premier niveau de mise à l'échelle s'effectue par nom de service. Vous pouvez créer des instances d’un service, éventuellement avec différents niveaux de partitionnement, à mesure que vos instances de service plus anciennes sont occupées. Cela permet aux nouveaux consommateurs de service d'utiliser des instances de service moins occupées plutôt que des instances plus occupées.

Une option pour augmenter la capacité consiste à créer une instance de service avec un nouveau schéma de partition. Mais cela augmente la complexité. Tous les clients de consommation doivent savoir quand et comment utiliser le service qui porte un autre nom. Une autre solution pour le service de gestion ou le service intermédiaire consiste à effectuer une détermination sur les services et les partitions qui doivent gérer chaque demande.

### <a name="example-scenario-embedded-dates"></a>Exemple de scénario : dates incorporées
Un scénario possible serait d'utiliser les informations de date comme composante du nom du service. Par exemple, vous pourriez utiliser une instance de service avec un nom spécifique pour tous les clients ralliés en 2013 et un autre nom pour les clients ralliés en 2014. Ce schéma d'affectation de noms permet une augmentation programmée des noms en fonction de la date (à l'approche de 2014, l'instance de service pour 2014 peut être créée sur demande).

Toutefois, cette approche est basée sur les clients utilisant des informations d'affectation de noms spécifiques à l'application qui se trouvent hors de l'étendue des connaissances de Service Fabric.

* *Utilisation d'une convention d'affectation de noms*: en 2013, quand votre application est mise en service, vous créez un service appelé fabric:/app/service2013. Vers le deuxième trimestre de l'année 2013, vous créez un autre service appelé fabric:/app/service2014. Ces deux services sont du même type de service. Dans cette approche, votre client devra faire preuve de logique pour construire le nom de service approprié en fonction de l'année.
* *Utilisation d'un service de recherche*: un autre modèle consiste à fournir un service de recherche secondaire qui peut fournir le nom du service pour une clé de votre choix. De nouvelles instances de service peuvent ensuite être créées par le service de recherche. Le service de recherche lui-même ne conserve pas les données d'application, mais uniquement les données sur les noms de service qu'il crée. Ainsi, dans l’exemple ci-dessus basé sur l’année, le client contacte tout d’abord le service de recherche pour trouver le nom du service traitant les données pour une année donnée. Ensuite, le client utilise ce nom de service pour effectuer l’opération proprement dite. Le résultat de la première recherche peut être mis en cache.

## <a name="putting-it-all-together"></a>Exemple complet
Reprenons toutes les idées que nous avons évoquées et envisageons un autre scénario.

Prenons l’exemple suivant : vous essayez de créer un service qui agit comme un carnet d’adresses, avec les noms et les coordonnées de personnes. Combien d’utilisateurs allez-vous avoir ? Combien de contacts chaque utilisateur va-t-il stocker ? Tenter de comprendre toutes ces questions lorsque vous mettez en place votre service pour la première fois est vraiment difficile. Si vous ne choisissez pas le bon nombre de partitions, vous pourriez rencontrer des problèmes de mise à l’échelle plus tard. Mais pourquoi essayer de choisir un seul schéma de partition pour tous les utilisateurs ?

Dans ce type de situation, envisagez plutôt le modèle suivant :
1. Plutôt que d’essayer de choisir un schéma de partitionnement global à l’avance, créez un « service gestionnaire ».
2. Le travail de ce service gestionnaire est d’examiner les informations relatives aux clients lorsqu’ils se connectent à votre service. En fonction de ces informations, il crée une instance de votre service de stockage de contacts _réel_, _juste pour ce client_. Ce type de création de service dynamique présente de nombreux avantages :

    * Vous n’avez pas besoin d’estimer à l’avance le nombre de partitions pour tous les utilisateurs.
    * Une certaine segmentation des données, dans la mesure où chaque client possède sa propre copie du service.
    * Chaque service client peut être configuré différemment, avec plus ou moins de partitions ou de réplicas, en fonction de l’échelle attendue.
      * Par exemple, supposons que le client paie le niveau « Gold ». Il peut bénéficier de plus de réplicas ou de plus de partitions.
      * Ou supposons qu’il fournisse des informations indiquant que le nombre de contacts nécessaires est « limité ». Ce client ne recevra alors que quelques partitions.
    * Vous n’exécutez pas un ensemble d’instances de service ou de réplicas en attendant que les clients veuillent bien se connecter.
    * Si un client quitte le service, la suppression des informations de votre service est aussi simple que de laisser le service gestionnaire supprimer le service qu’il a créé.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

* [Disponibilité des services Service Fabric](service-fabric-availability-services.md)
* [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)
* [Définition et gestion de l'état](service-fabric-concepts-state.md)



<!--HONumber=Jan17_HO1-->


