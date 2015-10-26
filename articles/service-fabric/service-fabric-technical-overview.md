<properties
   pageTitle="Vue d’ensemble technique"
   description="Une vue d’ensemble technique de Service Fabric. Traite des concepts clés et présente une vue d’ensemble architecturale."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2015"
   ms.author="mfussell"/>

# Vue d'ensemble technique de Service Fabric

Service Fabric est une plateforme de systèmes distribués prenant en charge le développement d’applications évolutives, fiables, à faible latence et à gestion simplifiée pour le cloud. Ainsi, vous pouvez vous concentrer sur les besoins de votre entreprise, en laissant à Service Fabric le soin d’assurer la disponibilité et l’évolutivité de votre application.

## Concepts clés

**Cluster** : un ensemble connecté de machines virtuelles ou physiques dans lequel sont déployées les instances applicatives. Les clusters peuvent accepter des milliers de machines.

**Nœud** : une unité adressable d’un cluster. Les nœuds présentent des caractéristiques, telles que des propriétés de placement ou des ID uniques. Les nœuds peuvent rejoindre un cluster et se mettre en corrélation avec une instance de système d’exploitation avec Fabric.exe exécuté.

**Application/Type d’applications** : une collection de (micro)services. Considérez un type d’application comme un conteneur d’un ou de plusieurs types de services. Reportez-vous à l’article [Modèle d’application](service-fabric-application-model.md) pour mieux comprendre la structure d’un cluster (lui-même constitué de plusieurs nœuds), pouvant être composé de plusieurs types d’application.

**Service/Type de service** : code et configuration exécutant une fonction autonome (démarrant et s’exécutant indépendamment), par exemple un service de file d’attente ou un service de base de données. Un type d’application peut être constitué d’un ou de plusieurs types de services. Il existe deux types de services différents :

- Service sans état : un service présentant un état conservé sur un système de stockage externe, comme les bases de données Microsoft Azure ou le stockage de table Microsoft Azure. Si un nœud abritant une instance active de ce service est défaillant, une autre instance est automatiquement démarrée sur un autre nœud.

- Service avec état : un service qui présente un état et atteint les objectifs de fiabilité via la réplication entre les réplicas sur d’autres nœuds du cluster. Les services avec état possèdent un réplica principal et plusieurs réplicas secondaires. Si un nœud abritant un réplica actif de ce service est défaillant, un nouveau réplica est démarré sur un autre nœud. S’il s’agissait du réplica principal, un réplica secondaire est automatiquement promu.

**Instance d’application** : dans un cluster, vous pouvez créer de nombreuses instances applicatives d’un type d’application, qui présentent chacune un nom spécifique. Chaque instance applicative peut être gérée indépendamment et faire l’objet d’une gestion de versions d’autres instances applicatives du même type ou d’un type différent. Par ailleurs, ces éléments définissent l’isolation des ressources et de la sécurité.

**Instance de service** : code créé pour un type de service. Chaque instance de service présente un nom unique commençant par `fabric:/` et est associée à une instance applicative à désignation unique.

**Package d’application** : la collection de package de code de service et de fichiers de configuration combinés pour une application particulière. Il s’agit des fichiers physiques spécifiques déployés sous un format fichier/dossier. Par exemple, un package d’application dédié à une application de messagerie peut comporter un package de service de file d’attente, un package de service frontal et un package de service de base de données.

**Modèles de programmation** : deux modèles de programmation dédiés au développement d’applications sont disponibles dans Service Fabric :

- Services fiables : une API dédiée au développement de services avec et sans état, basée sur les classes `StatelessService` et `StatefulService` .NET, stockant l’état dans des collections .NET fiables (dictionnaire et file d’attente) et prenant en charge la connexion de diverses piles de communication comme l’API Web et WCF. Ce modèle de programmation est compatible avec les applications nécessitant l’exécution de calculs sur plusieurs unités d’état.

- Acteurs fiables : une API dédiée au développement d’objets avec et sans état à l’aide du modèle de programmation d’acteur virtuel, qui est adapté aux applications présentant plusieurs unités indépendantes d’état et de calcul.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
Pour en savoir plus sur Service Fabric, consultez les pages suivantes :

- [Modèle d’application](service-fabric-application-model.md)
- [Scénarios d’application](service-fabric-application-scenarios.md)
 

<!---HONumber=Oct15_HO3-->