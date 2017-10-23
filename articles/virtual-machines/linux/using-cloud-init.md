---
title: Utiliser cloud-init pour personnaliser une machine virtuelle Linux | Microsoft Docs
description: "Guide pratique d’utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Utiliser cloud-init pour personnaliser une machine virtuelle Linux dans Azure
Cet article vous explique comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour définir le nom d’hôte, mettre à jour des packages et gérer des comptes d’utilisateur sur une machine virtuelle dans Azure. Ces scripts cloud-init s’exécutent au démarrage lorsque vous créez une machine virtuelle avec Azure CLI 2.0. Pour une présentation plus détaillée de l’installation d’applications, l’écriture de fichiers de configuration et l’injection de clés à partir de Key Vault, consultez [ce didacticiel](tutorial-automate-vm-deployment.md). Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Présentation de cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) est une méthode largement utilisée pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. Comme cloud-init s’exécute pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration.

Cloud-init fonctionne aussi sur les différentes distributions. Par exemple, vous n’utilisez pas **apt-get install** ou **yum install** pour installer un package. Au lieu de cela, vous pouvez définir une liste des packages à installer, après quoi cloud-init se charge d’utiliser automatiquement l’outil de gestion de package natif correspondant à la distribution que vous sélectionnez.

Nous collaborons avec nos partenaires pour que cloud-init soit inclus et fonctionne dans les images qu’ils fournissent à Azure. Le tableau suivant présente la disponibilité actuelle de cloud-init sur les images de plateforme Azure :

| Alias | Éditeur | Offer | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04-LTS |le plus récent |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |le plus récent |
| CoreOS |CoreOS |CoreOS |Stable |le plus récent |


## <a name="set-the-hostname-with-cloud-init"></a>Définir le nom d’hôte avec cloud-init
Les fichiers cloud-init sont écrits en [YAML](http://www.yaml.org). Pour exécuter un script cloud-init lorsque vous créez une machine virtuelle dans Azure avec [az vm create](/cli/azure/vm#create), spécifiez le fichier cloud-init avec le commutateur `--custom-data`. Examinons quelques exemples de ce que vous pouvez configurer avec un fichier cloud-init. Un scénario courant consiste à définir le nom d’hôte d’une machine virtuelle. Par défaut, le nom d’hôte est le même que le nom de la machine virtuelle. 

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée le groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Dans l’interpréteur de commandes actuel, créez un fichier nommé *cloud_init_hostname.txt* et collez la configuration suivante. Par exemple, créez le fichier dans l’interpréteur de commandes Cloud et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Dans Cloud Shell, entrez `sensible-editor cloud_init_hostname.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne :

```yaml
#cloud-config
hostname: myhostname
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#create) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_hostname.txt` comme suit :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

Une fois créée, Azure CLI affiche des informations sur la machine virtuelle. Utilisez `publicIpAddress` pour établir une connexion SSH à votre machine virtuelle. Entrez votre propre adresse comme suit :

```bash
ssh azureuser@publicIpAddress
```

Pour afficher le nom de la machine virtuelle, utilisez la commande `hostname` comme suit :

```bash
hostname
```

La machine virtuelle doit signaler le nom d’hôte comme la valeur définie dans le fichier cloud-init, comme indiqué dans l’exemple de sortie suivant :

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Mettre à jour une machine virtuelle avec cloud-init
Pour des raisons de sécurité, configurez une machine virtuelle pour appliquer les dernières mises à jour au premier démarrage. Étant donné que cloud-init fonctionne sur différentes distributions Linux, il est inutile de spécifier `apt` ou `yum` pour le gestionnaire de package. Vous définissez plutôt `package_upgrade` et laissez le processus cloud-init déterminer le mécanisme approprié pour la distribution en cours d’utilisation. Ce flux de travail vous permet d’utiliser les mêmes scripts cloud-init sur toutes les distributions.

Pour afficher le processus de mise à niveau en action, créez un fichier cloud-init nommé *cloud_init_upgrade.txt* et collez la configuration suivante :

```yaml
#cloud-config
package_upgrade: true
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#create) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_upgrade.txt` comme suit :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre adresse IP publique comme suit :

```bash
ssh azureuser@publicIpAddress
```

Exécutez l’outil de gestion de package et vérifiez s’il existe des mises à jour. L’exemple suivant utilise `apt-get` sur une machine virtuelle Ubuntu :

```bash
sudo apt-get upgrade
```

Étant donné que cloud-init a recherché et installé des mises à jour au démarrage, il n’y a aucune mise à jour à appliquer, comme indiqué dans l’exemple de sortie suivant :

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Ajouter un utilisateur à une machine virtuelle avec cloud-init
L’une des premières tâches à effectuer sur une nouvelle machine virtuelle Linux consiste à ajouter un utilisateur pour vous-même afin d’éviter l’utilisation de *root*. Les clés SSH sont un bon réflexe de sécurité et de facilité d’utilisation. Les clés sont ajoutées au fichier *~/.ssh/authorized_keys* avec ce script cloud-init.

Pour ajouter un utilisateur à une machine virtuelle Linux, créez un fichier cloud-init nommé *cloud_init_add_user.txt* et collez la configuration suivante. Indiquez votre propre clé publique (comme le contenu de *~/.ssh/id_rsa.pub*) pour *ssh-authorized-keys* :

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#create) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_add_user.txt` comme suit :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre adresse IP publique comme suit :

```bash
ssh myadminuser@publicIpAddress
```

Pour confirmer que l’utilisateur a été ajouté à la machine virtuelle et aux groupes spécifiés, affichez le contenu du fichier */etc/group* comme suit :

```bash
cat /etc/group
```

L’exemple de sortie suivant montre que l’utilisateur indiqué dans le fichier *cloud_init_add_user.txt* a été ajouté à la machine virtuelle et au groupe approprié :

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Étapes suivantes
Le processus cloud-init est l’une des méthodes standard permettant de modifier vos machines virtuelles Linux au démarrage. Dans Azure, vous pouvez également utiliser des extensions de machine virtuelle pour modifier votre machine virtuelle Linux au démarrage ou une fois qu’elle est en cours d’exécution. Par exemple, vous pouvez utiliser l’extension de machine virtuelle Azure pour exécuter un script sur une machine virtuelle en cours d’exécution, pas seulement au premier démarrage. Pour plus d’informations sur les extensions de machine virtuelle, consultez [Extensions et fonctionnalités des machines virtuelles Azure](extensions-features.md) ou pour obtenir des exemples d’utilisation de l’extension, consultez [Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Linux Azure à l’aide de l’extension VMAccess](using-vmaccess-extension.md).