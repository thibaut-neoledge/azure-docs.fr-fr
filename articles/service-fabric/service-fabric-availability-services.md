---
title: "Disponibilité des services Service Fabric | Microsoft Docs"
description: "Décrit la détection des erreurs, le basculement et la récupération pour les services"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 1db7b4bcfa4b7c474a4b0eb4ef469a6cb1fe54a0


---
# <a name="availability-of-service-fabric-services"></a>Disponibilité des services Service Fabric
Cet article offre une vue d'ensemble de la manière dont Service Fabric maintient la disponibilité.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilité des services sans état de Service Fabric
Les services Azure Service Fabric peuvent être avec ou sans état. Un service sans état est un service d'application qui ne possède aucun [état persistant local](service-fabric-concepts-state.md).

La création d’un service sans état nécessite la définition d’un nombre d’instances. Le nombre d’instances définit le nombre d’instances de la logique d’application du service sans état qui doivent être exécutées dans le cluster. L'augmentation du nombre d'instances est la méthode recommandée pour la mise à l'échelle d’un service sans état.

Lorsqu’une erreur est détectée sur une instance d’un service sans état, une nouvelle instance est créée sur un nœud éligible du cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilité des services avec état de Service Fabric
Un service avec état possède un état qui lui est associé. Dans Service Fabric, un service avec état est modelé comme un jeu de réplicas. Chaque réplica est une instance du code du service qui possède une copie de l'état. Les opérations de lecture et d’écriture sont effectuées sur un seul réplica (appelé réplica principal). Les modifications de l’état en raison des opérations d’écriture sont *répliquées* sur plusieurs autres réplicas (appelés réplicas secondaires actifs). Cette association du réplica principal et des réplicas secondaires actifs constitue le jeu de réplicas du service.

Il ne peut y avoir qu’un seul réplica principal pour les demandes d’écriture et de lecture, mais il peut y avoir plusieurs réplicas secondaires actifs. Le nombre de réplicas secondaires actifs est configurable et un nombre plus élevé de réplicas peut tolérer un plus grand nombre de défaillances logicielles et matérielles simultanées.

En cas d’erreur (quand le réplica principal tombe en panne), Service Fabric choisit l’un des réplicas secondaires actifs comme nouveau réplica principal. Ce réplica secondaire actif possède déjà la version à jour de l’état (via la *réplication*) et peut continuer le traitement des opérations de lecture et d’écriture.

Ce concept de réplica, qu’il soit principal ou secondaire actif, correspond au rôle de réplica.

### <a name="replica-roles"></a>Rôles de réplica
Le rôle d’un réplica est utilisé pour gérer le cycle de vie de l’état géré par ce réplica. Un réplica dont le rôle est principal traite les demandes de lecture. Le réplica principal gère également toutes les demandes d’écriture en mettant à jour son état et en répliquant les modifications. Ces modifications sont appliquées à des réplicas secondaires actifs dans le jeu de réplicas. Le travail d’un réplica secondaire actif consiste à recevoir les modifications de l’état que le réplica principal a répliquées et à mettre à jour sa vue de l’état.

> [!NOTE]
> Les modèles de programmation de niveau supérieur tels que l’ [infrastructure Reliable Actors](service-fabric-reliable-actors-introduction.md) et [Reliable Services](service-fabric-reliable-services-introduction.md) clarifient le concept de rôle de réplica par rapport au développeur. Dans Actors, la notion de rôle est inutile, tandis que dans Services, elle est visible au besoin, mais largement simplifiée.
>
>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

* [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)
* [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)
* [Définition et gestion de l'état](service-fabric-concepts-state.md)
* [Services fiables (Reliable Services)](service-fabric-reliable-services-introduction.md)



<!--HONumber=Jan17_HO1-->


