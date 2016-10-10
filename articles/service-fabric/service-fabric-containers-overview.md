<properties
   pageTitle="Vue d’ensemble de Service Fabric et des conteneurs | Microsoft Azure"
   description="Voici une vue d’ensemble de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs. Cet article fournit une vue d’ensemble de l’utilisation de conteneurs et des fonctionnalités disponibles dans Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>

# Aperçu : Service Fabric et conteneurs 

>[AZURE.NOTE] Cette fonctionnalité est disponible en version préliminaire pour Linux. Par contre, elle n’est pas disponible actuellement sur Windows Server. Elle sera proposée en version préliminaire sur Windows Server dans la version suivante de Service Fabric, après la disponibilité générale de Windows Server 2016. La version ultérieure devrait prendre cette fonctionnalité en charge.

## Introduction
Service Fabric est un [orchestrateur](service-fabric-cluster-resource-manager-introduction.md) de services sur un cluster de machines virtuelles. Les services peuvent être développés de nombreuses façons, via des [modèles de programmation de Service Fabric](service-fabric-choose-framework.md) ou via le déploiement d’[exécutables invités](service-fabric-deploy-existing-app.md). Par défaut, Service Fabric déploie et active ces services en tant que processus. Ces processus assurent l’activation la plus rapide et offrent la densité la plus élevée en matière de ressources dans le cluster. Service Fabric peut également déployer des services dans des images de conteneur. Remarque importante : vous pouvez mélanger les services dans des processus et dans des conteneurs au sein d’une même application. Selon votre scénario, vous obtenez le meilleur des deux mondes.

## Qu’est-ce qu’un conteneur ?
Les conteneurs sont des composants encapsulés et pouvant être déployés au cas par cas, qui sont exécutés en tant qu’instances isolées sur un même noyau. Ils tirent parti de la virtualisation effectuée au niveau du système d’exploitation. Cela signifie que chaque application, son runtime, ses dépendances et ses bibliothèques système s’exécutent au sein d’un conteneur bénéficiant d’un accès privé complet à leur propre vue isolée sur les constructions du système d’exploitation. En parallèle avec la portabilité, ce degré de sécurité et d’isolement des ressources est le principal avantage associé à l’utilisation de conteneurs avec Service Fabric, qui exécute les services dans des processus, par ailleurs.

La technologie d’un conteneur virtualise le système d’exploitation sous-jacent par rapport aux applications. Les conteneurs offrent un environnement immuable permettant aux applications de s’exécuter avec un certain degré d’isolation. Les conteneurs s’exécutent directement sur le noyau et disposent d’une vue isolée sur le système de fichiers et d’autres ressources. Par rapport aux machines virtuelles, les conteneurs présentent les avantages suivants :

1.  **Une taille réduite** : les conteneurs utilisent un espace de stockage unique et des deltas plus petits pour chaque couche. Ils sont donc plus efficaces.

2.  **Démarrage rapide** : les conteneurs n’ont pas besoin d’initialiser un système d’exploitation. Ils sont donc prêts plus rapidement que les machines virtuelles (en quelques secondes, généralement).

3.  **Portabilité** : une image d’application en conteneur peut être portée de manière à s’exécuter sur le cloud ou sur site, à l’intérieur de machines virtuelles ou directement sur des machines physiques.

4.  **Gouvernance des ressources** : le nombre de ressources physiques qu’un conteneur peut consommer sur son hôte est limité.

## Types de conteneur
Service Fabric prend en charge les types de conteneur ci-après.

### Conteneurs Docker sur Linux
Docker propose des API de niveau supérieur pour créer et gérer des conteneurs sur des conteneurs de noyau Linux. Docker Hub constitue un référentiel central pour stocker et récupérer des images de conteneurs.

### Conteneurs Windows Server
Windows Server 2016 fournit deux types de conteneurs, qui proposent des niveaux d’isolation différents. Les conteneurs Windows Server sont similaires aux conteneurs Docker, en ce sens qu’ils proposent l’isolation des systèmes de fichiers et espaces de noms. Toutefois, ils partagent le noyau avec l’hôte sur lequel ils s’exécutent. Sur Linux, cette isolation est généralement fournie via des espaces de noms et cgroups. Les conteneurs Windows Server se comportent de la même manière.

