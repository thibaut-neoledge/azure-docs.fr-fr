---
title: "Configurer votre environnement de développement sur Mac OS X | Microsoft Docs"
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
ms.date: 04/06/2017
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e5d14eb0a656d67030f4c0d3d510aec0e9cafae7
ms.lasthandoff: 03/29/2017


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
    Cette procédure permet de désactiver le fichier `Vagrantfile` contenant la configuration de la machine virtuelle, ainsi que l’emplacement à partir duquel la machine virtuelle est téléchargée.


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

   Cette étape sert à télécharger l’image de machine virtuelle préconfigurée, à la démarrer en local, puis à configurer un cluster Service Fabric dans celle-ci. Cette opération peut prendre plusieurs minutes. Si la configuration est correcte, un message s’affiche indiquant que le cluster est en train de démarrer.

    ![La configuration du cluster démarre après l’approvisionnement de la machine virtuelle][cluster-setup-script]

>[!TIP]
> Si le téléchargement de la machine virtuelle prend un certain temps, vous pouvez la télécharger à l’aide de wget ou de curl ou via un navigateur en accédant au lien spécifié par **config.vm.box_url** dans le fichier `Vagrantfile`. Après l’avoir téléchargée localement, modifiez `Vagrantfile` pour pointer vers le chemin d’accès local où vous avez téléchargé l’image. Par exemple, si vous avez téléchargé l’image vers /home/users/test/azureservicefabric.tp8.box, définissez **config.vm.box_url** sur ce chemin.
>

5. Vérifiez que le cluster a été configuré correctement en accédant à Service Fabric Explorer à l’adresse http://192.168.50.50:19080/Explorer (en supposant que vous avez conservé l’adresse IP du réseau privé par défaut).

    ![Service Fabric Explorer affiché depuis le Mac hôte][sfx-mac]

## <a name="install-the-service-fabric-plugin-for-eclipse-neon"></a>Installer le plug-in Service Fabric pour Eclipse Neon

Service Fabric fournit un plug-in pour **Eclipse Neon pour IDE Java** qui peut simplifier le processus de création, de génération et de déploiement des services Java. Vous pouvez suivre les étapes d’installation mentionnées dans cette [documentation](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) générale relative à l’installation ou à la mise à jour du plug-in Eclipse Service Fabric.

## <a name="using-service-fabric-eclipse-plugin-on-mac"></a>Utilisation du plug-in Eclipse de Service Fabric sur Mac

Assurez-vous d’avoir effectué les étapes présentées dans la [documentation du plug-in Eclipse de Service Fabric](service-fabric-get-started-eclipse.md). Les étapes de création, de génération et de déploiement d’une application Java Service Fabric à l’aide du conteneur vagrant invité sur un hôte Mac sont pratiquement identiques à celles décrites dans la documentation générale, mis à part pour les points suivants :

* Étant donné que les bibliothèques Service Fabric sont indispensables pour la génération de votre application Java Service Fabric, le projet Eclipse doit être créé dans un chemin d’accès partagé. Par défaut, le contenu se trouvant dans le chemin d’accès sur votre ordinateur hôte qui contient ``Vagrantfile`` est partagé avec le chemin d’accès ``/vagrant`` sur l’invité.
* Si vous avez ``Vagrantfile`` dans un chemin d’accès, par exemple, ``~/home/john/allprojects/``, vous devez créer votre projet Service Fabric ``MyActor`` à l’emplacement ``~/home/john/allprojects/MyActor`` et le chemin d’accès à votre espace de travail Eclipse serait ``~/home/john/allprojects``.

## <a name="next-steps"></a>Étapes suivantes
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide de Yeoman)](service-fabric-create-your-first-linux-application-with-java.md)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Créer et déployer votre première application Java Service Fabric sur Linux à l’aide du plug-in Service Fabric pour Eclipse)](service-fabric-get-started-eclipse.md)
* [Création d’un cluster Service Fabric dans Azure à partir du portail Azure](service-fabric-cluster-creation-via-portal.md)
* [Création d’un cluster Service Fabric dans Azure à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Modéliser une application dans Service Fabric](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

