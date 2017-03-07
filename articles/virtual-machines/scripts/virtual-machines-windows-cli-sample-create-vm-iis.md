---
title: "Exemple de script Azure CLI - Installation d’IIS | Microsoft Docs"
description: "Exemple de script Azure CLI - Installation d’IIS"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/28/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 1c0d2de77acc4696cb0e274e14591a2925454dd6
ms.lasthandoff: 03/01/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Créer rapidement une machine virtuelle avec l’interface Azure CLI

Ce script crée une machine virtuelle Azure avec Windows Server 2016, puis utilise l’extension du script personnalisé de machine virtuelle Azure pour installer IIS. Une fois le script exécuté, le site web IIS par défaut est accessible sur l’adresse IP publique de la machine virtuelle.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`. En outre, vous devez modifier la variable $AdminPassword au début du script afin de satisfaire les exigences de complexité de mots de passe uniques et de réunion.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI sur Windows, consultez la page [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Utilisation de l’interface Azure CLI dans Windows).

## <a name="create-vm-sample"></a>Créer un exemple de machine virtuelle

```azurecli
#!/bin/bash

# Update for your admin password
AdminPassword=ChangeYourAdminPassword1

# Create a resource group.
az group create --name myResourceGroup --location westeurope

# Create a virtual machine. 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password $AdminPassword

# Open port 80 to allow web traffic to host.
az vm open-port --port 80 --resource-group myResourceGroup --name myVM 

# Use CustomScript extension to install Apache.
az vm extension set \
  --publisher Microsoft.Compute \
  --version 1.8 \
  --name CustomScriptExtension \
  --vm-name myVM \
  --resource-group myResourceGroup \
  --settings '{"commandToExecute":"powershell.exe Install-WindowsFeature -Name Web-Server"}'
```

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante peut être utilisée pour la suppression du groupe de ressources, de la machine virtuelle et de toutes les ressources associées.

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée la machine virtuelle et la connecte à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | Crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant. Dans cet exemple, le port 80 est ouvert pour le trafic HTTP. |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | Ajoute et exécute une extension de machine virtuelle sur une machine virtuelle. Dans cet exemple, l’extension de script personnalisé sert à installer IIS.|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