Les conteneurs Windows Hyper-V proposent un niveau d’isolation et de sécurité plus élevé, car aucun conteneur ne partage le noyau du système d’exploitation avec les autres, ni avec l’hôte. Grâce à ce niveau élevé d’isolation à des fins de sécurité, les conteneurs Hyper-V ciblent particulièrement les scénarios de mutualisation hostiles.

La figure suivante illustre les différents types de virtualisation et niveaux d’isolation disponibles dans le système d’exploitation. ![Plateforme Service Fabric][Image1]

## Scénarios d’utilisation des conteneurs
Voici quelques exemples courants d’utilisation de conteneurs.

1. **Opération lift-and-shift pour IIS**. Si vous voulez continuer à utiliser certaines applications MVC ASP.NET existantes, placez-les dans un conteneur plutôt que de les migrer vers une mémoire à tores magnétiques ASP. NET. Ces applications MVC ASP.NET dépendent de la fonction IIS. Vous pouvez empaqueter ces applications dans des images de conteneur à partir d’une image IIS créée au préalable, puis les déployer avec Service Fabric. Consultez la page [Images de conteneur sur Windows Server](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) portant sur la création d’images IIS.


2. **Mélange de conteneurs et de microservices Service Fabric**. Utilisez une image de conteneur existante pour une partie de votre application. Par exemple, vous pouvez utiliser le [conteneur NGINX](https://hub.docker.com/_/nginx/) pour le système frontal web de votre application et les services avec état générés avec Reliable Services pour les calculs les plus intenses du back-end. Exemple : les applications de jeu.


3. **Réduction de l’impact des services de « voisins bruyants »**. La capacité de gouvernance des ressources des conteneurs vous permet de limiter le nombre de ressources utilisées par un service sur un hôte. S’il existe des services susceptibles de consommer un grand nombre de ressources et, de ce fait, d’affecter les performances d’autres services (opération de type requête exécutée sur le long terme, par exemple), nous vous recommandons de les placer dans des conteneurs soumis à la gouvernance des ressources.

## Prise en charge des conteneurs par Service Fabric
Actuellement, Service Fabric prend en charge le déploiement de conteneurs Docker sur Linux, et de conteneurs Windows Server sur Windows Server 2016. La prise en charge de conteneurs Hyper-V sera ajoutée dans une version ultérieure.

Dans le [modèle d’application](service-fabric-application-model.md) Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service. Les scénarios suivants sont pris en charge :

1.	**Conteneurs d’invités** : le scénario correspondant est identique au [scénario des exécutables invités](service-fabric-deploy-existing-app.md), dans lequel vous pouvez déployer des applications existantes au sein d’un conteneur. Exemples : nodejs, javascript ou tout code (fichiers EXE).


2.	**Services sans état au sein de conteneurs** : il s’agit de services sans état ayant recours aux modèles de programmation Reliable Actors et Reliable Services. Actuellement, cette fonctionnalité est uniquement prise en charge par Linux. La prise en charge des services sans état au sein de conteneurs Windows sera ajoutée ultérieurement.

 
3.	**Services avec état au sein de conteneurs** : il s’agit de services avec état ayant recours au modèle de programmation Reliable Actors sur Linux. Actuellement, les modèles Reliable Services ne sont pas pris en charge par Linux. La prise en charge des services avec état au sein de conteneurs Windows sera ajoutée ultérieurement.

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. On parle de services en conteneur. Ces fonctionnalités sont les suivantes :

- Activation et déploiement d’images de conteneur
- Gouvernance des ressources
- Authentification de référentiels
- Mappage des ports de conteneur aux ports hôtes
- Découverte et communication entre des conteneurs
- Possibilité de configurer et de définir des variables d’environnement

## Étapes suivantes
Dans cet article, vous avez appris ce qu’était un conteneur. Vous savez désormais que Service Fabric est un orchestrateur de conteneurs et vous connaissez les fonctionnalités qu’il fournit pour la prise en charge des conteneurs. Nous allons désormais parcourir ces différentes fonctionnalités, afin de vous indiquer comment les utiliser.

[Déployer un conteneur sur Service Fabric](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png

<!---HONumber=AcomDC_0928_2016-->