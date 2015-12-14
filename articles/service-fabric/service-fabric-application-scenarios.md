<properties 
   pageTitle="Scénarios et conception d'applications | Microsoft Azure" 
   description="Vue d'ensemble des catégories d'applications cloud dans Service Fabric. Traite de la conception d'applications à l'aide de services avec ou sans état"
   services="service-fabric" 
   documentationCenter=".net" 
   authors="msfussell" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="11/30/2015"
   ms.author="mfussell"/>

# Scénarios d'applications Service Fabric

Microsoft Azure Service Fabric offre une plateforme d’infrastructure fiable et flexible qui vous permet d’écrire et d’exécuter de nombreux types de services et d’applications d’entreprise. Ces applications et microservices peuvent être sans état ou avec état, et font l'objet d'un équilibrage des ressources entre les machines virtuelles pour optimiser l'efficacité. L'architecture unique de Service Fabric vous permet d'exécuter dans vos applications des traitements d'événement, des transactions parallèles, des calculs en mémoire et des analyses de données pratiquement en temps réel. Vous pouvez facilement augmenter ou réduire l’échelle de vos applications (vraiment dehors ou dedans), en fonction de vos besoins en ressources.

La plateforme Service Fabric dans Azure est idéale pour les catégories d'applications et de services suivantes :

- **Services hautement disponibles** : les services Service Fabric assurent un basculement extrêmement rapide. Service Fabric permet de créer plusieurs réplicas de service secondaire. Si un nœud, un processus ou un service individuel tombe en panne en raison d’une défaillance matérielle, un des réplicas secondaires est immédiatement promu réplica principal avec une perte de service négligeable pour les clients. 

- **Services extensibles** : les différents services peuvent être partitionnés, permettant une montée en charge de l'état à l'ensemble du cluster. En outre, les services individuels peuvent être créés et supprimés à la volée. L’échelle des services peut être rapidement et facilement augmentée de quelques instances à plusieurs milliers d’instances et réduite d’autant, selon vos besoins en ressources. Vous pouvez utiliser Service Fabric pour générer ces services gérer l’ensemble de leur cycle de vie.
 
- **Calcul sur des données non statiques** : Service Fabric vous permet de générer des applications, des E/S et de calculer des applications intensives avec état. Service Fabric permet la colocalisation du traitement (calcul) et des données dans les applications. Normalement, aujourd’hui lorsque votre application requiert l’accès aux données. Il y a une latence réseau associée à un niveau de cache de données externe ou de stockage. Avec Service Fabric avec état, cette latence est éliminée, ce qui permet des lectures et écritures performantes. Par exemple, si vous avez une application qui effectue des sélections de recommandation quasiment en temps réel pour les clients avec un délai d’aller-retour de moins de 100 ms. Les caractéristiques de latence et les performances de Service Fabric (où le calcul de la sélection de la recommandation est colocalisé avec les données et les règles) offre une expérience réactive à l’utilisateur par rapport au modèle de mise en œuvre standard consistant à extraire les données nécessaires depuis le stockage étendu.
 
- **Applications interactives basées sur les sessions** : Service Fabric est utile si vos applications, telles que les jeux en ligne ou la messagerie instantanée, ont besoin d'effectuer des opérations de lecture et d'écriture avec une faible latence. Service Fabric vous permet de générer ces applications interactives avec état sans avoir à créer de cache ou d’applications sans état (ce qui augmente la latence et en introduisant potentiellement des problèmes de cohérence.
 
- **Traitement graphique distribué** : la croissance des réseaux sociaux a augmenté considérablement la nécessité d'analyser les graphes à grande échelle en parallèle. La mise à l'échelle rapide et le traitement des charges en parallèle font de Service Fabric une plateforme naturelle pour le traitement des graphes à grande échelle. Service Fabric vous permet de créer des services hautement extensibles pour les groupes tels que les réseaux sociaux, l'aide à la décision et la recherche scientifique.
 
- **Flux de travail et analyse des données** : les opérations de lecture/écriture rapides de Service Fabric sont particulièrement adaptées pour les applications qui doivent traiter des événements ou des flux de données de manière fiable. Service Fabric convient également pour les applications qui décrivent un pipeline de traitement, où les résultats ne peuvent être transmis sans perte d'une étape de traitement à l'autre que s'ils sont fiables, notamment les systèmes transactionnels et financiers, où les garanties en termes de calcul et de cohérence des données sont essentielles.

## Conception d'applications composées de microservices avec et sans état
La création d'applications avec des rôles de travail Azure Cloud Service est un exemple de services sans état. Contrairement à cela, les microservices avec état maintiennent l’état d’autorité au-delà de la demande et de sa réponse, ce qui offre une haute disponibilité et la cohérence de cet état via les API simples qui fournissent des garanties transactionnelles sauvegardées par la réplication. Les services avec état Service Fabric démocratisent la haute disponibilité (HA), sur tous les types d’applications, et pas seulement des bases de données et autres banques de données. Il s’agit d’une évolution naturelle : les applications ont déjà évolué d’un état de simples bases de données purement relationnelles pour les bases de données HA à NoSQL ; maintenant, les applications elles-mêmes peuvent avoir un état « actif » et les données gérées au sein de celles-ci pour des gains de performances supplémentaires sans sacrifier la fiabilité, la cohérence ou disponibilité.

Quand vous créez des applications comprenant des microservices, vous avez généralement une combinaison d’applications web sans état (ASP.NET, node.js, etc.) qui appellent des services de couche intermédiaire métier sans et avec état, tous déployés dans le même cluster Service Fabric au moyen des commandes de déploiement Service Fabric. Chacun de ces services est indépendant de l'évolution, de la fiabilité et de l'utilisation des ressources, ce qui améliore grandement la souplesse dans le développement et la gestion du cycle de vie.
  
Les microservices avec état simplifient les conceptions d'applications, car ils suppriment les caches et files d'attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d'une application purement sans état. Les services avec état étant naturellement hautement disponibles et à faible latence, vous avez moins d’éléments mobiles à gérer au sein de votre application. Les diagrammes ci-dessous comparent la conception d'une application sans état et la conception d'une application avec état. Grâce aux modèles de programmation [Reliable Services](service-fabric-reliable-services-introduction.md) et [Reliable Actors](service-fabric-reliable-actors-introduction.md), les services avec état réduisent la complexité de l'application, tout en atteignant un débit élevé et une faible latence.

## Application créée à l'aide de services sans état##
![Application utilisant un service sans état][Image1]

## Application créée à l'aide de services avec état##
![Application utilisant un service sans état][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes


Commencez à créer des services sans et avec état au moyen des modèles de programmation [Services fiables](service-fabric-reliable-services-quick-start.md) et [Acteurs fiables](service-fabric-reliable-actors-get-started.md) Service Fabric.

Consultez également les rubriques suivantes :

[En savoir plus sur les microservices](service-fabric-overview-microservices.md)

[Définition et gestion de l’état des services](service-fabric-concepts-state.md)

[Disponibilité des services](service-fabric-availability-services.md)

[Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)

[Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=AcomDC_1203_2015-->