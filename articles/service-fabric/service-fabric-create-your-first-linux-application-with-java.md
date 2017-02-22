---
title: "Créer votre première application de microservices Azure sur Linux à l’aide de Java | Microsoft Docs"
description: "Créer et déployer une application Service Fabric à l’aide de Java"
services: service-fabric
documentationcenter: java
author: seanmck
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: 7033955fa9c18b2fa1a28d488ad5268d598de287
ms.openlocfilehash: dc9234760b0dfb5d109fc86ac47a89c8fcf7d991


---
# <a name="create-your-first-azure-service-fabric-application"></a>Créer votre première application Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric fournit des Kits de développement logiciel (SDK) pour générer des services Linux dans .NET Core et Java. Dans ce didacticiel, nous créons une application pour Linux et générer un service à l’aide de Java.  

> [!NOTE]
> Java, en tant que langage de programmation de première classe intégré, est uniquement pris en charge avec la version préliminaire de Linux (la prise en charge avec Windows est prévue). Toutefois, toutes les applications, y compris les applications Java, peuvent être exécutées en tant qu’exécutables invités ou dans des conteneurs sur Windows ou Linux. Pour plus d’informations, consultez [Deploy an existing executable to Azure Service Fabric (Déploiement d’un exécutable existant dans Azure Service Fabric)](service-fabric-deploy-existing-app.md) et [Deploy containers to Service Fabric (Déployer des conteneurs dans Service Fabric)](service-fabric-deploy-container.md).
>

## <a name="video-tutorial"></a>Didacticiel vidéo

La vidéo de la Microsoft Virtual Academy suivante vous guide dans le processus de création d’une application Java sur Linux :  
<center><a target="\_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-create-your-first-linux-application-with-java/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>


## <a name="prerequisites"></a>Composants requis
Avant de commencer, assurez-vous que vous avez bien [configuré votre environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement Linux à boîtier unique sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Création de l'application
Une application Service Fabric peut contenir un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre premier service et son ajout ultérieurement. Nous utilisons Yeoman pour créer une application avec un seul service.

1. Saisissez ``yo azuresfjava`` dans un terminal.
2. Donnez un nom à votre application.
3. Choisissez le type de votre premier service et nommez-le. Pour les besoins de ce didacticiel, nous choisissons un service Reliable Actors.

   ![Générateur Yeoman Service Fabric pour Java][sf-yeoman]

> [!NOTE]
> Pour plus d’informations sur les options, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).
>

## <a name="build-the-application"></a>Création de l'application
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal.

  ```bash
  cd myapp
  gradle
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Démarrer le client de test et effectuer un basculement
Les projets d’acteur n’effectuent aucune opération automatiquement. Ils ont besoin d’un autre service ou client pour leur envoyer des messages. Le modèle d’acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service d’acteur.

1. Exécutez le script à l’aide de l’utilitaire watch pour afficher la sortie du service d’acteur.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Dans Service Fabric Explorer, recherchez le nœud qui héberge le réplica principal pour le service d’acteur. Dans la capture d’écran ci-dessous, il s’agit du nœud 3.

    ![Recherche du réplica principal dans Service Fabric Explorer][sfx-primary]

3. Cliquez sur le nœud trouvé à l’étape précédente, puis sélectionnez **Désactiver (redémarrer)** à partir du menu Actions. Cette action permet de redémarrer l’un des cinq nœuds de votre cluster local et de forcer un basculement sur l’un des réplicas secondaires s’exécutant sur un autre nœud. Dans le même temps, prêtez attention à la sortie du client de test et notez que le compteur continue à être incrémenté malgré le basculement.

## <a name="build-and-deploy-an-application-with-the-eclipse-neon-plugin"></a>Générer et déployer une application avec le plug-in Eclipse Neon

Si vous avez installé le plug-in [Service Fabric](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-linux#install-the-java-sdk-and-eclipse-neon-plugin-optional) pour Eclipse Neon, vous pouvez l’utiliser pour créer, générer et déployer des applications Service Fabric générées avec Java.  Lors de l’installation d’Eclipse, choisissez le **Environnement de développement intégré (IDE) Eclipse pour développeurs Java EE**.

### <a name="create-the-application"></a>Création de l'application

Le plug-in Service Fabric est disponible via l’extensibilité Eclipse.

1. Dans Eclipse, choisissez **Fichier > Autres > Service Fabric**. Un ensemble d’options s’affiche, notamment Acteurs et Conteneurs.

    ![Modèles Service Fabric dans Eclipse][sf-eclipse-templates]

2. Ici, choisissez Service sans état.

3. Vous êtes invité à confirmer l’utilisation de la perspective Service Fabric, qui optimise Eclipse pour une utilisation avec les projets Service Fabric. Choisissez « Oui ».

### <a name="deploy-the-application"></a>Déployer l’application
Les modèles Service Fabric comprennent un ensemble de tâches Gradle pour générer et déployer des applications, que vous pouvez déclencher via Eclipse.

1. Choisissez **Exécuter > Run Configurations (Exécuter des configurations)**.
2. Spécifiez **local** ou **cloud**. La valeur par défaut est **local**. Pour déployer sur un cluster à distance, sélectionnez **cloud**.
3. Vérifiez que les informations appropriées sont renseignées dans les profils de publication, en modifiant `local.json` ou `cloud.json` si nécessaire.
4. Cliquez sur **Exécuter**.

Votre application est générée et déployée en quelques instants. Vous pouvez surveiller son état depuis Service Fabric Explorer.

## <a name="adding-more-services-to-an-existing-application"></a>Ajout d’autres services à une application existante

Pour ajouter un autre service à une application déjà créée à l’aide de `yo`, procédez comme suit :
1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfjava:AddService`.


## <a name="next-steps"></a>Étapes suivantes
* [Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Azure](service-fabric-azure-cli.md)
* [Résolution des problèmes de déploiement](service-fabric-azure-cli.md#troubleshooting)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png



<!--HONumber=Jan17_HO4-->


