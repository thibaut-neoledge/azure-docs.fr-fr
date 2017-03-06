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
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: d4cff286de1abd492ce7276c300b50d71f06345b
ms.openlocfilehash: 1287a028122080c0d9745502a4a98a957894a0de
ms.lasthandoff: 02/27/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Différentes méthodes pour créer une machine virtuelle Linux
Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux, y compris avec Azure CLI 2.0. Vous trouverez également les options de création à l’aide [d’Azure CLI 1.0](virtual-machines-linux-creation-choices-nodejs.md).

[L’interface Azure CLI 2.0](/cli/azure/install-az-cli2) est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Installer la version la plus appropriée pour votre environnement et connectez-vous à un compte Azure à l’aide de [az login](/cli/azure/#login)

Les exemples suivants utilisent Azure CLI 2.0. Lisez chaque article pour en savoir plus sur les commandes indiquées. Vous trouverez également des exemples sur les options de création de Linux à l’aide de la [CLI Azure 1.0](virtual-machines-linux-creation-choices-nodejs.md).

* [Création d'une machine virtuelle Linux à l’aide d’Aide CLI 2.0](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Cet exemple utilise [az group create](/cli/azure/group#create) pour créer un groupe de ressources nommé `myResourceGroup` : 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Cet exemple utilise [az vm create](/cli/azure/vm#create) pour créer une machine virtuelle nommée `myVM` à l’aide de la dernière image Debian avec des disques gérés Azure et une clé publique nommée `id_rsa.pub` :

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Si vous souhaitez utiliser des disques non gérés, ajoutez l’indicateur `--use-unmanaged-disks` à la commande ci-dessus. Un compte de stockage est créé pour vous. Pour plus d’informations, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md).

* [Create a secured Linux VM using an Azure template (Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure)](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant utilise [az group deployment create](/cli/azure/group/deployment#create) pour créer une machine virtuelle à l’aide d’un modèle stocké sur GitHub :
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Inclut la création d’un équilibrage de charge et de plusieurs machines virtuelles dans un groupe à haute disponibilité.

* [Ajouter un disque à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * L’exemple suivant utilise [az vm disk attach-new](/cli/azure/vm/disk#attach-new) pour ajouter un disque géré de 50 Go à une machine virtuelle existante nommée `myVM` :
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Attacher un disque à l’aide du portail Azure](virtual-machines-linux-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### <a name="azure-images"></a>Images Microsoft Azure
Utilisez les commandes [az vm image](/cli/azure/vm/image) pour voir ce qui est disponible par éditeur, version de distributeur et build.

Répertorier les éditeurs disponibles :

```azurecli
az vm image list-publishers --location WestUS
```

Répertorier les produits disponibles (offres) pour un éditeur donné :

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Répertorier les références disponibles (versions distributeur) d’une offre donnée :

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Répertorier toutes les images disponibles pour une version donnée :

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

La commande **az vm create** dispose d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Il est souvent plus rapide d’utiliser des alias que d’avoir à spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

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
  
  * Exemples de commandes **az vm** rapides pour la capture d’une machine virtuelle existante utilisant des disques non gérés :
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Étapes suivantes
* Créez une machine virtuelle Linux à partir du [portail](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), avec l’[interface de ligne de commande](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou à l’aide d’un [modèle Azure Resource Manager](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Après avoir créé une machine virtuelle Linux, [ajoutez un disque de données](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

