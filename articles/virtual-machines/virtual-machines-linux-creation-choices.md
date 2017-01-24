---
title: "Les différentes façons de créer une machine virtuelle Linux | Microsoft Azure"
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
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 44c46fff9ccf9c7dba9ee380faf5f8213b58e3c3
ms.openlocfilehash: 4397d84ef4d97bdee387777a193ec0b969f2d5e1


---
# <a name="different-ways-to-create-a-linux-vm-including-azure-cli-20-preview"></a>Différentes façons de créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire)
Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux.

## <a name="azure-cli"></a>Interface de ligne de commande Azure

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- Azure CLI 1.0 : notre interface de ligne de commande pour les modèles de déploiement Classic et Resource Manager
- [Azure CLI 2.0 (version préliminaire)](../xplat-cli-install.md) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

L’interface Azure CLI 2.0 (version préliminaire) est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Vérifiez que vous êtes connecté à l’aide d’une **connexion az**.

Les didacticiels suivants fournissent des exemples d’utilisation de l’interface Azure CLI 2.0 (version préliminaire). Lisez chaque article pour en savoir plus sur les commandes indiquées :

* [Créer une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire)](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Cet exemple crée un groupe de ressources appelé myResourceGroup : 
    
    ```azurecli
    az group create -n myResourceGroup -l westus
    ```

  * Cet exemple crée une machine virtuelle dans le nouveau groupe de ressources à l’aide de la dernière image Debian avec une clé publique nommée `id_rsa.pub` :

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
    --admin-username ops \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --public-ip-address-dns-name mydns \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant crée une machine virtuelle à l’aide d’un modèle stocké sur GitHub :
    
    ```azurecli
    az group deployment create -g myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclut la création d’un équilibrage de charge et de plusieurs machines virtuelles dans un groupe à haute disponibilité.

* [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant ajoute un disque de 5 Go à une machine virtuelle existante nommée `myVM`:
    
    ```azurecli
    az vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
      --disk-size 5 --vhd https://myStorage.blob.core.windows.net/vhds/myDataDisk1.vhd
    ```

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Attacher un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### <a name="azure-images"></a>Images Microsoft Azure
Utilises les commandes de l’interface de ligne de commande `az vm image` pour voir ce qui est disponible par éditeur, version de distributeur et build.

Répertorier les éditeurs disponibles :

```azurecli
az vm image list-publishers -l WestUS
```

Répertorier les produits disponibles (offres) pour un éditeur donné :

```azurecli
az vm image list-offers --publisher-name Canonical -l WestUS
```

Répertorier les références disponibles (versions distributeur) d’une offre donnée :

```azurecli
az vm image list-skus --publisher-name Canonical --offer UbuntuServer -l WestUS
```

Répertorier toutes les images disponibles pour une version donnée :

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS -l WestUS
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

La commande `az vm create` dispose d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Il est souvent plus rapide d’utiliser des alias que d’avoir à spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

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
* [Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Exemples de commandes rapides pour la capture d’une machine virtuelle existante :
    
    ```azurecli
    az vm deallocate -g myResourceGroup -n myVM
    az vm generalize -g myResourceGroup -n myVM
    az vm capture -g myResourceGroup -n myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Étapes suivantes
* Créez une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), avec l’[interface de ligne de commande](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou à l’aide d’un [modèle Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Après avoir créé une machine virtuelle Linux, [ajoutez un disque de données](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)




<!--HONumber=Jan17_HO1-->


