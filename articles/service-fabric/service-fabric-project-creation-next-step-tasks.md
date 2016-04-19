<properties
   pageTitle="Étapes suivantes de la création de projet Service Fabric | Microsoft Azure"
   description="Cet article contient des liens vers un ensemble de tâches de développement de base pour Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/27/2016"
   ms.author="seanmck"/>

# Votre application Service Fabric et étapes suivantes
Votre application Azure Service Fabric a été créée. Cet article décrit la constitution de votre projet et certaines étapes futures potentielles.

## Votre application.
Chaque nouvelle application inclut un projet d'application. Il peut y avoir un ou deux projets supplémentaires en fonction du type de service choisi.

### Le projet d'application
Le projet d'application se compose des éléments suivants :

- Un ensemble de références aux services qui composent votre application.

- Deux profils de publication (local et cloud) que vous pouvez utiliser pour préserver les préférences d’utilisation d’environnements différents, comme les préférences liées à un point de terminaison de cluster et si vous souhaitez exécuter les déploiements de mise à niveau par défaut.

- Deux fichiers (local et cloud) de paramètres de l'application que vous pouvez utiliser pour gérer les configurations de l'application spécifiques à l'environnement, comme le nombre de partitions à créer pour un service.

- Un script de déploiement que vous pouvez utiliser pour déployer votre application à partir de la ligne de commande ou dans le cadre d’un pipeline de déploiement et d’intégration continue automatisée.

- Le manifeste d'application qui décrit l'application. Le manifeste se trouve dans le dossier ApplicationPackageRoot.

### Service sans état
Quand vous ajoutez un nouveau service sans état, Visual Studio ajoute un projet de service à votre solution qui inclut un type hérité de `StatelessService`. Le service incrémente une variable locale dans un compteur.

### Service avec état
Quand vous ajoutez un nouveau service avec état, Visual Studio ajoute un projet de service à votre solution qui inclut un type hérité de `StatefulService`. Le service incrémente un compteur dans sa méthode `RunAsync` et stocke le résultat dans un `ReliableDictionary`.

### Service d’acteur
Quand vous ajoutez un nouvel acteur fiable (Reliable Actor), Visual Studio ajoute deux projets à votre solution : un projet d’acteur et un projet d’interface.

Le projet d’acteur fournit des méthodes pour la définition et l’obtention de la valeur d’un compteur qui persiste de manière fiable au sein de l’état de l’acteur. Le projet d'interface fournit une interface que les autres services peuvent utiliser pour appeler l'acteur.

### API web sans état
Le projet d’API web sans état fournit un service web de base que vous pouvez utiliser pour ouvrir votre application à des clients externes. Pour plus d’informations sur la structure du projet, consultez [Prise en main : services de l’API Web Service Fabric avec auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi).

## Étapes suivantes
### Création d’un cluster Azure
Le SDK Service Fabric fournit un cluster local pour le développement et les tests. Pour créer un cluster dans Azure, consultez [Configuration d’un cluster Service Fabric à partir du portail Azure][create-cluster-in-portal].

### Essayer gratuitement de déployer des applications vers Azure grâce au service Party Clusters

Si vous souhaitez essayer de déployer et de gérer vos applications dans Azure sans configurer vos propres clusters, vous pouvez utiliser le [service Party Clusters](http://aka.ms/tryservicefabric) gratuit.

### Publication de votre application dans Azure
Vous pouvez publier votre application directement à partir de Visual Studio vers un cluster Azure. Pour en savoir plus, consultez [Publication de votre application dans Azure][publish-app-to-azure].

### Visualisation de votre cluster à l'aide de l'outil Service Fabric Explorer
L’outil Service Fabric Explorer offre un moyen facile de visualiser votre cluster, notamment les applications déployées et la disposition physique. Pour en savoir plus, consultez [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer][visualize-with-sfx].

### Contrôle de la version et mise à niveau de vos services
Service Fabric permet une gestion de version indépendante et la mise à niveau de services indépendants dans une application. Pour en savoir plus, consultez [Gestion de version et mise à niveau de vos services][app-upgrade-tutorial].

### Configuration de l’intégration continue avec Visual Studio Team Services
Pour savoir comment configurer un processus d’intégration continue pour votre application Service Fabric, consultez [Configurer l’intégration continue avec Visual Studio Team Services][ci-with-vso].



<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_0406_2016-->