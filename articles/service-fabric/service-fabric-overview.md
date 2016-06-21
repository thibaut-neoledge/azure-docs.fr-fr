<properties
   pageTitle="Vue d’ensemble de Service Fabric | Microsoft Azure"
   description="Présentation de Service Fabric, où les applications sont composées de nombreux microservices pour fournir une mise à l’échelle et une résilience. Service Fabric est une plateforme de systèmes distribués qui permet de créer des applications évolutives, fiables et faciles à gérer pour le cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/24/2016"
   ms.author="mfussell"/>

# Vue d'ensemble de Service Fabric
Service Fabric est une plateforme de systèmes distribués qui facilite l’empaquetage, le déploiement et la gestion de microservices fiables et évolutifs et résout les problèmes importants liés au développement et à la gestion d’applications cloud. En utilisant Service Fabric, les développeurs et administrateurs n’ont plus à résoudre les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques exigeantes, sachant qu’elles sont évolutives, fiables et faciles à gérer. Service Fabric représente la plateforme middleware de nouvelle génération pour la création et la gestion de ces applications d’entreprise de niveau 1 à l’échelle du cloud.

## Applications composées de microservices
Service Fabric vous permet de créer et gérer des applications évolutives et fiables, composées de microservices s'exécutant à très haute densité sur un pool partagé d'ordinateurs (appelé cluster Service Fabric). Il fournit un runtime sophistiqué pour générer des microservices avec et sans état distribués et évolutifs. Il fournit également des fonctionnalités complètes de gestion d’application pour la configuration, le déploiement, l’analyse, la mise à niveau/mise à jour corrective et la suppression d’applications déployées.

Pourquoi est-il important d’adopter une approche microservices ? Voici les deux principales raisons :

1. Ils permettent de mettre à l'échelle différentes parties de votre application en fonction de ses besoins

2. Les équipes de développement sont plus agiles dans le déploiement de modifications et fournissent ainsi plus vite et plus souvent des fonctionnalités à vos clients.

Service Fabric alimente de nombreux services Microsoft aujourd’hui, notamment la base de données SQL Azure, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT, Skype Entreprise et de nombreux services Azure principaux, pour n’en citer que quelques-uns.

Service Fabric est adapté à la création de services « nés dans le cloud » qui peuvent commencer petit, si nécessaire, et évoluer à grande échelle avec des centaines ou des milliers d'ordinateurs.

Actuellement, les services Internet sont créés à l’aide de microservices. Les microservices sont, par exemple, les passerelles de protocole, les profils utilisateur, les paniers d’achat, le traitement des stocks, les files d’attente et les caches. Service Fabric est une plateforme de microservices qui attribue à chaque microservice un nom unique pouvant être avec ou sans état.

Service Fabric fournit des fonctionnalités complètes de gestion du runtime et du cycle de vie pour les applications constituées de ces microservices. Il héberge les microservices dans des conteneurs déployés et activés sur le cluster Service Fabric. De la même façon qu’il est possible d’augmenter la densité d’un ordre de grandeur en passant des machines virtuelles aux conteneurs, un ordre de grandeur de même densité est désormais possible en passant des conteneurs aux microservices. Par exemple, un seul cluster Azure SQL Database, qui repose sur Service Fabric, comprend des centaines d’ordinateurs exécutant des dizaines de milliers de conteneurs qui hébergent un total de centaines de milliers de bases de données. (Chaque base de données est un microservice Service Fabric avec état). Il en est de même avec Event Hubs ou les autres services mentionnés ci-dessus. C’est pourquoi le terme « hyper-extensibilité » peut être utilisé pour décrire les fonctionnalités de Service Fabric. Si les conteneurs vous fournissent une haute densité, les microservices vous fournissent une hyper-extensibilité.

Pour plus d'informations sur l’approche microservices, consultez [Pourquoi adopter une approche microservices de la création d'applications ?](service-fabric-overview-microservices.md)

## Créer des clusters Service Fabric n'importe où
Vous pouvez créer des clusters Service Fabric dans de nombreux environnements pour y déployer vos applications. Cela peut être effectué dans Azure ou localement, sur Windows Server ou sous Linux. En outre, l'environnement de développement dans le Kit de développement logiciel (SDK) est identique à l'environnement de production, sans aucun émulateur impliqué. En d'autres termes, s’il s'exécute sur votre cluster de développement local, il se déploiera sur le même cluster dans d'autres environnements.

Pour plus d’informations, consultez [Déploiement universel sur Windows Server ou Linux avec Service Fabric](service-fabric-deploy-anywhere.md)

![Plateforme Service Fabric][Image1]

## Microservices Service Fabric avec et sans état

Service Fabric vous permet de créer des applications composées de microservices. Les microservices sans état (par exemple, les passerelles de protocole, les proxys web, etc.) ne conservent pas un état mutable en dehors des demandes du service et de leur réponse. Les rôles de travail Azure Cloud Services sont un exemple de service sans état. Les microservices avec état (par exemple, les comptes d’utilisateur, les bases de données, les appareils, les paniers d’achat, les files d’attente, etc.) conservent un état mutable faisant autorité au-delà de la demande et la réponse. Actuellement, les applications Internet sont constituées d’une combinaison de microservices avec et sans état.

