---
title: "Créer une application Java Azure Service Fabric Reliable Actors sur Linux | Microsoft Docs"
description: "Découvrez comment créer et déployer une application Java Service Fabric Reliable Actors en cinq minutes."
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
ms.date: 06/29/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 9afd12380926d4e16b7384ff07d229735ca94aaa
ms.openlocfilehash: 254f38a600ea4026120bc411368eeb01310e56b2
ms.contentlocale: fr-fr
ms.lasthandoff: 07/15/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Création de votre première application Java Service Fabric Reliable Actors sur Linux

Ce guide de démarrage rapide vous aide à créer votre première application Java Azure Service Fabric dans un environnement de développement Linux en quelques minutes.  Lorsque vous aurez terminé, vous disposerez d’une simple application de service unique Java exécutée sur le cluster de développement local.  

## <a name="prerequisites"></a>Composants requis
Avant de commencer, installez le Kit de développement logiciel (SDK) et l’interface de ligne de commande Azure, puis configurez un cluster de développement dans votre [environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement de développement Linux sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

Pour déployer votre application, vous devrez également configurer [Azure CLI 2.0](service-fabric-azure-cli-2-0.md) (recommandé) ou [l’interface de ligne de commande XPlat](service-fabric-azure-cli.md).

## <a name="create-the-application"></a>Création de l'application
Une application Service Fabric contient un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre premier service et son ajout ultérieurement.  Vous pouvez également créer, générer et déployer des applications Java Service Fabric à l’aide d’un plug-in d’Eclipse. Consultez les instructions de [création et déploiement de votre première application Java à l’aide d’Eclipse](service-fabric-get-started-eclipse.md). Pour ce démarrage rapide, utilisez Yeoman pour créer une application avec un service unique qui stocke et obtient une valeur de compteur.

1. Saisissez ``yo azuresfjava`` dans un terminal.
2. Donnez un nom à votre application. 
3. Choisissez le type de votre premier service et nommez-le. Pour ce didacticiel, choisissez un service Reliable Actors. Pour plus d’informations sur les autres types de services, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).
   ![Générateur Yeoman Service Fabric pour Java][sf-yeoman]

## <a name="build-the-application"></a>Création de l'application
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal. Pour générer et placer l’application dans un package, exécutez ce qui suit :

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Déployer l’application
Une fois que l’application est générée, vous pouvez la déployer sur le cluster local.

### <a name="using-xplat-cli"></a>Utilisation de l’interface de ligne de commande XPlat

1. Connectez-vous au cluster Service Fabric local.

    ```bash
    azure servicefabric cluster connect
    ```

2. Exécutez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

### <a name="using-azure-cli-20"></a>Avec Azure CLI 2.0

L’application générée se déploie de la même manière qu’une autre application Service Fabric. Pour obtenir des instructions détaillées, consultez la documentation concernant [la gestion d’une application Service Fabric avec Azure CLI](service-fabric-application-lifecycle-azure-cli-2-0.md).

Vous pourrez retrouver les paramètres de ces commandes dans les manifestes générés au sein du package d’application.

Une fois l’application déployée, ouvrez un navigateur et accédez à [Service Fabric Explorer ](service-fabric-visualizing-your-cluster.md), à l’adresse [http://localhost:19080/Explorer](http://localhost:19080/Explorer).
Ensuite, développez le nœud **Applications** et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

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

## <a name="related-articles"></a>Articles connexes

* [Prise en main de Service Fabric et d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Getting started with Service Fabric XPlat CLI](service-fabric-azure-cli.md) (Prise en main de l’interface de ligne de commande Service Fabric XPlat)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

