---
title: "Exemple de script Azure CLI - Création d’une machine virtuelle Windows Server | Microsoft Docs"
description: "Exemple de script Azure CLI - Création d’une machine virtuelle Windows Server"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 777baeb2a6a281343da8bea2de957ee9883a5ba2
ms.openlocfilehash: 4cc559956cf49d002124b6035eca874996c730f9
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-virtual-machine-with-the-azure-cli"></a>Créer une machine virtuelle avec l’interface Azure CLI

Ce script crée une machine virtuelle Azure avec un système d’exploitation Windows Server 2016 et les ressources réseau associées. Une fois le script exécuté, la machine virtuelle est accessible sur RDP.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`. En outre, vous devez modifier la variable $AdminPassword au début du script afin de satisfaire les exigences de complexité de mots de passe uniques et de réunion.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI sur Windows, consultez la page [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Utilisation de l’interface Azure CLI dans Windows).

## <a name="create-vm-sample"></a>Créer un exemple de machine virtuelle

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-detailed/create-windows-vm-detailed.sh "Création rapide de machine virtuelle")]

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
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#create) | Crée un réseau virtuel et un sous-réseau Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#create) | Crée une adresse IP publique avec une adresse IP statique et un nom DNS associé. |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#create) | Crée un groupe de sécurité réseau qui représente une frontière de sécurité entre Internet et la machine virtuelle. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#create) | Crée une carte réseau virtuelle et l’associe au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée la machine virtuelle et la connecte à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

