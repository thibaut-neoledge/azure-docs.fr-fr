<properties
   pageTitle="Disponibilité des services Service Fabric"
	description="Décrit la détection des erreurs, le basculement et la récupération pour des services"
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
	ms.date="08/26/2015"
	ms.author="aprameyr"/>

# Disponibilité des services Service Fabric
Les services Service Fabric peuvent être avec ou sans état. Cet article offre une vue d'ensemble de la manière dont Service Fabric maintient la disponibilité d'un service en cas de défaillance.

## Disponibilité des services sans état de Service Fabric
Un service sans état est un service d'application qui ne possède aucun [état persistant local](service-fabric-concepts-state.md).

La création d'un service sans état nécessite la définition d'un nombre d'instances correspondant au nombre d'instances du service sans état qui doivent être en cours d'exécution dans le cluster. Il s'agit du nombre de copies de la logique d'application qui seront instanciées dans le cluster. L'augmentation du nombre d'instances est la méthode recommandée pour la mise à l'échelle des services sans état.

Quand une erreur est détectée sur une instance quelconque du service sans état, une nouvelle instance est créée sur un autre nœud éligible dans le cluster.

## Disponibilité des services avec état de Service Fabric
Un service avec état possède un état qui lui est associé. Dans Service Fabric, un service avec état est modelé comme un jeu de réplicas. Chaque réplica est une instance du code du service qui possède une copie de l'état. Les opérations de lecture et d'écriture sont effectuées sur un seul réplica (appelé réplica principal). Les modifications de l'état dues aux opérations d'écriture sont *répliquées* sur plusieurs autres réplicas (appelés réplicas secondaires actifs). Cette combinaison du réplica principal et des réplicas secondaires actifs constitue le jeu de réplicas du service.

Il ne peut y avoir qu'un seul réplica principal traitant les demandes de lecture et d'écriture. Il peut y avoir plusieurs réplicas secondaires actifs. Le nombre de réplicas secondaires actifs est configurable et un nombre plus élevé de réplicas permet de tolérer un plus grand nombre de défaillances logicielles et matérielles simultanées.

En cas d'erreur (quand le réplica principal tombe en panne), Service Fabric choisit l'un des réplicas secondaires actifs comme nouveau réplica principal. Ce réplica secondaire actif possède déjà la version à jour de l'état (via la *réplication*) et peut continuer le traitement des opérations de lecture et d'écriture.

Ce concept de réplica principal ou secondaire actif correspond au rôle de réplica.

### Rôles de réplica
Le rôle d'un réplica est utilisé pour gérer le cycle de vie de l'état géré par ce réplica. Un réplica dont le rôle est principal traite les demandes de lecture. Il traite également les demandes d'écriture en mettant à jour son état et en répliquant les modifications sur les réplicas secondaires actifs dans son jeu de réplicas. Un réplica secondaire actif est chargé de recevoir les modifications de l'état que le réplica principal a répliquées et de mettre à jour sa vue de l'état.

>[AZURE.NOTE]Les modèles de programmation de niveau supérieur tels que l'[infrastructure Acteurs fiables](service-fabric-reliable-actors-introduction.md) clarifient le concept de rôle de réplica par rapport au développeur.

## Étapes suivantes

Pour plus d'informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)

- [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

- [Définition et gestion de l'état](service-fabric-concepts-state.md)
 

<!---HONumber=August15_HO9-->