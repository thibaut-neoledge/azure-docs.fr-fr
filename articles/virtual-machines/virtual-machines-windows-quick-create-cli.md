---
title: "Démarrage rapide avec Azure - Commande pour la création d’une machine virtuelle Windows | Microsoft Docs"
description: "Apprenez à créer rapidement des machines virtuelles Windows avec Azure CLI."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 3064da2a6afee007418ef6ba5ec4247e3e045c38
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli-20"></a>Créer une machine virtuelle Windows avec l’interface Azure CLI 2.0

Vous utilisez Azure CLI 2.0 pour créer et gérer des ressources Azure à partir de la ligne de commande ou dans les scripts. Ce guide détaille l’utilisation de l’interface de ligne de commande Azure pour le déploiement d’une machine virtuelle Azure exécutant Windows Server 2016.

Avant de commencer, assurez-vous que l’interface de ligne de commande Azure est installée. Pour plus d’informations, consultez le [Guide d’installation de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#login) et suivez les instructions à l’écran.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Create virtual machine

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). 

L’exemple suivant permet de créer une machine virtuelle nommée `myVM`. Cet exemple utilise `azureuser` pour le nom d’utilisateur administratif et ` myPassword12` comme mot de passe. Mettez à jour ces valeurs avec quelque chose d’approprié pour votre environnement. Ces valeurs sont nécessaires lors de la création d’une connexion avec la machine virtuelle.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

Lorsque la machine virtuelle a été créée, l’interface de ligne de commande Azure affiche des informations similaires à l’exemple suivant. Prenez note de l’adresse IP publique. Cette adresse est utilisée pour accéder à la machine virtuelle.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Connexion à la machine virtuelle

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle. Remplacez l’adresse IP par l’adresse IP publique de votre machine virtuelle. À l'invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Suppression d'une machine virtuelle

Lorsque vous n’en avez plus besoin, la commande suivante permet de supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

[Didacticiel d’installation de rôle et de configuration de pare-feu](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Découvrir des exemples de commande de déploiement de machine virtuelle](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
