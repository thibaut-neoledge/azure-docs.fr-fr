<properties 
   pageTitle="Vue d'ensemble de Service Fabric" 
   description="Présentation de Service Fabric, où les applications sont composées de microservices. Service Fabric est une plateforme de systèmes distribués qui permet de créer des applications évolutives, fiables et faciles à gérer pour le cloud" 
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
   ms.date="04/14/2015"
   ms.author="mfussell"/>

# Vue d'ensemble de Service Fabric
Service Fabric est une plateforme de systèmes distribués qui permet de créer des applications évolutives, fiables et faciles à gérer pour le cloud. Service Fabric résout les problèmes non négligeables du développement et de la gestion des applications cloud. En utilisant Service Fabric, les développeurs et administrateurs n'ont plus à résoudre les problèmes d'infrastructure complexes et peuvent se concentrer à la place sur l'implémentation de charges de travail stratégiques exigeantes, sachant qu'elles sont évolutives, fiables et faciles à gérer. Service Fabric représente la plateforme middleware de nouvelle génération pour la création et la gestion de ces services d'entreprise de niveau 1 à l'échelle du cloud.

## Applications composées de microservices ##
Service Fabric vous permet de créer et gérer des applications évolutives et fiables, composées de microservices s'exécutant à très haute densité sur un pool partagé d'ordinateurs (communément appelé cluster Service Fabric). Il fournit un runtime sophistiqué pour la création de microservices distribués et évolutifs avec et sans état et de fonctionnalités de gestion applicative complètes pour la configuration, le déploiement, la surveillance, la mise à niveau/mise à jour corrective et la suppression d'applications déployées.

Service Fabric alimente de nombreux services Microsoft aujourd'hui tels qu'Azure SQL Database, Azure DocumentDB, Cortana, Power BI, Microsoft Intune, Azure Event Hubs, plusieurs services Azure principaux et Skype Entreprise, pour n'en citer que quelques-uns.

Service Fabric est adapté à la création de services « nés dans le cloud » qui peuvent commencer petit, si nécessaire, et évoluer à grande échelle avec des centaines ou des milliers d'ordinateurs, formant des clusters Service Fabric dans les groupes à haute disponibilité d'une ou plusieurs régions.

Actuellement, les services Internet sont créés à l'aide de microservices. Les microservices sont, par exemple, les passerelles de protocole, les profils utilisateur, les paniers d'achat, le traitement des stocks, les files d'attente, les caches, etc. Service Fabric est une plateforme de microservices qui attribue à chaque microservice un nom unique pouvant être avec ou sans état.

Service Fabric fournit des fonctionnalités complètes de gestion du runtime et du cycle de vie pour les applications constituées de ces microservices. Il héberge les microservices dans des conteneurs déployés et activés sur le cluster Service Fabric. De la même façon qu'il est possible d'augmenter la densité d'un ordre de grandeur en passant des ordinateurs virtuels aux conteneurs, un ordre de grandeur de même densité est désormais possible en passant des conteneurs aux microservices. Par exemple, un seul cluster Azure SQL Database, qui repose sur Service Fabric, comprend des centaines d'ordinateurs exécutant des dizaines de milliers de conteneurs qui hébergent un total de centaines de milliers de bases de données (chaque base de données est un microservice Service Fabric avec état). Il en est de même avec Event Hubs ou les autres services mentionnés ci-dessus. C'est pourquoi le terme « hyper-extensibilité » peut être utilisé pour décrire les fonctionnalités de Service Fabric : si les conteneurs offre une haute densité, les microservices offrent une hyper-extensibilité.

![Plateforme Service Fabric][Image1]

## Microservices Service Fabric avec et sans état

Les microservices sans état (par exemple, les passerelles de protocole, les proxys web, etc.) ne conservent aucun état mutable en dehors des demandes du service et de leur réponse. Les rôle de travail Cloud Services sont un exemple de service sans état. Les microservices avec état (par exemple, les comptes d'utilisateur, les bases de données, les appareils, les paniers d'achat, les files d'attente, etc.) conservent un état mutable faisant autorité au-delà de la demande et la réponse. Actuellement, les applications Internet sont constituées d'une combinaison de microservices avec et sans état.
 
Pourquoi les microservices avec état sont-ils importants ? Pourquoi ne pas simplement utiliser les services sans état dans toutes les circonstances ? Deux raisons :

1) La possibilité de créer des services OLTP à débit élevé, faible latence et tolérance de pannes, comme les vitrines interactives, la recherche, les systèmes Internet des objets (IoT), les systèmes commerciaux, les systèmes de traitement de carte de crédit et de détection des fraudes, la gestion des enregistrements personnels, etc., en conservant le code proche des données sur le même ordinateur.

