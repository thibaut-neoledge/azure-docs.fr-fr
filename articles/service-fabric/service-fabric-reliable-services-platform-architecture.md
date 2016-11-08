---
title: Architecture de service fiable | Microsoft Docs
description: Présentation de l’architecture Reliable Service pour les services avec et sans état
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: vturecek

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/30/2016
ms.author: alanwar

---
# Architecture de services Reliable Services avec et sans état
Un service Reliable Service Azure Service Fabric peut être avec état ou sans état. Chaque type de service s’exécute au sein d’une architecture spécifique. Ces architectures sont décrites dans cet article. Consultez [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md) pour plus d’informations sur les différences entre les services avec et sans état.

## Services fiables avec état
### Architecture d'un service avec état
![Graphique d’architecture d’un service avec état](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Service fiable avec état
Un service Reliable Service avec état peut dériver de la classe StatefulServiceBase ou de la classe StatefulService. Les deux classes de base sont fournies par Service Fabric. Elles offrent différents niveaux de prise en charge et d’abstraction pour que le service avec état puisse échanger avec Service Fabric, et pour qu’il participe en tant que service au sein du cluster Service Fabric.

StatefulService provient de StatefulServiceBase. StatefulServiceBase propose aux services plus de souplesse, mais nécessite une meilleure compréhension des éléments internes de Service Fabric. Consultez [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md) et [Utilisation avancée du service fiable](service-fabric-reliable-services-advanced-usage.md) pour plus d’informations sur les spécificités de l’écriture de services à l’aide des classes StatefulService et StatefulServiceBase.

Les deux classes de base gèrent la durée de vie et le rôle de la mise en œuvre du service. La mise en œuvre du service peut remplacer les méthodes virtuelles de l’une des classes de base si la mise en œuvre du service a du travail à faire sur ces points dans le cycle de vie de la mise en œuvre du service ou si elle souhaite créer un objet écouteur de communication. Notez que bien qu’une mise en œuvre de service peut implémenter son propre objet écouteur de communication exposant ICommunicationListener, dans le diagramme ci-dessus, l’écouteur de communication est mis en œuvre par Service Fabric car la mise en œuvre du service utilise un écouteur de communication implémenté par Service Fabric.

Un service Reliable Service avec état utilise le Gestionnaire d’état fiable pour tirer parti des collections fiables. Les collections fiables sont des structures de données locales qui sont hautement disponibles pour le service, c’est-à-dire qu’elles sont toujours disponibles, en dépit des basculements du service. Chaque type de collection fiable est implémenté par un fournisseur d'état fiable. Pour plus d’informations sur les collections fiables, consultez [la présentation des collections fiables](service-fabric-reliable-services-reliable-collections.md)

### Fournisseurs et gestionnaire d'état fiable
Le gestionnaire d’état fiable est l’objet qui gère les fournisseurs d’état fiable. Il a pour fonction de créer, supprimer, énumérer et veiller à ce que les fournisseurs d’état fiable perdurent et soient hautement disponibles. Une instance de fournisseur d’état fiable représente une instance d’une structure de données persistante et hautement disponible, comme un dictionnaire ou une file d’attente.

Chaque fournisseur d’état fiable expose une interface qui est utilisée par un service avec état pour interagir avec le fournisseur d’état fiable. Par exemple, IReliableDictionary sert d’interface avec le dictionnaire fiable alors que IReliableQueue sert d’interface avec la file d’attente fiable. Tous les fournisseurs d'état fiable implémentent l'interface IReliableState.

Le Gestionnaire d’état fiable a une interface nommée IReliableStateManager qui y autorise l’accès à partir d’un service avec état. Les interfaces aux fournisseurs d'état fiable sont renvoyées par le biais de IReliableStateManager.

Le Gestionnaire d'état fiable utilise une architecture de plug-in dynamique, afin que les nouveaux types de collections fiables puissent être raccordés de façon dynamique.

Le dictionnaire fiable et la file d’attente fiable sont basés sur l’implémentation d’un magasin différentiel avec version hautes performances.

### Réplicateur transactionnel
Le composant de réplicateur transactionnel est chargé de s’assurer que l’état du service, c’est-à-dire l’état dans le Gestionnaire d’état fiable et les collections fiables, est cohérent dans tous les réplicas exécutant le service. Elle garantit également que l’état est conservé dans le journal. Le Gestionnaire d'état fiable interagit avec le réplicateur transactionnel au moyen d'un mécanisme privé.

Le réplicateur transactionnel utilise un protocole réseau pour communiquer l’état avec d’autres réplicas de l’instance de service afin que tous les réplicas disposent d’informations à jour sur l’état.

Le réplicateur transactionnel utilise un journal pour conserver les informations d'état afin que celles-ci survivent en cas de panne du processus ou du nœud. L'interface avec le journal est assurée au moyen d'un mécanisme privé.

### Journal
Le composant de journal fournit un magasin persistant hautes performances qui peut être optimisé pour l’écriture sur des disques en rotation ou d’état solide. La conception du journal est destinée au stockage persistant (c’est-à-dire les disques durs) en local pour les nœuds qui exécutent le service avec état. Cela permet d’obtenir une faible latence et un débit élevé, contrairement à un stockage persistant distant qui n’est pas local pour le nœud.

Le composant de journal utilise plusieurs fichiers journaux. Il existe un fichier journal partagé au niveau du nœud que tous les réplicas utilisent car il peut fournir la plus faible latence et le débit le plus élevé pour le stockage des données d'état. Par défaut, le journal partagé est placé dans le répertoire de travail du nœud Service Fabric, mais il peut également être configuré pour être placé dans un autre emplacement, idéalement sur un disque réservé exclusivement au journal partagé. Chaque réplica pour le service a également un fichier journal dédié, et ce fichier journal dédié est placé dans le répertoire de travail du service. Il n'existe aucun mécanisme pour configurer le placement du fichier journal dédié dans un autre emplacement.

Le journal partagé est une zone de transition pour les informations d’état du réplica alors que le fichier journal dédié constitue la destination finale où elles sont conservées. Dans cette conception, les informations d’état sont d’abord écrites dans le fichier journal partagé, puis elles sont tardivement déplacées dans le fichier journal dédié en arrière-plan. De cette façon, l’écriture dans le journal partagé a la latence la plus faible et le débit le plus élevé pour permettre au service de progresser plus rapidement.

Les lectures et écritures dans le journal partagé sont effectuées via des opérations E/S directes vers l’espace préalloué sur le disque pour le fichier journal partagé. Pour permettre une utilisation optimale de l'espace disque sur le lecteur de journaux dédiés, le fichier journal dédié est créé en tant que fichier fragmenté NTFS. Notez que cela permettra de surapprovisionner l'espace disque, et le système d'exploitation affichera les fichiers journaux dédiés en utilisant beaucoup plus d'espace disque qu’en réalité.

Outre une interface en mode utilisateur minime avec le journal, le journal est écrit comme un pilote de mode noyau. En s’exécutant comme un pilote de mode noyau, le journal peut fournir les meilleures performances pour tous les services qui l’utilisent.

Pour plus d’informations sur la configuration du journal, consultez [Configuration de Reliable Services avec état](service-fabric-reliable-services-configuration.md).

## Service fiable sans état
### Architecture d'un service sans état
![Graphique d’architecture d’un service sans état](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Service fiable sans état
Les implémentations de service sans état proviennent de la classe StatelessService ou StatelessServiceBase. La classe StatelessServiceBase offre davantage de flexibilité que la classe StatelessService. Les deux classes de base gèrent la durée de vie et le rôle du service.

La mise en œuvre du service peut remplacer les méthodes virtuelles de l’une des classes de base si le service a du travail à faire sur ces points dans le cycle de vie du service ou s’il souhaite créer un objet écouteur de communication. Notez que bien que le service peut implémenter son propre objet écouteur de communication exposant ICommunicationListener, dans le diagramme ci-dessus, l’écouteur de communication est mis en œuvre par Service Fabric, car la mise en œuvre du service utilise un écouteur de communication implémenté par Service Fabric.

Consultez [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md) et [Utilisation avancée du service fiable](service-fabric-reliable-services-advanced-usage.md) pour plus d’informations sur les spécificités de l’écriture de services à l’aide des classes StatelessService et StatelessServiceBase.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
Pour plus d'informations sur Service Fabric, consultez :

[Présentation du service fiable](service-fabric-reliable-services-introduction.md)

[Démarrage rapide](service-fabric-reliable-services-quick-start.md)

[Présentation des collections fiables](service-fabric-reliable-services-reliable-collections.md)

[Utilisation avancée du service fiable](service-fabric-reliable-services-advanced-usage.md)

[Configuration du service fiable](service-fabric-reliable-services-configuration.md)

<!---HONumber=AcomDC_0406_2016-->