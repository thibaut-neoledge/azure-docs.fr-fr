---
title: "Disponibilité des services Service Fabric | Microsoft Docs"
description: "Décrit la détection des erreurs, le basculement et la récupération pour les services"
services: service-fabric
documentationcenter: .net
author: appi101
manager: timlt
editor: 
ms.assetid: 279ba4a4-f2ef-4e4e-b164-daefd10582e4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/10/2016
ms.author: aprameyr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: af0880503cc720d86b81a6ec3d74197afe8de08d


---
# <a name="availability-of-service-fabric-services"></a>Disponibilité des services Service Fabric
Les services Azure Service Fabric peuvent être avec ou sans état. Cet article offre une vue d'ensemble de la manière dont Service Fabric maintient la disponibilité d'un service en cas de défaillance.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilité des services sans état de Service Fabric
Un service sans état est un service d'application qui ne possède aucun [état persistant local](service-fabric-concepts-state.md).

La création d’un service sans état nécessite la définition d’un nombre d’instances correspondant au nombre d’instances du service sans état qui doivent être en cours d’exécution dans le cluster. Il s’agit du nombre de copies de la logique d’application qui seront instanciées dans le cluster. L’augmentation du nombre d’instances est la méthode recommandée pour la montée en puissance d’un service sans état.

Quand une erreur est détectée sur une instance quelconque d’un service sans état, une nouvelle instance est créée sur un autre nœud éligible dans le cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilité des services avec état de Service Fabric
Un service avec état possède un état qui lui est associé. Dans Service Fabric, un service avec état est modelé comme un jeu de réplicas. Chaque réplica est une instance du code du service qui possède une copie de l'état. Les opérations de lecture et d’écriture sont effectuées sur un seul réplica (appelé réplica principal). Les modifications de l’état en raison des opérations d’écriture sont *répliquées* sur plusieurs autres réplicas (appelés réplicas secondaires actifs). Cette association du réplica principal et des réplicas secondaires actifs constitue le jeu de réplicas du service.

Il ne peut y avoir qu’un seul réplica principal pour les demandes d’écriture et de lecture, mais il peut y avoir plusieurs réplicas secondaires actifs. Le nombre de réplicas secondaires actifs est configurable et un nombre plus élevé de réplicas peut tolérer un plus grand nombre de défaillances logicielles et matérielles simultanées.

En cas d’erreur (quand le réplica principal tombe en panne), Service Fabric choisit l’un des réplicas secondaires actifs comme nouveau réplica principal. Ce réplica secondaire actif possède déjà la version à jour de l’état (via la *réplication*) et peut continuer le traitement des opérations de lecture et d’écriture.

Ce concept de réplica, qu’il soit principal ou secondaire actif, correspond au rôle de réplica.

### <a name="replica-roles"></a>Rôles de réplica
Le rôle d’un réplica est utilisé pour gérer le cycle de vie de l’état géré par ce réplica. Un réplica dont le rôle est principal traite les demandes de lecture. Il traite également les demandes d’écriture en mettant à jour son état et en répliquant les modifications sur les réplicas secondaires actifs dans son jeu de réplicas. Le rôle d’un réplica secondaire actif est de recevoir les modifications de l’état que le réplica principal a répliquées et de mettre à jour sa vue de l’état.

> [!NOTE]
> Les modèles de programmation de niveau supérieur tels que l’ [infrastructure d’acteurs fiables](service-fabric-reliable-actors-introduction.md) clarifient le concept de rôle de réplica par rapport au développeur.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

* [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)
* [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)
* [Définition et gestion de l'état](service-fabric-concepts-state.md)




<!--HONumber=Nov16_HO3-->


