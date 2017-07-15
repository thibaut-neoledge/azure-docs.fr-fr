---
title: "Différentes façons de créer une machine virtuelle Linux avec Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez les différentes façons de créer une machine virtuelle Linux sur Azure, avec des liens vers des outils et des didacticiels pour chaque méthode."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 756141fda3148726af0030a010465ee14e8ce6b6
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# Les différentes méthodes de création d’une machine virtuelle Linux dans Azure
<a id="different-ways-to-create-a-linux-virtual-machine-in-azure" class="xliff"></a>
Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux.

## Interface de ligne de commande Azure
<a id="azure-cli" class="xliff"></a>
Vous pouvez créer des machines virtuelles dans Azure à l’aide d’une des versions suivantes de CLI :

- Azure CLI 1.0 : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](../windows/creation-choices.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

L’interface de ligne de commande Azure 1.0 est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Vous pouvez en savoir plus sur [la manière d’installer et de configurer l’interface de ligne de commande Azure](../../cli-install-nodejs.md). Les didacticiels suivants fournissent des exemples d’utilisation de l’interface de commande Azure 1.0. Lisez chaque article pour en savoir plus sur les commandes de démarrage rapide de l’interface de ligne de commande indiquées :

* [Créer une machine virtuelle Linux à partir de l’interface CLI Azure pour le développement et le test](quick-create-cli-nodejs.md)
  
  * L’exemple suivant crée une VM CoreOS à l’aide d’une clé publique nommée *azure_id_rsa.pub* :
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](create-ssh-secured-vm-from-template.md)
  
  * L’exemple suivant crée une machine virtuelle à l’aide d’un modèle stocké sur GitHub :
    
    ```azurecli
    azure group create --name myResourceGroup --location eastus 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](create-cli-complete-nodejs.md)
  
  * Inclut la création d’un équilibrage de charge et de plusieurs machines virtuelles dans un groupe à haute disponibilité.
* [Ajouter un disque à une machine virtuelle Linux](add-disk.md)
  
  * L’exemple suivant ajoute un disque de *5* Go à une machine virtuelle existante nommée *myVM* :
    
    ```azurecli
    azure vm disk attach-new \
        --resource-group myResourceGroup \
        --vm-name myVM \
        --size-in-GB 5
    ```

## Portail Azure
<a id="azure-portal" class="xliff"></a>
Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](quick-create-portal.md) 

## Système d'exploitation et choix d'images
<a id="operating-system-and-image-choices" class="xliff"></a>
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### Images Microsoft Azure
<a id="azure-images" class="xliff"></a>
Utilises les commandes de l’interface de ligne de commande `azure vm image` pour voir ce qui est disponible par éditeur, version de distributeur et build.

Procédez comme suit pour répertorier les éditeurs disponibles :

```azurecli
azure vm image list-publishers --location eastus
```

Procédez comme suit pour répertorier les produits disponibles (offres) pour un éditeur donné :

```azurecli
azure vm image list-offers --location eastus --publisher Canonical
```

Procédez comme suit pour répertorier les références disponibles (versions distributeur) d’une offre donnée :

```azurecli
azure vm image list-skus --location eastus --publisher Canonical --offer UbuntuServer
```

Procédez comme suit pour répertorier toutes les images disponibles pour une version donnée :

```azurecli
azure vm image list --location eastus --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](cli-ps-findimage.md#use-azure-cli-10).

Les commandes `azure vm quick-create` et `azure vm create` disposent d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Il est souvent plus rapide d’utiliser des alias que d’avoir à spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

| Alias | Éditeur | Offer | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |le plus récent |
| CoreOS |CoreOS |CoreOS |Stable |le plus récent |
| Debian |credativ |Debian |8 |le plus récent |
| openSUSE |SUSE |openSUSE |13.2 |le plus récent |
| RHEL |Redhat |RHEL |7,2 |le plus récent |
| SLES |SLES |SLES |12-SP1 |le plus récent |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |le plus récent |

### Utiliser votre propre image
<a id="use-your-own-image" class="xliff"></a>
Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Azure existante, en *capturant* cette machine virtuelle. Vous pouvez également télécharger une image créée sur site. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

* [Distributions prises en charge par Azure](endorsed-distros.md)
* [Informations concernant les distributions non approuvées](create-upload-generic.md)
* [Chargement et création d’une machine virtuelle Linux à partir d’une image de disque personnalisée](upload-vhd.md)
* [Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources](capture-image.md).
  
  * Exemples de commandes rapides pour la capture d’une machine virtuelle existante :
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## Étapes suivantes
<a id="next-steps" class="xliff"></a>
* Créez une machine virtuelle Linux à partir du [portail](quick-create-portal.md), avec l’[interface de ligne de commande](quick-create-cli.md) ou à l’aide d’un [modèle Azure Resource Manager](../windows/cli-deploy-templates.md).
* Après avoir créé une machine virtuelle Linux, [ajoutez un disque de données](add-disk.md).
* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](using-vmaccess-extension.md)


