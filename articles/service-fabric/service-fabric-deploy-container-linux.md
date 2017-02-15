---
title: "Service Fabric et déploiement de conteneurs sous Linux | Microsoft Docs"
description: "Présentation de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs Docker. Cet article décrit les fonctionnalités que Service Fabric offre pour les conteneurs et explique comment déployer une image de conteneur Docker dans un cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Déployer un conteneur Docker sur Service Fabric
> [!div class="op_single_selector"]
> * [Déployer un conteneur Windows](service-fabric-deploy-container.md)
> * [Déployer un conteneur Docker](service-fabric-deploy-container-linux.md)
>
>

Cet article vous guide dans le processus de création des services en conteneur sous Linux.

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. On parle de services en conteneur.

Ces fonctionnalités sont les suivantes :

* Activation et déploiement d’images de conteneur
* Gouvernance des ressources
* Authentification de référentiels
* Mappage des ports de conteneur aux ports hôtes
* Découverte et communication entre des conteneurs
* Possibilité de configurer et de définir des variables d’environnement

## <a name="packaging-a-docker-container-with-yeoman"></a>Empaquetage d’un conteneur Docker avec Yeoman
Lors de l’empaquetage d’un conteneur sous Linux, vous pouvez choisir d’utiliser un modèle Yeoman ou de [créer le package d’application manuellement](service-fabric-deploy-container.md#manually).

Une application Service Fabric peut contenir un ou plusieurs conteneurs, chacun ayant un rôle précis dans la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre application et l’ajout d’une image de conteneur. Nous allons utiliser Yeoman pour créer une application avec un seul conteneur Docker appelé *SimpleContainerApp*. Vous pourrez ajouter d’autres services ultérieurement en modifiant les fichiers du manifeste.

## <a name="create-the-application"></a>Création de l'application
1. Dans un terminal, tapez **yo azuresfguest**.
2. Choisissez l’infrastructure **Conteneur** .
3. Nommez votre application, par exemple SimpleContainerApp.
4. Fournissez l’URL de l’image de conteneur à partir d’un référentiel DockerHub. Elle est sous la forme [référentiel]/[nom de l’image]

![Générateur Yeoman Service Fabric pour les conteneurs][sf-yeoman]

## <a name="deploy-the-application"></a>Déployer l’application
Une fois que l’application est générée, vous pouvez la déployer vers le cluster local à l’aide de l’interface de ligne de commande Azure.

1. Connectez-vous au cluster Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```
2. Utilisez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```
3. Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://localhost:19080/Explorer (remplacez localhost par l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X).
4. Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.
5. Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance de l’application et annuler l’inscription du type d’application.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Vue d’ensemble de Service Fabric et des conteneurs](service-fabric-containers-overview.md)
* [Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