2) La simplification de la conception d'applications sous forme de microservices avec état permet de supprimer les caches et files d'attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d'une application purement sans état. Les services avec état étant naturellement hautement disponibles et à faible latence, vous avez moins d'éléments mobiles à gérer au sein de votre application.

Pour plus d'informations sur les modèles d'application et la conception à l'aide de Service Fabric, consultez [Scénarios d'application](../service-fabric-application-scenarios)

## Gestion du cycle de vie des applications
Service Fabric offre une excellente prise en charge de la gestion complète du cycle de vie des applications cloud : du développement au retrait éventuel, en passant par le déploiement, la gestion quotidienne et la maintenance.

Les fonctionnalités Service Fabric de gestion du cycle de vie des applications permettent aux administrateurs d'application / opérateurs informatiques d'utiliser des flux de travail simples et automatisés pour configurer, déployer, corriger et surveiller les applications. Ces flux de travail intégrés réduisent considérablement la charge pesant sur les opérateurs informatiques pour que vos applications soient disponibles en permanence.

La plupart des applications sont constituées d'une combinaison de microservices avec et sans état et d'autres EXE/runtimes déployés ensemble. Service Fabric, grâce à des types forts sur les applications et aux microservices packagés, permet le déploiement de plusieurs instances d'application qui peuvent être gérées et mises à niveau individuellement. Plus important encore, Service Fabric est en mesure de déployer *n'importe quel* exécutable ou runtime et le rendre fiable. Par exemple, il peut servir à déployer ASP.NET 5, node.js, des scripts ou tout autre élément qui compose votre application.
  
Pour plus d'informations sur la gestion du cycle de vie des applications, consultez [Cycle de vie des applications](../service-fabric-application-lifecycle)

## Fonctionnalités clés
Service Fabric vous permet d'effectuer les opérations suivantes :

- Développer des applications hautement évolutives avec réparation spontanée.

- Développer une approche de « centre de données sur votre ordinateur ». L'environnement de développement local utilise le même code qui s'exécute dans le centre de données Azure.
 
- Développer des applications composées de microservices, d'exécutables et d'autres infrastructures applicatives de votre choix comme ASP.NET, nodejs, etc.

- Développer des (micro)services avec et sans état et les rendre très fiables.

- Simplifier la conception de votre application, en utilisant les (micro)services avec état à la place des files d'attente et des caches.
 
- Déployer des applications en quelques secondes.

- Déployer dans Azure ou dans des cloud locaux qui exécutent Windows Server sans aucune modification de code. Écrire une fois, puis déployer sur n'importe quel cluster Service Fabric.

- Déployer des applications avec une densité supérieure à celle des ordinateurs virtuels, en déployant des centaines ou des milliers d'applications par ordinateur.

- Déployer des versions différentes de la même application côte à côte, chacune pouvant être mise à niveau indépendamment.
 
- Gérer le cycle de vie de vos applications avec état sans interruption de service, y compris les mises à niveau avec rupture et sans rupture.

- Gérer des applications à l'aide de l'interface des API .NET, de Powershell ou de REST.
 
- Mettre à niveau et corriger des microservices dans les applications indépendamment.

- Surveiller et diagnostiquer l'intégrité de vos applications et définir des stratégies pour effectuer des réparations automatiques.

- Monter ou descendre en puissance votre cluster Service Fabric facilement, sachant que les applications s'adaptent en fonction des ressources disponibles.

- Observer la façon dont l'équilibrage des ressources avec réparation spontanée orchestre la redistribution des applications au sein du cluster Service Fabric pour récupérer après des défaillances et optimiser la distribution de la charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

Pour plus d'informations, consultez [Vue d'ensemble technique](../service-fabric-technical-overview).

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png

 

<!---HONumber=July15_HO4-->