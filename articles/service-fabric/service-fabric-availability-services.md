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
ms.date: 08/18/2017
ms.author: masnider
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3e46b4bdcf7b55c31afe5e7bc84a1fb95ad98701
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="availability-of-service-fabric-services"></a>Disponibilité des services Service Fabric
Cet article offre une vue d'ensemble de la manière dont Service Fabric maintient la disponibilité.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilité des services sans état de Service Fabric
Les services Azure Service Fabric peuvent être avec ou sans état. Un service sans état est un service d'application qui ne possède aucun [état persistant local](service-fabric-concepts-state.md) devant être hautement disponible ou fiable.

La création d’un service sans état nécessite la définition d’un `InstanceCount`. Le nombre d’instances définit le nombre d’instances de la logique d’application du service sans état qui doivent être exécutées dans le cluster. L'augmentation du nombre d'instances est la méthode recommandée pour la mise à l'échelle d’un service sans état.

Lorsqu’une instance ou un service nommé sans état échoue, une nouvelle instance est créée sur un nœud éligible du cluster. Par exemple, une instance de service sans état peut échouer sur Node1 et être recréée sur Node5.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilité des services avec état de Service Fabric
Un service avec état possède un état qui lui est associé. Dans Service Fabric, un service avec état est modelé comme un jeu de réplicas. Chaque réplica est une instance du code du service en cours d’exécution qui possède une copie de l'état pour ce service. Les opérations de lecture et d’écriture sont effectuées sur un seul réplica (appelé réplica principal). Les modifications de l’état en raison des opérations d’écriture sont *répliquées* sur les autres réplicas du jeu de réplicas (appelés réplicas secondaires actifs) puis appliquées. 

Il ne peut y avoir qu’un seul réplica principal, mais il peut y avoir plusieurs réplicas secondaires actifs. Le nombre de réplicas secondaires actifs est configurable et un nombre plus élevé de réplicas peut tolérer un plus grand nombre de défaillances logicielles et matérielles simultanées.

En cas d’erreur (quand le réplica principal tombe en panne), Service Fabric choisit l’un des réplicas secondaires actifs comme nouveau réplica principal. Ce réplica secondaire actif possède déjà la version à jour de l’état (via la *réplication*) et peut continuer le traitement des opérations de lecture et d’écriture. Ce processus est appelé une reconfiguration et est décrit de façon détaillée dans [Reconfiguration](service-fabric-concepts-reconfiguration.md).

Ce concept de réplica, qu’il soit principal ou secondaire actif, correspond au rôle de réplica. Il est développé de façon approfondie dans [Réplicas et instances](service-fabric-concepts-replica-lifecycle.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Mise à l’échelle des applications Service Fabric](service-fabric-concepts-scalability.md)
- [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)
- [Définition et gestion de l'état](service-fabric-concepts-state.md)
- [Services fiables (Reliable Services)](service-fabric-reliable-services-introduction.md)

