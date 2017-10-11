---
title: "Scénarios et conception d’applications | Microsoft Docs"
description: "Vue d'ensemble des catégories d'applications cloud dans Service Fabric. Traite de la conception d’applications à l’aide de services avec ou sans état"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: mfussell
ms.openlocfilehash: 471ec6f45f4152fbac56242ef3ce906f8af00b54
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="service-fabric-application-scenarios"></a>Scénarios d'applications Service Fabric
Azure Service Fabric offre une plateforme d’infrastructure fiable et flexible qui vous permet d’écrire et d’exécuter de nombreux types de services et d’applications d’entreprise. Ces applications et microservices peuvent être sans état ou avec état, et font l’objet d’un équilibrage des ressources entre les machines virtuelles pour optimiser l’efficacité. L'architecture unique de Service Fabric vous permet d'exécuter dans vos applications des traitements d'événement, des transactions parallèles, des calculs en mémoire et des analyses de données pratiquement en temps réel. Vous pouvez facilement augmenter ou réduire l’échelle de vos applications (vraiment dehors ou dedans), en fonction de vos besoins en ressources.

La plateforme Service Fabric dans Azure est idéale pour les catégories d’applications suivantes :

* **Services hautement disponibles**: les services Service Fabric assurent un basculement rapide en créant plusieurs réplicas de services secondaires. Si un nœud, un processus ou un service individuel tombe en panne en raison d’une défaillance matérielle ou autre, un des réplicas secondaires est promu réplica principal avec une perte de service minimale.
* **Services extensibles**: les différents services peuvent être partitionnés, permettant une montée en charge de l’état à l’ensemble du cluster. En outre, les services individuels peuvent être créés et supprimés à la volée. L’échelle des services peut être rapidement et facilement augmentée de quelques instances à plusieurs milliers d’instances et réduite d’autant, en fonction de vos besoins en ressources. Vous pouvez utiliser Service Fabric pour générer ces services et gérer l’ensemble de leur cycle de vie.
* **Calcul sur des données non statiques**: Service Fabric vous permet de générer des applications, des entrées/sorties et de calculer des applications intensives avec état. Service Fabric permet la colocalisation du traitement (calcul) et des données dans les applications. Normalement, lorsque votre application a besoin d’un accès aux données, il y a une latence réseau associée à un niveau de stockage ou de cache de données externe. Avec Service Fabric avec état, cette latence est éliminée, ce qui permet des lectures et écritures performantes. Imaginez, par exemple, une application qui effectue des sélections de recommandation en temps quasi-réel pour des clients, avec un délai d’aller-retour de moins de 100 millisecondes. Les caractéristiques de latence et les performances de Service Fabric (où le calcul de la sélection de la recommandation est colocalisé avec les données et les règles) offrent une expérience réactive à l’utilisateur par rapport au modèle de mise en œuvre standard consistant à extraire les données nécessaires depuis le stockage distant.  
* **Applications interactives basées sur les sessions** : Service Fabric est utile si vos applications, telles que les jeux en ligne ou la messagerie instantanée, ont besoin d’effectuer des opérations de lecture et d’écriture avec une faible latence. Service Fabric vous permet de générer ces applications interactives avec état sans avoir à créer de cache ou de magasin distinct nécessaire aux applications sans état (cela augmente la latence et entraîne des problèmes de cohérence).
* **Flux de travail et analyse des données**: les opérations de lecture et écriture rapides de Service Fabric sont particulièrement adaptées pour les applications qui doivent traiter des événements ou des flux de données de manière fiable. Service Fabric prend également en charge les applications qui décrivent les pipelines de traitement dans lequel les résultats doivent être fiables et transmis à l’étape de traitement sans perte. Ceci s’applique en particulier aux systèmes transactionnels et financiers, qui imposent une garantie de calcul et de cohérence des données.
* **Collecte, traitement des données et IoT** : dans la mesure où Service Fabric, par l’intermédiaire de ses services avec état, gère une échelle importante et possède une faible latence, il est idéal pour traiter des données sur des millions d’appareils sur lesquels les données de l’appareil et le calcul sont colocalisés.
Plusieurs clients ont établi des systèmes IoT avec Service Fabric notamment [BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) et [Mesh Systems](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/).

