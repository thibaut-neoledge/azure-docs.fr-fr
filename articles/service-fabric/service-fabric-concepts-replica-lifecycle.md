---
title: "Réplicas et instances dans Azure Service Fabric | Microsoft Docs"
description: "Comprendre les réplicas et les instances, leur fonction et leur cycle de vie"
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: aprameyr
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 794cbed0f5072bcc1c18343b9896aad464861c45
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="replicas-and-instances"></a>Réplicas et instances 
Cet article donne une vue d’ensemble du cycle de vie des réplicas de services avec état et des instances de services sans état.

## <a name="instances-of-stateless-services"></a>Instances de services sans état
Une instance d’un service sans état est une copie de la logique de service en cours d’exécution sur l’un des nœuds du cluster. Une instance au sein d’une partition n’est identifiable que par le biais de son ID d’instance. Le cycle de vie d’une instance peut être représenté par le diagramme suivant :

![Cycle de vie de l’instance](./media/service-fabric-concepts-replica-lifecycle/instance.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Dès que Cluster Resource Manager a déterminé le placement d’une instance, celle-ci entre dans cette phase du cycle de vie. À ce stade, l’instance est démarrée sur le nœud (démarrage de l’hôte d’application, création puis ouverture de l’instance). À l’issue du démarrage, l’état de l’instance passe à prêt. 

Si le nœud ou l’hôte d’application de cette instance tombe en panne, son état passe à abandonné.

### <a name="ready-rd"></a>Ready (RD)
Dans l’état prêt, l’instance est opérationnelle sur le nœud. S’il s’agit d’un service fiable, RunAsync a été appelée. 

Si le nœud ou l’hôte d’application de cette instance tombe en panne, son état passe à abandonné.

### <a name="closing-cl"></a>Closing (CL)
Dans l’état de fermeture, Service Fabric procède à l’arrêt de l’instance sur ce nœud. De nombreuses raisons peuvent en être la cause, par exemple un équilibrage de charge, une mise à niveau d’application, ou la suppression du service. Une fois l’arrêt accompli, son état passe à abandonné.

### <a name="dropped-dd"></a>Dropped (DD)
Dans l’état abandonné, l’instance n’est plus exécutée sur le nœud. À ce stade, l’infrastructure de service conserve les métadonnées relatives à cette instance (dont la suppression est prévue tardivement).

> [!NOTE]
> Il est possible de passer de n’importe quel état à l’état abandonné par l’intermédiaire de l’option ForceRemove sur `Remove-ServiceFabricReplica`.
>

## <a name="replicas-of-stateful-services"></a>Réplicas de services avec état
Un réplica d’un service avec état est une copie de la logique de service en cours d’exécution sur l’un des nœuds du cluster. De plus, le réplica conserve une copie de l’état de ce service. Deux concepts connexes décrivent le cycle de vie et le comportement des réplicas avec état :
- Cycle de vie du réplica
- Rôle du réplica

Les paragraphes suivants décrivent les services avec état persistant. Pour les services avec état volatile (ou en mémoire), les états hors service et abandonné sont équivalents.

![Cycle de vie du réplica](./media/service-fabric-concepts-replica-lifecycle/replica.png)

### <a name="inbuild-ib"></a>InBuild (IB)
Un réplica InBuild est un réplica qui est créé ou préparé en vue de rejoindre le jeu de réplicas. Selon le rôle du réplica, la phase InBuild adopte une sémantique différente. 

Si le nœud ou l’hôte d’application d’un réplica InBuild tombe en panne, son état passe à hors service.

####<a name="primary-inbuild-replicas"></a>Réplicas Primary InBuild 
Les réplicas Primary InBuild sont les premiers réplicas d’une partition. Ceci se produit généralement lors de la création de la partition. Les réplicas Principal InBuild apparaissent également lorsque tous les réplicas d’une partition redémarrent ou sont abandonnés.

####<a name="idlesecondary-inbuild-replicas"></a>Réplicas IdleSecondary InBuild
Il peut s’agir de nouveaux réplicas que Cluster Resource Manager est en train de créer. Ce peut être également des réplicas existants qui sont tombés en panne et doivent réintégrer le jeu de réplicas. Ces réplicas sont amorcés ou générés par le réplica principal avant de pouvoir rejoindre le jeu de réplicas en tant que réplicas ActiveSecondary et ainsi participer à l’accusé de réception quorum d’opérations.

####<a name="activesecondary-inbuild-replicas"></a>Réplicas ActiveSecondary InBuild
Cet état peut être observé dans certaines requêtes. Il s’agit d’une optimisation où le jeu de réplicas ne change pas, mais où la création d’un réplica est nécessaire. Le réplica lui-même suit les transitions de l’état normal de la machine (comme décrit dans la section sur les rôles du réplica).

### <a name="ready-rd"></a>Ready (RD)
Un réplica Ready est un réplica qui participe à la réplication et à l’accusé de réception quorum d’opérations. L’état prêt s’applique aux réplicas principaux et secondaires actifs.

Si le nœud ou l’hôte d’application d’un réplica prêt tombe en panne, son état passe à hors service.

### <a name="closing-cl"></a>Closing (CL)
Un réplica passe à l’état de fermeture dans les scénarios suivants :

- **Arrêt du code pour le réplica** : Service Fabric peut être amené à mettre fin à l’exécution du code pour un réplica. Il peut y avoir de nombreuses raisons à cela, par exemple une mise à niveau d’application, de structure, d’infrastructure, ou il peut s’agir d’une erreur signalée par le réplica, etc. Lorsque la fermeture du réplica est terminée, il passe à l’état hors service. L’état persistant associé à ce réplica stocké sur le disque n’est pas nettoyé.

- **Suppression du réplica à partir du cluster** : Service Fabric peut être amené à supprimer l’état persistant et à arrêter l’exécution du code d’un réplica. Il peut y avoir de nombreuses raisons à cela, par exemple l’équilibrage de charge.

### <a name="dropped-dd"></a>Dropped (DD)
Dans l’état abandonné, l’instance n’est plus exécutée sur le nœud. Il n’existe également plus d’état sur le nœud. À ce stade, Service Fabric conserve les métadonnées relatives à cette instance (qui sont au final également supprimées).

### <a name="down-d"></a>Down (D)
Dans l’état hors service, le code du réplica ne s’exécute pas, mais l’état persistant de ce réplica existe sur ce nœud. Un réplica peut être hors service pour de nombreuses raisons, par exemple une panne de nœud, des incidents survenant dans le code du réplica, une mise à niveau d’application, des défaillances de réplica, etc.

Le cas échéant, un réplica hors service est ouvert par Service Fabric, par exemple lorsque la mise à niveau est terminée sur le nœud, etc.

Le rôle de réplica ne s’applique pas à l’état hors service.

### <a name="opening-op"></a>Opening (OP)
Un réplica hors service passe à l’état d’ouverture quand Service Fabric doit le rendre de nouveau opérationnel. Par exemple, après une mise à niveau de code, pour que l’application se termine sur un nœud, etc. 

Si le nœud ou l’hôte d’application pour un réplica qui s’ouvre se bloque, son état passe à hors service.

Le rôle de réplica ne s’applique pas dans l’état d’ouverture.

### <a name="standby-sb"></a>StandBy (SB)
Un réplica Standby est un réplica d’un service persistant qui s’est arrêté et a ensuite été ouvert. Ce réplica peut être utile à Service Fabric si l’ajout d’un autre réplica au jeu de réplicas est nécessaire (car il possède déjà une partie de l’état et le processus de génération est plus rapide). Après l’expiration de StandByReplicaKeepDuration, le réplica en attente est ignoré.

Si le nœud ou l’hôte d’application pour un réplica en attente tombe en panne, son état passe à hors service.

Le rôle de réplica ne s’applique pas dans l’état en attente.

> [!NOTE]
> Tout réplica qui n’est pas hors service ou abandonné est considéré *opérationnel*.
>

> [!NOTE]
> Il est possible de passer de n’importe quel état à l’état abandonné par l’intermédiaire de l’option ForceRemove sur `Remove-ServiceFabricReplica`.
>

## <a name="replica-role"></a>Rôle du réplica 
Le rôle du réplica détermine sa fonction dans le jeu de réplicas.

- **Principal (P)** : il existe un principal dans le jeu de réplicas qui est chargé d’effectuer les opérations de lecture et d’écriture. 
- **ActiveSecondary (S)** : ce sont les réplicas qui reçoivent des mises à jour d’état du réplica principal, les appliquent et retournent des accusés de réception. Il existe plusieurs secondaires actifs dans le jeu de réplicas, et leur nombre détermine le nombre de défaillances que le service peut supporter.
- **IdleSecondary (I)** : ces réplicas sont en fait générés par le réplica principal, ils reçoivent un état de ce réplica avant de pouvoir être promus réplicas secondaires actifs. 
- **None (N)** : ces réplicas n’ont pas de responsabilité dans le jeu de réplicas.
- **Unknown (U)** : c’est le rôle initial d’un réplica, avant de recevoir un appel de l’API ChangeRole depuis Service Fabric.

La section suivante décrit les transitions du rôle de réplica et donne quelques exemples de scénarios dans lesquels celles-ci peuvent se produire :

![Rôle du réplica](./media/service-fabric-concepts-replica-lifecycle/role.png)

- U -> P : Création d’un réplica principal.
- U -> I : Création d’un réplica inactif.
- U -> N : Suppression d’un réplica en attente.
- I -> S : Promotion d’un réplica secondaire inactif au rôle de secondaire actif, afin que ses accusés de réception contribuent au quorum.
- I -> P : Promotion du rôle d’inactif secondaire à celui de principal. Ce cas de figure peut se produire sous certaines reconfigurations spéciales, lorsque le réplica secondaire inactif est le candidat approprié pour devenir réplica principal.
- I -> N : Suppression d’un réplica secondaire inactif.
- S -> P : Promotion du réplica secondaire actif au rôle de réplica principal. Le basculement du principal peut en être la cause, ou un déplacement du principal initié par Cluster Resource Manager en réponse à un équilibrage de charge ou à une mise à niveau d’application, etc.
- S -> N : Suppression d’un réplica secondaire actif.
- P -> S : Rétrogradation du réplica principal. Cela peut être dû à un déplacement du principal initié par Cluster Resource Manager en réponse à un équilibrage de charge ou à une mise à niveau d’application, etc.
- P -> N : Suppression du réplica principal.

> [!NOTE]
> Les modèles de programmation de niveau supérieur tels que [Reliable Actors](service-fabric-reliable-actors-introduction.md) et [Reliable Services](service-fabric-reliable-services-introduction.md) empêchent le développeur de voir le concept de rôle de réplica. Dans Actors, la notion de rôle est inutile, tandis que dans Services, elle est largement simplifiée dans la plupart des scénarios.
>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Cycle de vie des Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)

