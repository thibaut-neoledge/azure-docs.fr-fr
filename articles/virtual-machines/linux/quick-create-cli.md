---
title: "Démarrage rapide avec Azure - Commande pour la création d’une machine virtuelle | Microsoft Docs"
description: "Apprenez à créer rapidement des machines virtuelles avec Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: 2f1f63d14468467c8cf3956324beb829adce296f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/17/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Créer une machine virtuelle Linux avec Azure CLI

L’interface de ligne de commande (CLI) Azure permet de créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide détaille l’utilisation de la CLI Azure pour le déploiement d’une machine virtuelle Azure exécutant Ubuntu Server. Une fois le serveur déployé, une connexion SSH est créée, et un serveur web NGINX est installé.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce démarrage rapide requiert Azure CLI version 2.0.4 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). Vous pouvez également utiliser [Cloud Shell](/azure/cloud-shell/quickstart) depuis votre navigateur.

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Create virtual machine

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). 

L’exemple suivant crée une machine virtuelle nommée *myVM* et des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Lorsque la machine virtuelle a été créée, l’interface de ligne de commande Azure affiche des informations similaires à l’exemple suivant. Notez la valeur de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web 

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Si cet ordinateur virtuel doit être un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm#open-port) pour ouvrir le port souhaité.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Se connecter avec SSH à votre machine virtuelle

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle. Veillez à remplacer *<publicIpAddress>* par l’adresse IP publique correcte de votre machine virtuelle.  Dans l’exemple ci-dessus, notre adresse IP était *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installer NGINX

Utilisez le script Bash suivant pour mettre à jour les sources de package et installer le dernier package NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX

Une fois NGINX installé et le port 80 ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web de votre choix pour afficher la page d’accueil NGINX par défaut. Veillez à utiliser la *publicIpAddress* indiquée ci-dessus pour vous rendre sur la page par défaut. 

![Site par défaut NGINX](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle simple ainsi qu’une règle de groupe de sécurité réseau, et installé un serveur web. Pour en savoir plus sur les machines virtuelles Azure, suivez le didacticiel pour les machines virtuelles Linux.


> [!div class="nextstepaction"]
> [Didacticiels sur les machines virtuelles Azure Linux](./tutorial-manage-vm.md)

