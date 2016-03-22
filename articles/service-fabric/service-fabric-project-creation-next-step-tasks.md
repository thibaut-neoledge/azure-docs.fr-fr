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
   ms.date="03/08/2016"
   ms.author="seanmck"/>

# Votre application Service Fabric et étapes suivantes
Votre application Azure Service Fabric a été créée. Cet article décrit la constitution de votre projet et certaines étapes futures potentielles.

## Votre application.
Chaque nouvelle application inclut un projet d'application. Il peut y avoir un ou deux projets supplémentaires en fonction du type de service choisi.

### Le projet d'application
Le projet d'application se compose des éléments suivants :

- Un ensemble de références aux services qui composent votre application.

- Deux profils de publication (local et cloud) que vous pouvez utiliser pour préserver les préférences d’utilisation d’environnements différents, comme les préférences liées à un point de terminaison de cluster et si vous souhaitez exécuter les déploiements de mise à niveau par défaut.

- Deux fichiers (local et cloud) de paramètres de l'application que vous pouvez utiliser pour gérer les configurations de l'application spécifiques à l'environnement, comme le nombre de partitions à créer pour un service.

- Un script de déploiement que vous pouvez utiliser pour déployer votre application à partir de la ligne de commande ou dans le cadre d’un pipeline de déploiement et d’intégration continue automatisée.

- Le manifeste d'application qui décrit l'application. Le manifeste se trouve dans le dossier ApplicationPackageRoot.

### Reliable Services
Quand vous ajoutez un nouveau service fiable (Reliable Service), Visual Studio ajoute un projet de service à votre solution. Le projet de service contient une classe étend `StatelessService` ou `StatefulService`, selon le type choisi.

### Reliable Actors
Quand vous ajoutez un nouvel acteur fiable (Reliable Actor), Visual Studio ajoute deux projets à votre solution : un projet d’acteur et un projet d’interface.

Le projet d'acteur définit le type d'acteur et (pour les acteurs avec état) son état. Le projet d'interface fournit une interface que les autres services peuvent utiliser pour appeler l'acteur.

Notez que les projets d’acteur ne contiennent pas de comportement de démarrage par défaut, car les acteurs doivent être activés par d’autres services. Envisagez d’ajouter un service fiable ou un projet ASP.NET pour créer vos acteurs et interagir avec eux.

### ASP.NET 5
Les modèles ASP.NET 5 destinés à être utilisés dans les applications Service Fabric sont presque identiques à ceux disponibles pour les projets ASP.NET 5 créés indépendamment. Les seules différences sont les suivantes :

- Le projet contient un dossier **PackageRoot** pour stocker le fichier ServiceManifest, ainsi que des packages de données et de configuration.

- Le projet fait référence à un package NuGet supplémentaire (Microsoft.ServiceFabric.AspNet.Hosting), qui agit comme une passerelle entre .NET Execution Environment (DNX) et Service Fabric.

## Étapes suivantes
### Ajouter un serveur web frontal à votre application
Service Fabric offre une intégration avec ASP.NET 5 pour la création de points d'entrée web pour votre application. Pour apprendre à créer une interface REST basée sur l’API web ASP.NET, consultez [Ajout d’un serveur web frontal à votre application][add-web-frontend].

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

<!---------HONumber=AcomDC_0309_2016-->