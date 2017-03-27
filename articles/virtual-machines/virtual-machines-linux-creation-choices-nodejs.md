---
title: "Différentes façons de créer une machine virtuelle Linux | Microsoft Docs"
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
ms.date: 01/19/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: c3959babbfa6d7d512248e262060d44390de28a4
ms.lasthandoff: 03/21/2017


---
# <a name="different-ways-to-create-a-linux-virtual-machine-in-azure"></a>Les différentes méthodes de création d’une machine virtuelle Linux dans Azure
Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux.

## <a name="azure-cli"></a>Interface de ligne de commande Azure
Vous pouvez créer des machines virtuelles dans Azure à l’aide d’une des versions suivantes de CLI :

- Azure CLI 1.0 : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-creation-choices.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

L’interface de ligne de commande Azure 1.0 est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Vous pouvez en savoir plus sur [la manière d’installer et de configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md). Les didacticiels suivants fournissent des exemples d’utilisation de l’interface de commande Azure 1.0. Lisez chaque article pour en savoir plus sur les commandes de démarrage rapide de l’interface de ligne de commande indiquées :

* [Créer une machine virtuelle Linux à partir de l’interface CLI Azure pour le développement et le test](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant crée une VM CoreOS à l’aide d’une clé publique nommée `azure_id_rsa.pub`:
    
    ```azurecli
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
      --image-urn CoreOS
    ```
* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant crée une machine virtuelle à l’aide d’un modèle stocké sur GitHub :
    
    ```azurecli
    azure group create --name myResourceGroup --location WestUS 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```
* [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-create-cli-complete-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclut la création d’un équilibrage de charge et de plusieurs machines virtuelles dans un groupe à haute disponibilité.
* [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant ajoute un disque de 5 Go à une machine virtuelle existante nommée `TestVM`:
    
    ```azurecli
    azure vm disk attach-new --resource-group myResourceGroup  --vm-name myVM \
      --size-in-GB 5
    ```

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Attacher un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### <a name="azure-images"></a>Images Microsoft Azure
Utilises les commandes de l’interface de ligne de commande `azure vm image` pour voir ce qui est disponible par éditeur, version de distributeur et build.

Procédez comme suit pour répertorier les éditeurs disponibles :

```azurecli
azure vm image list-publishers --location WestUS
```

Procédez comme suit pour répertorier les produits disponibles (offres) pour un éditeur donné :

```azurecli
azure vm image list-offers --location WestUS --publisher Canonical
```

Procédez comme suit pour répertorier les références disponibles (versions distributeur) d’une offre donnée :

```azurecli
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Procédez comme suit pour répertorier toutes les images disponibles pour une version donnée :

```azurecli
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

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

### <a name="use-your-own-image"></a>Utiliser votre propre image
Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Azure existante, en *capturant* cette machine virtuelle. Vous pouvez également télécharger une image créée sur site. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

* [Distributions prises en charge par Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Informations concernant les distributions non approuvées](virtual-machines-linux-create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Chargement et création d’une machine virtuelle Linux à partir d’une image de disque personnalisée](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Exemples de commandes rapides pour la capture d’une machine virtuelle existante :
    
    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --vm-name myVM
    azure vm generalize --resource-group myResourceGroup --vm-name myVM
    azure vm capture --resource-group myResourceGroup --vm-name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Étapes suivantes
* Créez une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), avec l’[interface de ligne de commande](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou à l’aide d’un [modèle Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Après avoir créé une machine virtuelle Linux, [ajoutez un disque de données](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


