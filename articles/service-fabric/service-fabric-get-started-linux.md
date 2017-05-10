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
ms.date: 05/04/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: d01e141ec8ee8da18d38a216f3b13c88f3632801
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017


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

### <a name="supported-operating-system-versions"></a>Versions du système d’exploitation prises en charge
Les versions de système d’exploitation prises en charge pour le développement sont les suivantes :

* Ubuntu 16.04 (i**« Xenial Xerus »**)

## <a name="update-your-apt-sources"></a>Mettre à jour vos sources apt
Pour installer le Kit de développement logiciel (SDK) et le package runtime associé via apt-get, vous devez tout d’abord mettre à jour vos sources apt.

1. Ouvrez un terminal.
2. Ajoutez le référentiel Service Fabric à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```
3. Ajoutez le référentiel **dotnet** à votre liste de sources.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```
4. Ajoutez la nouvelle clé GPG à votre porte-clés apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```
    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Actualisez vos listes de packages selon les référentiels nouvellement ajoutés.

    ```bash
    sudo apt-get update
    ```
## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Installer et configurer le Kit de développement logiciel (SDK) pour les conteneurs et les exécutables invités
Une fois vos sources mises à jour, vous pouvez installer le Kit de développement logiciel (SDK).

1. Installez le package de Kit de développement logiciel (SDK) Service Fabric. Vous devez confirmer l’installation et accepter un contrat de licence.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```
    Pour automatiser l’installation, vous pouvez ignorer l’invite de contrat de licence en définissant vos sélections debconf pour les packages Service Fabric. Les deux commandes suivantes peuvent être exécutées
    
    ```bash
    echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Une fois que vous avez suivi les étapes d’installation du package de Kit de développement logiciel (SDK) courant, vous pouvez créer des applications avec un exécutable invité ou avec les services de conteneur en exécutant `yo azuresfguest`. Vous devrez peut-être définir votre variable d’environnement **$NODE_PATH** dans laquelle se trouvent les modules Node. 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

Si vous utilisez l’environnement en tant que racine, vous devrez peut-être définir la variable avec la commande suivante :

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> Vous pouvez ajouter ces commandes à votre fichier ~/.bashrc afin de ne pas avoir à définir la variable d’environnement à chaque connexion.
>

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurer l’interface de ligne de commande interplateforme Azure
[L’interface de ligne de commande interplateforme Azure][azure-xplat-cli-github] inclut des commandes permettant d’interagir avec des entités Service Fabric, y compris des clusters et des applications. Comme elle est basée sur Node.js, [assurez-vous que vous avez installé Node][install-node] avant de suivre les instructions ci-dessous :

1. Clonez le référentiel Github sur votre ordinateur de développement.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```
2. Basculez dans le référentiel cloné et installez les dépendances d’interface de ligne de commande à l’aide de Node Package Manager (NPM).

    ```bash
    cd azure-xplat-cli
    npm install
    ```
