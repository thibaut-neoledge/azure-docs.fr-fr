---
title: "Créer une application Azure Service Fabric Reliable Actors Java sur Linux | Microsoft Docs"
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
ms.date: 09/20/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: 68f9492231d367b1ede6ab032ec1c66c75150957
ms.contentlocale: fr-fr
ms.lasthandoff: 09/21/2017

---
# <a name="create-your-first-java-service-fabric-reliable-actors-application-on-linux"></a>Création de votre première application Java Service Fabric Reliable Actors
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Ce guide de démarrage rapide vous aide à créer votre première application Azure Service Fabric Java dans un environnement de développement Linux en quelques minutes.  Lorsque vous aurez terminé, vous disposerez d’une simple application de service unique Java exécutée sur le cluster de développement local.  

## <a name="prerequisites"></a>Composants requis
Avant de commencer, installez le Kit de développement logiciel (SDK), l’interface de ligne de commande Service Fabric, puis configurez un cluster de développement dans votre [environnement de développement Linux](service-fabric-get-started-linux.md). Si vous utilisez Mac OS X, vous pouvez [configurer un environnement de développement Linux sur une machine virtuelle à l’aide de Vagrant](service-fabric-get-started-mac.md).

Il vous sera également utile d’installer l’[interface de ligne de commande Service Fabric](service-fabric-cli.md).

### <a name="install-and-set-up-the-generators-for-java"></a>Installer et configurer les générateurs de Java
Service Fabric fournit des outils de génération de modèles automatique qui vous aideront à créer une application Java Service Fabric à partir d’un terminal à l’aide du générateur de modèle Yeoman. Suivez les étapes ci-dessous pour vous assurer que le générateur de modèle yeoman Service Fabric de Java est en état de fonctionnement sur votre machine.
1. Installer nodejs et NPM sur votre machine

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Installer le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM

  ```bash
  sudo npm install -g yo
  ```
3. Installer le générateur d’applications Service Fabric Yeo Java à partir de NPM

  ```bash
  sudo npm install -g generator-azuresfjava
  ```

## <a name="create-the-application"></a>Création de l’application
Une application Service Fabric contient un ou plusieurs services, chacun ayant un rôle précis pour la fourniture de la fonctionnalité d’application. Le générateur que vous avez installé lors de la dernière section facilite la création de votre premier service et vous aide à ajouter de nouveaux services ultérieurement.  Vous pouvez également créer, générer et déployer des applications Java Service Fabric à l’aide d’un plug-in d’Eclipse. Consultez les instructions de [création et déploiement de votre première application Java à l’aide d’Eclipse](service-fabric-get-started-eclipse.md). Pour ce démarrage rapide, utilisez Yeoman pour créer une application avec un service unique qui stocke et obtient une valeur de compteur.

1. Saisissez ``yo azuresfjava`` dans un terminal.
2. Donnez un nom à votre application.
3. Choisissez le type de votre premier service et nommez-le. Pour ce didacticiel, choisissez un service Reliable Actors. Pour plus d’informations sur les autres types de services, voir [Vue d’ensemble des modèles de programmation Service Fabric](service-fabric-choose-framework.md).
   ![Générateur Yeoman Service Fabric pour Java][sf-yeoman]

## <a name="build-the-application"></a>Création de l'application
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal.
Les dépendances Java Service Fabric ont été extraites de Maven. Pour générer et travailler sur les applications Java Service Fabric, assurez-vous d’avoir installé JDK et Gradle. Dans le cas contraire, exécutez les commandes suivantes pour installer JDK(openjdk-8-jdk) et Gradle -

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

Pour générer et placer l’application dans un package, exécutez ce qui suit :

  ```bash
  cd myapp
  gradle
  ```

## <a name="deploy-the-application"></a>Déployer l’application
Une fois que l’application est générée, vous pouvez la déployer sur le cluster local.

1. Connectez-vous au cluster Service Fabric local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Exécutez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

    ```bash
    ./install.sh
    ```

L’application générée se déploie de la même manière qu’une autre application Service Fabric. Consultez la documentation sur [la gestion d’une application Service Fabric avec l’interface de ligne de commande Service Fabric](service-fabric-application-lifecycle-sfctl.md) pour obtenir des instructions détaillées.

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

## <a name="service-fabric-java-libraries-on-maven"></a>Bibliothèques Java Service Fabric sur Maven
Les bibliothèques Java Service Fabric ont été hébergées dans Maven. Vous pouvez ajouter les dépendances dans les éléments ``pom.xml`` ou ``build.gradle`` de vos projets pour utiliser les bibliothèques Java Service Fabric à partir du référentiel **mavenCentral**. 

### <a name="actors"></a>Acteurs

Assistance Reliable Actor de Service Fabric pour votre application.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-actors-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-actors-preview:0.12.0'
  }
  ```

### <a name="services"></a>Services

Assistance Reliable Services de Service Fabric pour votre application.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-services-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-services-preview:0.12.0'
  }
  ```

### <a name="others"></a>Autres
#### <a name="transport"></a>Transport

Assistance de la couche transport pour application Java Service Fabric. Il est inutile d’ajouter explicitement cette dépendance à votre service Reliable Actor ou à vos Applications de service, à moins d’effectuer la programmation au niveau de la couche transport.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-transport-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-transport-preview:0.12.0'
  }
  ```

#### <a name="fabric-support"></a>Assista,ce Fabric

Assistance du niveau système pour Service Fabric, qui communique avec le runtime Service Fabric. Vous n’avez pas besoin d’ajouter explicitement cette dépendance à vos applications Reliable Actor ou Service Fabric. Elle est extraite automatiquement depuis Maven, lorsque vous incluez les autres dépendances ci-dessus.

  ```XML
  <dependency>
      <groupId>com.microsoft.servicefabric</groupId>
      <artifactId>sf-preview</artifactId>
      <version>0.12.0</version>
  </dependency>
  ```

  ```gradle
  repositories {
      mavenCentral()
  }
  dependencies {
      compile 'com.microsoft.servicefabric:sf-preview:0.12.0'
  }
  ```

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migration d’anciennes applications Java Service Fabric à utiliser avec Maven
Nous avons récemment déplacé les bibliothèques Java Service Fabric vers un référentiel Maven depuis le Kit de développement logiciel (SDK) Java Service Fabric. Tandis que les nouvelles applications que vous générez à l’aide de Yeonman ou d’Eclipse généreront les derniers projets mis à jour (en mesure de fonctionner avec Maven), vous pouvez mettre à jour vos services sans état Service Fabric existants ou vos applications Java Actor existantes ayant utilisé le Kit de développement logiciel (SDK) précédemment, afin d’utiliser les dépendances Java Service Fabric à partir de Maven. Suivez les étapes mentionnées [ici](service-fabric-migrate-old-javaapp-to-use-maven.md) pour vous assurer qu’une version plus ancienne de votre application fonctionne avec Maven.

## <a name="next-steps"></a>Étapes suivantes

* [Création de votre première application Java Service Fabric sur Linux à l’aide d’Eclipse](service-fabric-get-started-eclipse.md)
* [Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)
* [Interagir avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Service Fabric](service-fabric-cli.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)
* [Bien démarrer avec l’interface de ligne de commande Service Fabric](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-yeoman.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-java/sfx-primary.png
[sf-eclipse-templates]: ./media/service-fabric-create-your-first-linux-application-with-java/sf-eclipse-templates.png

