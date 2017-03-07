---
title: "Exemple de script Azure CLI - Création de deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe | Microsoft Docs"
description: "Exemple de script Azure CLI - Création de deux machines virtuelles avec un groupe de sécurité réseau interne et un groupe de sécurité réseau externe"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a172d73732354d31d717d8e2f3a5c5c43cbbd6dc
ms.openlocfilehash: 9ef17544582e944fda2ab8ea8b8098bb83e2ab57
ms.lasthandoff: 03/01/2017

---

# <a name="secure-network-traffic-between-virtual-machines"></a>Sécuriser le trafic réseau entre les machines virtuelles

Ce script crée deux machines virtuelles et sécurise le trafic entrant vers les deux machines. Une machine virtuelle est accessible sur Internet et dispose d’un groupe de sécurité réseau configuré pour autoriser le trafic sur le port 3389 et le port 80. La seconde machine virtuelle n’est pas accessible sur Internet et elle dispose d’un groupe de sécurité réseau configuré pour autoriser uniquement le trafic provenant de la première machine virtuelle. 

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`. En outre, vous devez modifier la variable $AdminPassword au début du script afin de satisfaire les exigences de complexité de mots de passe uniques et de réunion.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI sur Windows, consultez la page [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Utilisation de l’interface Azure CLI dans Windows).

## <a name="create-vm-sample-with-a-network-security-group"></a>Créer un exemple de machine virtuelle avec un groupe de sécurité réseau

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nsg/create-windows-vm-nsg.sh "Création d’une machine virtuelle avec un groupe de sécurité réseau")]

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
| [az network vnet subnet create](https://docs.microsoft.com/cli/azure/network/vnet/subnet#create) | Crée un sous-réseau. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée la machine virtuelle et la connecte à la carte réseau, au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az network nsg rule update](https://docs.microsoft.com/cli/azure/network/nsg/rule#update) | Met à jour une règle de groupe de sécurité réseau. Dans cet exemple, la règle principale est mise à jour pour transférer le trafic uniquement à partir du sous-réseau frontal. |
| [az network nsg rule list](https://docs.microsoft.com/cli/azure/network/nsg/rule#list) | Renvoie des informations relatives à une règle de groupe de sécurité réseau. Dans cet exemple, le nom de la règle est stocké dans une variable à utiliser ultérieurement dans le script. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