3. Créez un lien symbolique à partir du dossier bin/azure du référentiel cloné vers /usr/bin/azure, afin qu’il soit ajouté à votre chemin d’accès et que les commandes soient disponibles à partir de n’importe quel répertoire.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```
4. Enfin, activez les commandes Service Fabric de saisie semi-automatique.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Les commandes Service Fabric ne sont pas encore disponibles dans Azure CLI 2.0.


## <a name="set-up-a-local-cluster"></a>Configurer un cluster local
Si tout a été correctement installé, vous pourrez démarrer un cluster local.

1. Exécutez le script de configuration du cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
2. Ouvrez un navigateur web et accédez à l’adresse http://localhost:19080/Explorer. Si le cluster a démarré, vous devez voir le tableau de bord Service Fabric Explorer.

    ![Service Fabric Explorer sur Linux][sfx-linux]

À ce stade, vous êtes en mesure de déployer des packages d’application Service Fabric préconfigurés ou nouveaux basés sur des conteneurs invités ou des exécutables invités. Pour générer de nouveaux services à l’aide des Kits de développement logiciel (SDK) .Net Core ou Java, suivez les étapes de configuration facultatives ci-dessous.


> [!NOTE]
> Les clusters autonomes ne sont pas pris en charge sous Linux. La version préliminaire ne prend en charge que les clusters composés de plusieurs machines Linux Azure et d’un boîtier.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Installer le Kit de développement logiciel (SDK) Java (facultatif, si vous souhaitez utiliser Java pour les modèles de programmation)
Le Kit de développement logiciel (SDK) Java fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de Java.

1. Installez le package du Kit de développement logiciel (SDK) Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```
2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```
## <a name="install-the-eclipse-neon-plugin-optional"></a>Installer le plug-in Eclipse Neon (facultatif)

Vous pouvez installer le plug-in Eclipse pour Service Fabric à partir de **l’IDE Eclipse pour les développeurs Java**. Vous pouvez utiliser Eclipse pour créer des applications exécutables invités Service Fabric et des applications de conteneur en plus des applications Service Fabric Java.

> [!NOTE]
> L’installation du Kit de développement logiciel (SDK) Java est requise pour utiliser le plug-in Eclipse, même si vous l’utilisez uniquement pour créer et déployer des exécutables invités et des applications de conteneur.
>

1. Dans Eclipse, assurez-vous que vous disposez de la dernière version d’Eclipse **Neon** et de Buildship (version 1.0.17 ou ultérieure). Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’installation**. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update].
2. Pour installer le plug-in Service Fabric, choisissez **Aide > Installer un nouveau logiciel...**
3. Dans la zone de texte « Utiliser », entrez : http://dl.windowsazure.com/eclipse/servicefabric
4. Cliquez sur Ajouter.
    ![Plug-in Eclipse][sf-eclipse-plugin]
5. Choisissez le plug-in Service Fabric et cliquez sur Suivant.
6. Suivez les étapes d’installation et acceptez le contrat de licence d’utilisateur final.

Si le plug-in Eclipse de Service Fabric est déjà installé, assurez-vous que vous disposez de la dernière version. Vous pouvez vérifier en sélectionnant ``Help => Installation Details`` et en recherchant Service Fabric dans la liste des plug-ins installés. Sélectionnez la mise à jour si une version plus récente est disponible. 

Pour plus d’informations, voir [Service Fabric : prise en main avec Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Installer le Kit de développement logiciel (SDK) .NET Core (facultatif, si vous souhaitez utiliser les modèles de programmation .NET Core)
Le Kit de développement logiciel (SDK) .NET Core fournit les bibliothèques et les modèles nécessaires pour générer des services Service Fabric à l’aide de .Net Core interplateforme.

1. Installez le package du Kit de développement (SDK) .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Exécutez le script de configuration du Kit de développement logiciel (SDK).

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Mise à jour du kit de développement logiciel et du runtime

Pour mettre à jour vers la dernière version du kit de développement logiciel et du runtime, exécutez les étapes suivantes (supprimez de la liste les kits de développement logiciel que vous ne souhaitez pas mettre à jour ou installer) :

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```
   
> [!NOTE]
> La mise à jour des packages ci-dessus peut entraîner l’arrêt de votre cluster de développement local. Redémarrez votre cluster local après une mise à niveau en suivant les instructions de cette page
>
>

Pour mettre à jour l’interface CLI, accédez au répertoire où vous avez cloné le CLI et exécutez `git pull` pour mettre à jour.  Si des étapes supplémentaires sont nécessaires pour la mise à jour, elles seront spécifiées dans les notes de publication. 


## <a name="next-steps"></a>Étapes suivantes
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Create your first Java application on Linux (Créer votre première application Java sur Linux)](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Prepare your development environment on OSX (Préparer votre environnement de développement sur OSX)](service-fabric-get-started-mac.md)
* [Use the Azure CLI to manage your Service Fabric applications (Utiliser l’interface Azure CLI pour gérer vos applications Service Fabric)](service-fabric-azure-cli.md)
* [Différences entre Service Fabric Windows/Linux](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

