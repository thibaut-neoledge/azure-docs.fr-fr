<properties 
   pageTitle="Scénarios et conception d'applications avec Service Fabric" 
   description="Catégories d'applications. Conception d'applications à l'aide de services avec ou sans état" 
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
   ms.date="04/24/2015"
   ms.author="mfussell"/>

# Scénarios d'applications Service Fabric

Service Fabric et Azure offrent une plateforme d'infrastructure cloud fiable et flexible qui vous permet d'exécuter de nombreux types de services et d'applications d'entreprise. Ces applications et microservices peuvent être sans état ou avec état, et font l'objet d'un équilibrage des ressources entre les machines virtuelles pour optimiser l'efficacité. L'architecture unique de Service Fabric vous permet d'exécuter dans vos applications des traitements d'événement, des transactions parallèles, des calculs en mémoire et des analyses de données pratiquement en temps réel. Vous pouvez facilement augmenter ou réduire l'échelle de vos applications, en fonction de vos besoins en ressources.

La plateforme Service Fabric dans Azure est idéale pour les catégories d'applications et de services suivantes :

- **Services hautement disponibles** : les services Service Fabric assurent un basculement extrêmement rapide. Plusieurs réplicas de service secondaires demeurent disponibles. Si un nœud tombe en panne en raison d'une défaillance matérielle, un des réplicas secondaires est immédiatement promu réplica principal avec une perte de service négligeable pour les clients. L'échelle des services peut être rapidement et facilement augmentée de quelques instances à plusieurs milliers d'instances et réduite d'autant, selon vos besoins en ressources. Vous pouvez utiliser Service Fabric pour générer et gérer le cycle de vie de ces services cloud extensibles.

- **Services extensibles** : les différents services peuvent être partitionnés, permettant une montée en charge de l'état à l'ensemble du cluster. En outre, plusieurs services Service Fabric peuvent être créés et subir une montée en charge.
 
- **Calcul sur des données non statiques** : Service Fabric vous permet de générer des applications avec état gourmandes en calculs et en opérations d'E/S. Les ressources de traitement et de données dans les applications Service Fabric se trouvant au même endroit, quand votre application doit effectuer des opérations de lecture et d'écriture rapides, la latence du réseau associée à un niveau de stockage ou à un cache de données externes est éliminée. Par exemple, supposons que vous disposiez d'une application qui sélectionne des publicités pratiquement en temps réel avec une durée d'aller-retour inférieure à 100 ms, délai en deçà duquel le calcul des règles de sélection de publicité procure une expérience réactive à l'utilisateur.
 
- **Applications interactives basées sur les sessions** : Service Fabric est utile si vos applications, telles que les jeux en ligne ou la messagerie instantanée, ont besoin d'effectuer des opérations de lecture et d'écriture avec une faible latence. Service Fabric vous permet de générer ces applications interactives avec état sans créer de cache ou de magasin distinct nécessaire aux applications sans état.
 
- **Traitement graphique distribué** : la croissance des réseaux sociaux a augmenté considérablement la nécessité d'analyser les graphes à grande échelle en parallèle. La mise à l'échelle rapide et le traitement des charges en parallèle font de Service Fabric une plateforme naturelle pour le traitement des graphes à grande échelle. Service Fabric vous permet de créer des services hautement extensibles pour les groupes tels que les réseaux sociaux, l'aide à la décision et la recherche scientifique.
 
- **Flux de travail et analyse des données** : les opérations de lecture/écriture rapides de Service Fabric sont particulièrement adaptées pour les applications qui doivent traiter des événements ou des flux de données de manière fiable. Service Fabric convient également pour les applications qui décrivent un pipeline de traitement, où les résultats ne peuvent être transmis sans perte d'une étape de traitement à l'autre que s'ils sont fiables, notamment les systèmes transactionnels et financiers, où les garanties en termes de calcul et de cohérence des données sont essentielles.

## Conception d'applications composées de microservices avec et sans état ##
La création d'applications avec des rôles de travail Azure Cloud Service est un exemple de services sans état. À l'opposé, les microservices avec état maintiennent l'état faisant autorité au-delà de la demande et de la réponse correspondante, tout en assurant la réplication de l'état via des API simples. Les services avec état démocratisent la haute disponibilité pour toutes les applications, pas seulement pour les bases de données et autres magasins de données. C'est l'évolution naturelle de la conception : les applications, qui utilisaient auparavant des bases de données purement relationnelles pour la haute disponibilité, sont passées à des bases de données NoSQL pour, aujourd'hui, abriter elles-mêmes la gestion de l'état des données « à chaud » qui doivent être accessibles aux processus de calcul.

Quand vous créez des applications comprenant des microservices, vous avez généralement une combinaison d'applications web sans état (ASP.NET, node.js, etc.) qui appellent des services de couche intermédiaire métier sans et avec état, tous déployés dans le même cluster Service Fabric au moyen des commandes de déploiement Service Fabric. L'indépendance de la mise à l'échelle, de la fiabilité et de l'utilisation des ressources pour chacun de ces microservices fournit cette souplesse dans le développement et la gestion du cycle de vie.
  
Les microservices avec état simplifient les conceptions d'applications, car ils suppriment les caches et files d'attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d'une application purement sans état. Les services avec état étant en soi hautement disponibles et à faible latence, cela signifie moins de composantes mobiles à gérer au sein de votre application. Les diagrammes ci-dessous comparent la conception d'une application sans état et la conception d'une application avec état. Dans le cas d'une application avec état, grâce aux modèles de programmation [Services fiables](../Service-Fabric/service-fabric-reliable-services-introduction.md) et [Acteurs fiables](service-fabric-reliable-actors-introduction.md), les services avec état réduisent la complexité de l'application, tout en atteignant un débit élevé et une faible latence.

## Application créée à l'aide de services sans état##
![Application utilisant un service sans état][Image1]

## Application créée à l'aide de services avec état##
![Application utilisant un service sans état][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes


Commencez à créer des services sans et avec état au moyen des modèles de programmation [Services fiables](service-fabric-reliable-services-quick-start.md) et [Acteurs fiables](service-fabric-reliable-actors-get-started.md) Service Fabric.

Consultez également les rubriques suivantes :

[Définition et gestion de l'état des services](service-fabric-concepts-state.md)

[Disponibilité des services](../service-fabric-concepts-availability-services.md)

[Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)

[Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
 
 

<!---HONumber=July15_HO2-->