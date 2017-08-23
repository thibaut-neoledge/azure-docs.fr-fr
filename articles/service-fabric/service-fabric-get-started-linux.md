---
title: "Configurer votre environnement de développement sur Linux | Microsoft Docs"
description: "Installez le runtime et le Kit de développement logiciel (SDK), puis créez un cluster de développement local sur Linux. Une fois l’installation terminée, vous serez prêt à créer des applications."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: da6a8b4824d7215eb1db131680856ac04003f5aa
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---
# <a name="prepare-your-development-environment-on-linux"></a>Préparer votre environnement de développement sur Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Pour déployer et exécuter des [applications Azure Service Fabric](service-fabric-application-model.md) sur votre ordinateur de développement Linux, installez le runtime et le Kit de développement logiciel (SDK) courant. Vous pouvez également installer les Kits de développement logiciel (SDK) facultatifs pour Java et .NET Core.

## <a name="prerequisites"></a>Composants requis

Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Mise à jour des sources APT
Pour installer le Kit de développement logiciel (SDK) et le package runtime associé via l’outil apt-get command-line, vous devez d’abord mettre à jour les sources Advanced Packaging Tool (APT).

1. Ouvrez un terminal.
2. Ajoutez le référentiel Service Fabric à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Ajoutez le référentiel `dotnet` à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Ajoutez la nouvelle clé de protection de la confidentialité Gnu (GnuPG ou GPG) à votre porte-clés APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Ajoutez les clés GPG Docker officielles à votre porte-clés APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configurer le référentiel de Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Actualisez vos listes de packages selon les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Installer et configurer le Kit de développement logiciel (SDK) pour les conteneurs et les exécutables invités

Après avoir mis à jour vos sources, vous pouvez installer le Kit de développement logiciel (SDK).

1. Installez le package SDK Service Fabric, confirmez l’installation et acceptez le contrat de licence.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Les commandes suivantes automatisent la validation de la licence pour les packages Service Fabric :
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   ```
    
2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Après avoir installé le package SDK courant, vous pourrez créer des applications avec des services de conteneurs ou exécutables invités en exécutant `yo azuresfguest` ou `yo azuresfcontainer`. Vous devrez peut-être définir votre variable d’environnement $NODE_PATH à l’emplacement des modules de nœud. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Si vous utilisez l’environnement en tant que racine, vous devrez peut-être définir la variable avec la commande suivante :

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Vous pouvez ajouter ces commandes à votre fichier ~/.bashrc pour ne pas avoir à définir la variable d’environnement à chaque connexion.
>

## <a name="set-up-the-xplat-service-fabric-cli"></a>Configuration de l’interface de ligne de commande XPlat Service Fabric
[L’interface de ligne de commande XPlat][azure-xplat-cli-github] inclut des commandes permettant d’interagir avec des entités Service Fabric, y compris des clusters et des applications. Comme elle est basée sur Node.js, [assurez-vous d’avoir installé Node][install-node] avant de suivre les instructions ci-dessous :

1. Clonez le référentiel GitHub sur votre machine de développement.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Basculez dans le référentiel cloné et installez les dépendances d’interface de ligne de commande CLI à l’aide de Node Package Manager (NPM).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Créez un lien symbolique du dossier `bin/azure` du référentiel cloné vers `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Enfin, activez les commandes Service Fabric de saisie semi-automatique.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

### <a name="set-up-azure-cli-20"></a>Configuration de l’interface de ligne de commande Azure CLI 2.0

Comme alternative à l’interface de ligne de commande XPlat CLI, vous pouvez désormais utiliser un module de commandes Service Fabric inclus dans Azure CLI.

Pour plus d’informations sur l’installation d’Azure CLI 2.0 et de l’utilisation des commandes de Service Fabric, consultez [Prise en main de Service Fabric et Azure CLI 2.0](service-fabric-azure-cli-2-0.md).

## <a name="set-up-a-local-cluster"></a>Configurer un cluster local
Une fois l’installation correctement terminée, vous pouvez démarrer un cluster local.