## <a name="application-design-case-studies"></a>Études de cas de conception d’applications
Plusieurs études de cas illustrant la façon dont Service Fabric permet de concevoir des applications sont publiées sur le [blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) et le [site des solutions microservices](https://azure.microsoft.com/solutions/microservice-applications/).

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>Conception d’applications composées de microservices avec et sans état
La création d’applications avec des rôles de travail Azure Cloud Service est un exemple de service sans état. À l’opposé, les microservices avec état maintiennent leur état faisant autorité au-delà de la demande et de la réponse correspondante. Ceci garantit une haute disponibilité et une cohérence de l’état grâce à des API simples qui fournissent des garanties transactionnelles soutenues par la réplication. Les services avec état de Service Fabric démocratisent la haute disponibilité sur tous les types d’applications, sans se limiter aux bases de données et autres banques de données. Il s’agit d’une évolution naturelle. Les applications sont déjà passées d’une simple utilisation de bases de données purement relationnelles pour la haute disponibilité à l’emploi de bases de données NoSQL. Les applications elles-mêmes peuvent maintenant avoir un état « actif » et bénéficier d’une gestion des données en interne pour optimiser leurs performances, sans pour autant sacrifier la fiabilité, la cohérence ou la disponibilité des données.

Quand vous créez des applications comprenant des microservices, vous avez généralement une combinaison d’applications web sans état (ASP.NET, Node.js, etc.) qui appellent des services de couche intermédiaire métier sans et avec état, tous déployés dans le même cluster Service Fabric au moyen des commandes de déploiement Service Fabric. Chacun de ces services est indépendant de l’évolution, de la fiabilité et de l’utilisation des ressources, ce qui améliore grandement la souplesse du développement et de la gestion du cycle de vie.

Les microservices avec état simplifient les conceptions d’applications, car ils suppriment les caches et files d’attente supplémentaires, jusque-là indispensables pour répondre aux exigences de disponibilité et de latence d’une application purement sans état. Les services avec état étant naturellement hautement disponibles et à faible latence, vous avez moins d’éléments mobiles à gérer au sein de votre application. Les schémas ci-dessous comparent la conception d’une application sans état à la conception d’une application avec état. Grâce aux modèles de programmation [Reliable Services](service-fabric-reliable-services-introduction.md) et [Reliable Actors](service-fabric-reliable-actors-introduction.md), les services avec état réduisent la complexité de l’application, tout en atteignant un débit élevé et une faible latence.

## <a name="an-application-built-using-stateless-services"></a>Application créée à l'aide de services sans état
![Application utilisant un service sans état][Image1]

## <a name="an-application-built-using-stateful-services"></a>Application créée à l'aide de services avec état
![Application utilisant un service sans état][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* Écouter les [études de cas clients](https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=qDJnf86yC_5206218965
)
* Lire les [études de cas clients](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)
* En savoir plus sur les [modèles et scénarios](service-fabric-patterns-and-scenarios.md)

* Commencez à créer des services sans et avec état au moyen des modèles de programmation [Reliable Services](service-fabric-reliable-services-quick-start.md) et [Reliable Actors](service-fabric-reliable-actors-get-started.md) de Service Fabric.
* Consultez également les rubriques suivantes :
  * [En savoir plus sur les microservices](service-fabric-overview-microservices.md)
  * [Définition et gestion de l’état du service](service-fabric-concepts-state.md)
  * [Disponibilité des services Service Fabric](service-fabric-availability-services.md)
  * [Mise à l’échelle des applications Service Fabric](service-fabric-concepts-scalability.md)
  * [Partitionnement des services Service Fabric](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
