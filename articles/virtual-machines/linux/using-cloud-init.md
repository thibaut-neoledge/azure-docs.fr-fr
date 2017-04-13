---
title: Utiliser cloud-init pour personnaliser une machine virtuelle Linux | Microsoft Docs
description: "Guide pratique d’utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création avec Azure CLI 2.0 en préversion"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 9864e4dfe3c0288e762f64a827e533ed9820ee64
ms.lasthandoff: 04/03/2017


---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation"></a>Utiliser cloud-init pour personnaliser une machine virtuelle Linux lors de la création
Cet article vous montre comment créer un script cloud-init pour définir le nom d’hôte, mettre à jour les packages installés et gérer les comptes d’utilisateur avec Azure CLI 2.0.  Les scripts cloud-init sont appelés lorsque vous créez une machine virtuelle à partir de l’interface de ligne de commande Azure.  Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](using-cloud-init-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Commandes rapides
Créez un script cloud-init.txt qui définit le nom d’hôte, met à jour tous les packages et ajoute un utilisateur sudo à Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```

Créez un groupe de ressources pour y lancer des machines virtuelles avec [az group create](/cli/azure/group#create). L’exemple suivant permet de créer le groupe de ressources nommé `myResourceGroup` :

```azurecli
az group create --name myResourceGroup --location westus
```

Créez une machine virtuelle Linux à configurer au cours de démarrage avec [az vm create](/cli/azure/vm#create) à l’aide de cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
### <a name="introduction"></a>Introduction
Lorsque vous lancez une nouvelle machine virtuelle Linux, vous obtenez une machine virtuelle Linux standard, non personnalisée ni adaptée à vos besoins. [Cloud-init](https://cloudinit.readthedocs.org) est un moyen classique d'injecter les paramètres d'un script ou d’une configuration dans cette machine virtuelle Linux lorsqu’elle démarre pour la première fois.

Dans Azure, il existe trois façons différentes d’apporter des modifications à une machine virtuelle Linux pendant son déploiement ou son démarrage.

* Injectez des scripts à l’aide de cloud-init.
* Injectez des scripts à l’aide de [l’extension Azure VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Un modèle Azure utilisant cloud-init.
* Un modèle Azure utilisant [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour injecter des scripts à tout moment après le démarrage :

* SSH pour exécuter directement des commandes.
* Injectez des scripts à l’aide de [l’extension Azure VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)de manière impérative ou dans un modèle Azure.
* Des outils de gestion de la configuration tels qu’Ansible, Salt, Chef et Puppet.

> [!NOTE]
> l’extension VMAccess exécute un script comme racine de la même manière à l’aide de SSH.  Cependant, l’utilisation de l’extension de machine virtuelle active plusieurs fonctionnalités qu’Azure offre qui peuvent être utiles selon votre scénario.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Disponibilité de cloud-init lors de la création d’alias d’images de machine virtuelle Azure :
| Alias | Éditeur | Offer | SKU | Version | Cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7,2 |le plus récent |no |
| CoreOS |CoreOS |CoreOS |Stable |le plus récent |yes |
| Debian |credativ |Debian |8 |le plus récent |no |
| openSUSE |SUSE |openSUSE |13.2 |le plus récent |no |
| RHEL |Redhat |RHEL |7,2 |le plus récent |no |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |le plus récent |yes |

Microsoft collabore avec ses partenaires pour que cloud-init soit inclus et fonctionne dans les images qu’ils fournissent à Azure.

## <a name="add-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Ajouter un script cloud-init à la création d’une machine virtuelle avec l’interface CLI Azure
Pour lancer un script cloud-init lors de la création d'une machine virtuelle dans Azure, spécifiez le fichier cloud-init à l'aide du commutateur d’interface de ligne de commande Azure `--custom-data` .

Créez un groupe de ressources afin d’y lancer des machines virtuelles.

Créez un groupe de ressources pour y lancer des machines virtuelles avec [az group create](/cli/azure/group#create). L’exemple suivant permet de créer le groupe de ressources nommé `myResourceGroup` :

```azurecli
az group create --name myResourceGroup --location westus
```

Créez une machine virtuelle Linux à configurer au cours de démarrage avec [az vm create](/cli/azure/vm#create) à l’aide de cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

## <a name="create-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Créer un script cloud-init pour définir le nom d’hôte d’une machine virtuelle Linux
Le nom d’hôte est l’un des paramètres les plus simples et les plus importants pour une machine virtuelle Linux. Nous pouvons facilement définir ce paramètre en utilisant cloud-init avec ce script.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Exemple de script cloud-init nommé `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Lors du démarrage initial de la machine virtuelle, ce script cloud-init définit le nom d'hôte sur `myservername`. Créez une machine virtuelle Linux à configurer au cours de démarrage avec [az vm create](/cli/azure/vm#create) à l’aide de cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud-init.txt
```

Connectez-vous et vérifiez le nom d'hôte de la nouvelle machine virtuelle.

```bash
ssh myVM
hostname
myservername
```

## <a name="create-a-cloud-init-script-to-update-linux"></a>Créer un script cloud-init pour mettre à jour Linux
Pour des questions de sécurité, configurez votre machine virtuelle Ubuntu de manière à ce qu’elle se mette à jour au premier démarrage.  À l'aide de cloud-init, nous pouvons le faire avec le script suivant, en fonction de la distribution Linux que vous utilisez.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Exemple de script cloud-init `cloud_config_apt_upgrade.txt` pour la famille Debian
```sh
#cloud-config
apt_upgrade: true
```

Une fois Linux démarré, tous les packages installés sont mis à jour par le biais d’ `apt-get`. Créez une machine virtuelle Linux à configurer au cours de démarrage avec [az vm create](/cli/azure/vm#create) à l’aide de cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud_config_apt_upgrade.txt
```

Connectez-vous et vérifiez que tous les packages sont mis à jour.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="create-a-cloud-init-script-to-add-a-user-to-linux"></a>Créer un script cloud-init pour ajouter un utilisateur à Linux
L’une des premières tâches liées à n'importe quelle nouvelle machine virtuelle Linux consiste à ajouter un utilisateur pour vous-même ou à éviter d'utiliser `root`. Les clés SSH sont la meilleure pratique en matière de sécurité et de facilité d’utilisation. Elles sont ajoutées au fichier `~/.ssh/authorized_keys` avec ce script cloud-init.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Exemple de script cloud-init `cloud_config_add_users.txt` pour la famille Debian
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Une fois Linux démarré, tous les utilisateurs répertoriés sont créés et ajoutés au groupe sudo. Créez une machine virtuelle Linux à configurer au cours de démarrage avec [az vm create](/cli/azure/vm#create) à l’aide de cloud-init.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --custom-data cloud_config_add_users.txt
```

Connectez-vous et vérifiez l’utilisateur qui vient d’être créé.

```bash
ssh myVM
cat /etc/group
```

Sortie

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Étapes suivantes
Cloud-init est devenu une méthode standard pour modifier votre machine virtuelle Linux au démarrage. Azure propose également des extensions de machine virtuelle, ce qui vous permet de modifier votre machine virtuelle Linux au démarrage ou pendant son exécution. Par exemple, vous pouvez utiliser la VMAccessExtension Azure pour réinitialiser les informations de SSH ou de l’utilisateur pendant l’exécution de la machine virtuelle. Avec cloud-init, vous devez effectuer un redémarrage pour réinitialiser le mot de passe.

[À propos des extensions et des fonctionnalités des machines virtuelles](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