1. Exécutez le script de configuration du cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Ouvrez un navigateur web et accédez à [Service Fabric Explorer](http://localhost:19080/Explorer). Si le cluster a démarré, vous devez voir le tableau de bord Service Fabric Explorer.

    ![Service Fabric Explorer sur Linux][sfx-linux]

À ce stade, vous pouvez déployer des packages d’application Service Fabric préconfigurés ou de nouveaux packages basés sur des conteneurs invités ou des exécutables invités. Pour générer de nouveaux services à l’aide des Kits de développement logiciel (SDK) .NET Core ou Java, suivez les étapes de configuration facultatives ci-dessous.


> [!NOTE]
> Les clusters autonomes ne sont pas pris en charge sous Linux. La préversion prend uniquement en charge les clusters à boîtier unique et Azure Linux sur plusieurs machines.
>

## <a name="install-the-java-sdk-optional-if-you-want-to-use-the-java-programming-models"></a>Installation du Kit de développement logiciel (SDK) Java (facultatif, si vous souhaitez utiliser les modèles de programmation Java)
Le Kit de développement logiciel (SDK) Java fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de Java.

1. Installez le package du Kit de développement logiciel (SDK) Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Installation du plug-in Eclipse Neon (facultatif)

Vous pouvez installer le plug-in Eclipse pour Service Fabric à partir de **l’IDE Eclipse pour les développeurs Java**. Vous pouvez utiliser Eclipse pour créer des applications exécutables invités Service Fabric et des applications de conteneur en plus des applications Service Fabric Java.

> [!NOTE]
> Le Kit de développement logiciel (SDK) Java est un prérequis pour l’utilisation du plug-in Eclipse, même si vous l’utilisez uniquement pour des exécutables invités et des applications conteneur.
>

1. Dans Eclipse, assurez-vous de disposer de la dernière version d’Eclipse Neon et de Buildship (version 1.0.17 ou ultérieure). Vous pouvez vérifier les versions des composants installés en sélectionnant **Aide** > **Détails de l’installation**. Vous pouvez mettre à jour Buildship en utilisant les instructions [Eclipse Buildship : plug-ins Eclipse pour Gradle][buildship-update].

2. Pour installer le plug-in Service Fabric, sélectionnez **Aide** > **Installer un nouveau logiciel**.

3. Dans la zone **Utiliser avec**, entrez : **http://dl.microsoft.com/eclipse**.

4. Cliquez sur **Add**.

    ![La page Logiciels disponibles][sf-eclipse-plugin]

5. Sélectionnez le plug-in **Service Fabric**, puis cliquez sur **Suivant**.

6. Terminez les étapes d’installation, puis acceptez les termes du contrat de licence utilisateur final.

Si le plug-in Eclipse Service Fabric est déjà installé, assurez-vous de disposer de la dernière version. Vous pouvez vérifier en sélectionnant **Aide** > **Détails d’installation** et en recherchant Service Fabric dans la liste des plug-ins installés. Si une version plus récente est disponible, sélectionnez **Mettre à jour**.

Pour plus d’informations, consultez la section [Plug-in Service Fabric pour le développement d’applications Java sous Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Installation du Kit de développement logiciel (SDK) .NET Core (facultatif, si vous souhaitez utiliser les modèles de programmation .NET Core)
Le Kit de développement logiciel (SDK) .NET Core fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de .NET Core.

1. Installez le package du Kit de développement (SDK) .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="update-the-sdk-and-runtime"></a>Mise à jour du kit de développement logiciel (SDK) et du runtime

Pour passer à la dernière version du Kit de développement logiciel (SDK) et du runtime, exécutez les commandes suivantes (désélectionnez les Kits inutiles) :

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> La mise à jour des packages peut entraîner l’arrêt de votre cluster de développement local. Après la mise à niveau, redémarrez votre cluster local en suivant les instructions présentes sur cette page.

## <a name="next-steps"></a>Étapes suivantes
* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide de Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Créer et déployer votre première application Java Service Fabric sous Linux à l’aide du plug-in Service Fabric pour Eclipse](service-fabric-get-started-eclipse.md)
* [Create your first Java application on Linux (Créer votre première application Java sur Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Use the XPlat CLI to manage your Service Fabric applications](service-fabric-azure-cli.md) (Utiliser l’interface de ligne de commande XPlat pour gérer des applications Service Fabric)
* [Différences entre Service Fabric Windows/Linux](service-fabric-linux-windows-differences.md)

## <a name="related-articles"></a>Articles connexes

* [Prise en main de Service Fabric et d’Azure CLI 2.0](service-fabric-azure-cli-2-0.md)
* [Prise en main de l’interface de ligne de commande Service Fabric XPlat](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

