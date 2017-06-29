---
title: "Créer votre première application de microservices Azure sur Linux à l’aide de Java | Microsoft Docs"
description: "Créer et déployer une application Service Fabric à l’aide de Java"
services: service-fabric
documentationcenter: java
author: rwike77
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/02/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 4ffab9eb858b05d3f5894e8753fb0fd4f38c4087
ms.contentlocale: fr-fr
ms.lasthandoff: 06/03/2017


---
# <a name="create-your-first-service-fabric-java-application-on-linux"></a>Créer votre première application Azure Service Fabric sur Linux
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Ce guide de démarrage rapide vous aide à créer votre première application Java Azure Service Fabric dans un environnement de développement Linux en quelques minutes.  Lorsque vous aurez terminé, vous disposerez d’une simple application de service unique Java exécutée sur le cluster de développement local.  

## <a name="prerequisites"></a>Composants requis
Avant de commencer, installez le Kit de développement logiciel (SDK) et l’interface de ligne de commande Azure, puis configurez un cluster de développement dans votre [environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement de développement Linux sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Création de l'application
Une application Service Fabric contient un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre premier service et son ajout ultérieurement.  Vous pouvez également créer, générer et déployer des applications Java Service Fabric à l’aide d’un plug-in d’Eclipse. Consultez les instructions de [création et déploiement de votre première application Java à l’aide d’Eclipse](service-fabric-get-started-eclipse.md). Pour ce démarrage rapide, utilisez Yeoman pour créer une application avec un service unique qui stocke et obtient une valeur de compteur.

1. Saisissez ``yo azuresfjava`` dans un terminal.
2. Donnez un nom à votre application. 
3. Choisissez le type de votre premier service et nommez-le. Pour ce didacticiel, choisissez un service Reliable Actors. Pour plus d’informations sur les autres types de services, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).
   ![Générateur Yeoman Service Fabric pour Java][sf-yeoman]

## <a name="build-the-application"></a>Création de l'application
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal. Pour générer l’application et créer un package, utilisez la commande suivante :

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

2. Exécutez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

3. Ouvrez un navigateur et accédez à[ Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) à l’adresse http://localhost:19080/Explorer (remplacez localhost par l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X).

4. Développez le nœud **Applications** et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

## <a name="start-the-test-client-and-perform-a-failover"></a>Démarrer le client de test et effectuer un basculement
Les acteurs ne font rien seuls, ils ont besoin d’un autre service ou client pour leur envoyer des messages. Le modèle d’acteur inclut un script de test simple que vous pouvez utiliser pour interagir avec le service d’acteur.

1. Exécutez le script à l’aide de l’utilitaire watch pour afficher la sortie du service d’acteur.  Le script de test appelle la méthode `setCountAsync()` sur l’acteur pour incrémenter un compteur, appelle la méthode `getCountAsync()` sur l’acteur pour obtenir la nouvelle valeur du compteur et affiche cette valeur dans la console.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. Dans Service Fabric Explorer, recherchez le nœud qui héberge le réplica principal pour le service d’acteur. Dans la capture d’écran ci-dessous, il s’agit du nœud 3. Le réplica principal de service gère les opérations de lecture et d’écriture.  Les modifications d’état de service sont ensuite propagées aux réplicas secondaires, en cours d’exécution sur les nœuds 0 et 1 dans la capture d’écran ci-dessous.

    ![Recherche du réplica principal dans Service Fabric Explorer][sfx-primary]

3. Dans **Nœuds**, cliquez sur le nœud trouvé à l’étape précédente, puis sélectionnez **Désactiver (redémarrer)** dans le menu Actions. Cette action permet de redémarrer le nœud qui exécute le réplica de service principal et de forcer un basculement sur l’un des réplicas secondaires s’exécutant sur un autre nœud.  Ce réplica secondaire est promu au rang de réplica principal, un autre réplica secondaire est créé sur un autre nœud, et le réplica principal commence à effectuer des opérations de lecture/écriture. Pendant le redémarrage du nœud, prêtez attention à la sortie du client de test et notez que le compteur continue à être incrémenté malgré le basculement.

## <a name="add-another-service-to-the-application"></a>Ajouter un autre service à l’application
Pour ajouter un autre service à une application existante à l’aide de `yo`, procédez comme suit :
1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfjava:AddService`.
3. Générez et déployez l’application, comme dans les étapes précédentes.

## <a name="remove-the-application"></a>Supprimer l’application
Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance d’application, désinscrire le package d’application et le supprimer de magasin d’images du cluster.

```bash
./uninstall.sh
```

Dans Service Fabric Explorer, vous voyez que l’application et le type d’application n’apparaissent plus dans le nœud **Applications**.

## <a name="next-steps"></a>Étapes suivantes
* [Création de votre première application Java Service Fabric sur Linux à l’aide d’Eclipse](service-fabric-get-started-eclipse.md)
* [Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Azure](service-fabric-azure-cli.md)
* [Résolution des problèmes de déploiement](service-fabric-azure-cli.md#troubleshooting)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

