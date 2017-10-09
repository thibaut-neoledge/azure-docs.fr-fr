---
title: "Configurer votre environnement de développement sur Mac OS X afin de travailler avec Azure Service Fabric | Microsoft Docs"
description: "Installez le runtime, le kit de développement logiciel et créez un cluster de développement local. Une fois la configuration terminée, vous serez prêt à générer des applications sur Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: saysa
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: c447a92e076bacc9b208b837493400b70cd067e1
ms.contentlocale: fr-fr
ms.lasthandoff: 09/26/2017

---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Vous pouvez générer des applications Service Fabric pour qu’elles s’exécutent sur les clusters Linux à l’aide de Mac OS X. Cet article explique comment configurer votre Mac pour le développement.

## <a name="prerequisites"></a>Composants requis
Service Fabric n’est pas exécuté en mode natif sur OS X. Pour exécuter un cluster Service Fabric local, nous fournissons une machine virtuelle Ubuntu préconfigurée à l’aide de Vagrant et VirtualBox. Avant de commencer, vous avez besoin des éléments suivants :

* [Vagrant (v1.8.4 ou version ultérieure)](http://www.vagrantup.com/downloads.html)
* [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

>[!NOTE]
> Vous devez utiliser les versions mutuellement prises en charge de Vagrant et VirtualBox. Vagrant peut avoir un comportement erratique sur une version non prise en charge de VirtualBox.
>

## <a name="create-the-local-vm"></a>Créer la machine virtuelle locale
Pour créer la machine virtuelle locale contenant un cluster Service Fabric à 5 nœuds, procédez comme suit :

1. Clonez le référentiel `Vagrantfile`.

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```
    Cette procédure permet de télécharger le fichier `Vagrantfile` contenant la configuration de la machine virtuelle, ainsi que l’emplacement à partir duquel la machine virtuelle est téléchargée.  Le fichier pointe vers une image Ubuntu stockée.

2. Accédez au clone local du référentiel.

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```
3. (Facultatif) Modifiez les paramètres de la machine virtuelle par défaut.

    Par défaut, la machine virtuelle locale est configurée comme suit :

   * 3 Go de mémoire allouée
   * Réseau hôte privé configuré sur l’IP 192.168.50.50 activant le transfert du trafic à partir de l’hôte Mac

     Vous pouvez modifier ces paramètres ou ajouter une autre configuration à la machine virtuelle dans `Vagrantfile`. Consultez la [documentation de Vagrant](http://www.vagrantup.com/docs) pour connaître la liste complète des options de configuration.
4. Création de la machine virtuelle

    ```bash
    vagrant up
    ```


5. Connectez-vous à la machine virtuelle et installez le Kit de développement logiciel (SDK) Service Fabric

    ```bash
    vagrant ssh
    ```

   Installez le Kit de développement logiciel (SDK) comme décrit dans la section relative à [l’installation du Kit de développement logiciel (SDK)](service-fabric-get-started-linux.md).  Pour des raisons pratiques, le script ci-dessous est fourni pour l’installation du runtime Service Fabric et du Kit de développement logiciel (SDK) Service Fabric courant avec l’interface de ligne de commande sfctl. L’exécution du script suppose que vous acceptez les licences de tous les logiciels en cours d’installation.

    ```bash
    sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
    ```

5.  Déploiement du cluster Service Fabric

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    >[!TIP]
    > Si le téléchargement de la machine virtuelle prend un certain temps, vous pouvez la télécharger à l’aide de wget ou de curl ou via un navigateur en accédant au lien spécifié par **config.vm.box_url** dans le fichier `Vagrantfile`. Après l’avoir téléchargée localement, modifiez `Vagrantfile` pour pointer vers le chemin d’accès local où vous avez téléchargé l’image. Par exemple, si vous avez téléchargé l’image vers /home/users/test/azureservicefabric.tp8.box, définissez **config.vm.box_url** sur ce chemin.
    >

5. Vérifiez que le cluster a été configuré correctement en accédant à Service Fabric Explorer à l’adresse http://192.168.50.50:19080/Explorer (en supposant que vous avez conservé l’adresse IP du réseau privé par défaut).

    ![Service Fabric Explorer affiché depuis le Mac hôte][sfx-mac]

## <a name="install-the-necessary-java-artifacts-on-vagrant-to-use-service-fabric-java-programming-model"></a>Installer les artefacts Java nécessaires sur Vagrant pour utiliser le modèle de programmation Java Service Fabric

Pour générer des services Service Fabric à l’aide de Java, assurez-vous que JDK 1.8 est installé avec Gradle, qui est utilisé pour exécuter des tâches de build. L’extrait de code suivant installe Open JDK 1.8, ainsi que Gradle. Les bibliothèques Java Service Fabric sont extraites à partir de Maven.

  ```bash
  vagrant ssh
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
```

## <a name="set-up-the-service-fabric-cli"></a>Configurer l’interface de ligne de commande Service Fabric

L’[interface de ligne de commande Service Fabric](service-fabric-cli.md) inclut des commandes permettant d’interagir avec des entités Service Fabric, y compris des clusters et des applications. Étant donné qu’elle est basée sur python, assurez-vous d’avoir installé python et pip avant de continuer avec la commande suivante :

```bash
pip install sfctl
```

## <a name="create-application-on-mac-using-yeoman"></a>Créer l’application sur Mac à l’aide de Yeoman
Service Fabric fournit des outils de génération de modèles automatique qui vous aideront à créer une application Service Fabric depuis un terminal à l’aide du générateur de modèles Yeoman. Suivez les étapes ci-dessous pour vous assurer que le générateur de modèles Yeoman Service Fabric est en état de fonctionnement sur votre machine.

1. Vous devez installer Node.js et NPM sur votre mac. Si ce n’est pas le cas, vous pouvez installer Node.js et NPM à l’aide de Homebrew via la commande suivante. Pour vérifier quelles sont les versions de Node.js et de NPM installées sur votre Mac, vous pouvez utiliser l’option ``-v``.

  ```bash
  brew install node
  node -v
  npm -v
  ```
2. Installer le générateur de modèles [Yeoman](http://yeoman.io/) sur votre machine à partir de NPM

  ```bash
  npm install -g yo
  ```
3. Installez le générateur Yeoman que vous souhaitez utiliser, en suivant les étapes indiquées dans la [documentation](service-fabric-get-started-linux.md) de prise en main. Pour créer des Applications Service Fabric à l’aide de Yeoman, suivez les étapes -

  ```bash
  npm install -g generator-azuresfjava       # for Service Fabric Java Applications
  npm install -g generator-azuresfguest      # for Service Fabric Guest executables
  npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
  ```
4. Pour générer une application Java Service Fabric sur Mac, vous devez installer - JDK 1.8 et Gradle sur la machine.

## <a name="set-up-net-core-20-development"></a>Configurer le développement de .NET Core 2.0

Installez le [Kit de développement logiciel (SDK) .NET Core 2.0 pour Mac](https://www.microsoft.com/net/core#macos) afin de démarrer [la création d’applications Service Fabric C#](service-fabric-create-your-first-linux-application-with-csharp.md). Les packages pour les applications .NET Core 2.0 Service Fabric sont hébergés sur NuGet.org, actuellement en préversion.


## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Installer le plug-in Service Fabric pour Eclipse Neon

Service Fabric fournit un plug-in pour **Eclipse Neon pour IDE Java** qui peut simplifier le processus de création, de génération et de déploiement des services Java. Vous pouvez suivre les étapes d’installation mentionnées dans cette [documentation](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) générale relative à l’installation ou à la mise à jour du plug-in Eclipse Service Fabric.

>[!TIP]
> Par défaut, nous prenons en charge l’adresse IP par défaut comme indiqué dans le fichier ``Vagrantfile`` du ``Local.json`` de l’application générée. En cas de modification, si vous déployez Vagrant avec une adresse IP différente, mettez également à jour l’adresse IP correspondante dans le fichier ``Local.json`` de votre application.

## <a name="next-steps"></a>Étapes suivantes
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Création d’un cluster Service Fabric dans Azure à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)
* [Utilisez l’interface de ligne de commande Service Fabric pour gérer vos applications](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