Pourquoi utiliser des microservices avec état et sans état ? Voici les deux principales raisons :

1. La possibilité de créer des services OLTP à débit élevé, faible latence et tolérance de pannes, comme les vitrines interactives, la recherche, les systèmes Internet des objets (IoT), les systèmes commerciaux, les systèmes de traitement de carte de crédit et de détection des fraudes, la gestion des enregistrements personnels, etc., en conservant le code proche des données sur le même ordinateur.

2. La simplification de la conception des applications, car les microservices avec état ne nécessitent pas de files d’attente et de caches supplémentaires. Ceux-ci étaient généralement nécessaires pour répondre aux exigences de latence et de disponibilité d’une application purement sans état. Les services avec état étant naturellement hautement disponibles et à faible latence, vous avez moins d’éléments mobiles à gérer au sein de votre application.

Pour plus d'informations sur les modèles d'application avec Service Fabric, consultez [Scénarios d'application](service-fabric-application-scenarios.md) et [Choix d'une infrastructure de modèle de programmation](service-fabric-choose-framework.md) pour votre service

## Gestion du cycle de vie des applications
Service Fabric offre une excellente prise en charge de la gestion du cycle de vie complet des applications cloud : du développement au retrait éventuel, en passant par le déploiement, la gestion quotidienne et la maintenance.

Les fonctionnalités Service Fabric de gestion du cycle de vie des applications permettent aux administrateurs d'application / opérateurs informatiques d'utiliser des flux de travail simples et automatisés pour configurer, déployer, corriger et surveiller les applications. Ces flux de travail intégrés réduisent considérablement la charge pesant sur les opérateurs informatiques pour que vos applications soient disponibles en permanence.

La plupart des applications sont constituées d’une combinaison de microservices avec et sans état et d’autres exécutables/runtimes déployés ensemble. Service Fabric, grâce à des types forts sur les applications et aux microservices packagés, permet le déploiement de plusieurs instances d’application qui peuvent être gérées et mises à niveau individuellement. Plus important encore, Service Fabric peut déployer *n’importe quel* exécutable ou runtime et le rendre fiable. Par exemple, il peut servir à déployer ASP.NET Core 1, node.js, des machines virtuelles Java, des scripts ou tout autre élément qui compose votre application.

Pour plus d'informations sur la gestion du cycle de vie des applications, consultez [Cycle de vie des applications](service-fabric-application-lifecycle.md), et sur le déploiement de n'importe quel code, consultez [Déployer un exécutable invité](service-fabric-deploy-existing-app.md)

## Fonctionnalités clés
Service Fabric vous permet d'effectuer les opérations suivantes :

- Développer des applications hautement évolutives avec réparation spontanée.

- Développer des applications composées de microservices, à l’aide du modèle de programmation Service Fabric, ou simplement héberger des exécutables invités et d’autres infrastructures applicatives de votre choix comme ASP.NET Core 1, node.js, etc.

- Développer des microservices avec et sans état et les rendre très fiables.

- Simplifier la conception de votre application, en utilisant les microservices avec état à la place des files d’attente et des caches.

- Déployer dans Azure ou dans des clouds locaux qui exécutent Windows Server ou Linux sans aucune modification de code. Écrire une fois, puis déployer sur n'importe quel cluster Service Fabric.

- Développer une approche de « centre de données sur votre ordinateur ». L’environnement de développement local utilise le même code exécuté dans le centre de données Azure.

- Déployer des applications en quelques secondes.

- Déployer des applications avec une densité supérieure à celle des ordinateurs virtuels, en déployant des centaines ou des milliers d'applications par ordinateur.

- Déployer des versions différentes de la même application côte à côte, chacune pouvant être mise à niveau indépendamment.

- Gérer le cycle de vie de vos applications avec état sans interruption de service, y compris les mises à niveau avec rupture et sans rupture.

- Gérer des applications à l’aide de l’interface des API .NET, de Powershell ou de REST.

- Mettre à niveau et corriger des microservices dans les applications indépendamment.

- Surveiller et diagnostiquer l’intégrité de vos applications et définir des stratégies pour effectuer des réparations automatiques.

- Monter ou descendre en puissance votre cluster Service Fabric facilement, sachant que les applications s’adaptent en fonction des ressources disponibles.

- Observer la façon dont l’équilibrage des ressources avec réparation spontanée orchestre la redistribution des applications au sein du cluster Service Fabric pour récupérer après des défaillances et optimiser la distribution de la charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

* Pour en savoir plus :
	* [Pourquoi une approche de microservices pour la conception d’applications ?](service-fabric-overview-microservices.md)
	* [Vue d'ensemble de la terminologie](service-fabric-technical-overview.md)
* Configuration de votre [environnement de développement](service-fabric-get-started.md) Service Fabric  
* Choix d’une [infrastructure de modèle de programmation](service-fabric-choose-framework.md) pour votre service

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

<!---HONumber=AcomDC_0608_2016-->