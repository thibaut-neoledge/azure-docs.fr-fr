---
title: "Étapes suivantes de la création de projet Service Fabric | Microsoft Docs"
description: "Cet article contient des liens vers un ensemble de tâches de développement de base pour Service Fabric."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2424e5efe3392b08e58ceb05ec63f15c7ad32


---
# <a name="your-service-fabric-application-and-next-steps"></a>Votre application Service Fabric et étapes suivantes
Votre application Azure Service Fabric a été créée. Cet article décrit la constitution de votre projet et certaines étapes futures potentielles.

## <a name="your-application"></a>Votre application.
Chaque nouvelle application inclut un projet d'application. Il peut y avoir un ou deux projets supplémentaires en fonction du type de service choisi.

### <a name="the-application-project"></a>Le projet d'application
Le projet d'application se compose des éléments suivants :

* Un ensemble de références aux services qui composent votre application.
* Trois profils de publication (local à 1 nœud, local à 5 nœuds et cloud) que vous pouvez utiliser pour préserver les préférences d’utilisation d’environnements différents, comme les préférences liées à un point de terminaison de cluster et si vous souhaitez exécuter les déploiements de mise à niveau par défaut.
* Trois fichiers de paramètres d’application (du même type que ci-dessus) que vous pouvez utiliser pour gérer les configurations d’application spécifiques à l’environnement, comme le nombre de partitions à créer pour un service.
* Un script de déploiement que vous pouvez utiliser pour déployer votre application à partir de la ligne de commande ou dans le cadre d’un pipeline de déploiement et d’intégration continue automatisée.
* Le manifeste d'application qui décrit l'application. Le manifeste se trouve dans le dossier ApplicationPackageRoot.

### <a name="stateless-service"></a>Service sans état
Quand vous ajoutez un nouveau service sans état, Visual Studio ajoute un projet de service à votre solution qui inclut un type hérité de `StatelessService`. Le service incrémente une variable locale dans un compteur.

### <a name="stateful-service"></a>Service avec état
Quand vous ajoutez un nouveau service avec état, Visual Studio ajoute un projet de service à votre solution qui inclut un type hérité de `StatefulService`. Le service incrémente un compteur dans sa méthode `RunAsync` et stocke le résultat dans un `ReliableDictionary`.

### <a name="actor-service"></a>Service d’acteur
Quand vous ajoutez un nouvel acteur fiable (Reliable Actor), Visual Studio ajoute deux projets à votre solution : un projet d’acteur et un projet d’interface.

Le projet d’acteur fournit des méthodes pour la définition et l’obtention de la valeur d’un compteur qui persiste de manière fiable au sein de l’état de l’acteur. Le projet d'interface fournit une interface que les autres services peuvent utiliser pour appeler l'acteur.

### <a name="stateless-web-api"></a>API web sans état
Le projet d’API web sans état fournit un service web de base que vous pouvez utiliser pour ouvrir votre application à des clients externes. Pour plus d’informations sur la structure du projet, consultez [Prise en main : services de l’API Web Service Fabric avec auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md).

### <a name="aspnet-core"></a>ASP.NET Core
Le Kit de développement logiciel (SDK) Service Fabric fournit le même ensemble de modèles ASP.NET Core que ceux disponibles pour les projets ASP.NET Core autonomes : vide, [Web API][aspnet-webapi] et [Web Application][aspnet-webapp].

## <a name="next-steps"></a>Étapes suivantes
### <a name="create-an-azure-cluster"></a>Création d’un cluster Azure
Le SDK Service Fabric fournit un cluster local pour le développement et les tests. Pour créer un cluster dans Azure, consultez [Configuration d’un cluster Service Fabric à partir du portail Azure][create-cluster-in-portal].

### <a name="try-deploying-to-azure-for-free-with-party-clusters"></a>Essayer gratuitement de déployer des applications vers Azure grâce au service Party Clusters
Si vous souhaitez essayer de déployer et de gérer vos applications dans Azure sans configurer vos propres clusters, vous pouvez utiliser le [service Party Clusters](http://aka.ms/tryservicefabric)gratuit.

### <a name="publish-your-application-to-azure"></a>Publication de votre application dans Azure
Vous pouvez publier votre application directement à partir de Visual Studio vers un cluster Azure. Pour en savoir plus, consultez [Publication de votre application dans Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Visualisation de votre cluster à l'aide de l'outil Service Fabric Explorer
L’outil Service Fabric Explorer offre un moyen facile de visualiser votre cluster, notamment les applications déployées et la disposition physique. Pour en savoir plus, consultez [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Contrôle de la version et mise à niveau de vos services
Service Fabric permet une gestion de version indépendante et la mise à niveau de services indépendants dans une application. Pour en savoir plus, consultez [Gestion de version et mise à niveau de vos services][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configuration de l’intégration continue avec Visual Studio Team Services
Pour savoir comment configurer un processus d’intégration continue pour votre application Service Fabric, consultez [Configurer l’intégration continue avec Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html



<!--HONumber=Nov16_HO3-->


