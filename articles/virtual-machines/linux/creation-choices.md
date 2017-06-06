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
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: b2f93579eb1c7a69d0dbd1b0ef112aed9b2168c3
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Différentes méthodes pour créer une machine virtuelle Linux
Dans Azure, vous avez la possibilité de créer une machine virtuelle (VM) Linux à l’aide des outils et des flux de travail qui vous conviennent. Cet article résume ces différences et fournit des exemples pour créer vos machines virtuelles Linux, y compris avec Azure CLI 2.0. Vous trouverez également les options de création à l’aide [d’Azure CLI 1.0](creation-choices-nodejs.md).

[L’interface Azure CLI 2.0](/cli/azure/install-az-cli2) est disponible sur les plateformes via un package npm, des packages fournis par un distributeur ou un conteneur Docker. Installer la version la plus appropriée pour votre environnement et connectez-vous à un compte Azure à l’aide de [az login](/cli/azure/#login)

* [Création d’une machine virtuelle Linux avec Azure CLI 2.0](quick-create-cli.md)
  
  * Avec la commande [az group create](/cli/azure/group#create), créez un groupe de ressources nommé *myResourceGroup* : 
   
    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```
    
  * Avec la commande [az vm create](/cli/azure/vm#create), créez une machine virtuelle nommée *myVM* à l’aide de la dernière version de l’image *UbuntuLTS* et générez des clés SSH si elles n’existent pas déjà dans *~/.ssh* :

    ```azurecli
    az vm create \
        --resource-group myResourceGroup \
        --name myVM \
        --image UbuntuLTS \
        --generate-ssh-keys
    ```

* [Créer une machine virtuelle Linux à l’aide d’un modèle Azure](create-ssh-secured-vm-from-template.md)
  
  * L’exemple suivant utilise la commande [az group deployment create](/cli/azure/group/deployment#create) pour créer une machine virtuelle à partir d’un modèle stocké sur GitHub :
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
* [Créer une machine virtuelle Linux et personnaliser avec cloud-init](tutorial-automate-vm-deployment.md)

* [Créer une application hautement disponible et à charge équilibrée sur plusieurs machines virtuelles Linux](tutorial-load-balancer.md)


## <a name="azure-portal"></a>Portail Azure
Le [portail Azure](https://portal.azure.com) vous permet de créer rapidement une machine virtuelle, puisque vous n’avez rien à installer sur votre système. Utilisez le portail Azure pour créer la machine virtuelle :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](quick-create-portal.md) 


## <a name="operating-system-and-image-choices"></a>Système d'exploitation et choix d'images
Lors de la création d’une machine virtuelle, vous choisissez une image basée sur le système d’exploitation que vous souhaitez exécuter. Microsoft Azure et ses partenaires proposent de nombreuses images, dont certaines comprennent des applications et des outils préinstallés. Sinon, téléchargez l’une de vos propres images (voir [la section ci-dessous](#use-your-own-image)).

### <a name="azure-images"></a>Images Microsoft Azure
Utilisez les commandes [az vm image](/cli/azure/vm/image) pour voir ce qui est disponible par éditeur, version de distributeur et build.

Répertorier les éditeurs disponibles :

```azurecli
az vm image list-publishers --location eastus
```

Répertorier les produits disponibles (offres) pour un éditeur donné :

```azurecli
az vm image list-offers --publisher Canonical --location eastus
```

Répertorier les références disponibles (versions distributeur) d’une offre donnée :

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location eastus
```

Répertorier toutes les images disponibles pour une version donnée :

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location eastus
```

Pour obtenir des exemples sur la navigation et l’utilisation d’images disponibles, consultez la page [Sélectionner des images de VM Linux avec l’interface de ligne de commande Azure](cli-ps-findimage.md).

La commande [az vm create](/cli/azure/vm#create) dispose d’alias que vous pouvez utiliser pour accéder rapidement aux distributeurs les plus courants et à leurs versions les plus récentes. Il est souvent plus rapide d’utiliser des alias que d’avoir à spécifier l’éditeur, l’offre, la référence et la version à chaque fois que vous créez une machine virtuelle :

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
Si vous avez besoin de personnalisations spécifiques, vous pouvez utiliser une image basée sur une machine virtuelle Azure existante, en capturant cette machine virtuelle. Vous pouvez également télécharger une image créée sur site. Pour plus d’informations sur les versions prises en charge et comment utiliser vos propres images, consultez les articles suivants :

* [Distributions prises en charge par Azure](endorsed-distros.md)
* [Informations concernant les distributions non approuvées](create-upload-generic.md)
* [Comment créer une image à partir d’une machine virtuelle Azure existante](tutorial-custom-images.md).
  
  * Exemples de commandes rapides pour la création d’images à partir d’une machine virtuelle Azure existante :
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm image create --resource-group myResourceGroup --source myVM --name myImage
    ```

## <a name="next-steps"></a>Étapes suivantes
* Créez une machine virtuelle Linux avec l’[interface de ligne de commande](quick-create-cli.md), à partir du [portail](quick-create-portal.md) ou à l’aide d’un [modèle Azure Resource Manager](../windows/cli-deploy-templates.md).
* Après avoir créé une machine virtuelle Linux, [en savoir plus sur les disques et les stockages Azure](tutorial-manage-disks.md).
* Étapes rapides pour [réinitialiser un mot de passe ou des clés SSH et gérer les utilisateurs](using-vmaccess-extension.md).

