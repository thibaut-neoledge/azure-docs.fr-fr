---
title: "Exemple de script Azure CLI - Création d’une machine virtuelle Windows Server 2016 avec IIS à l’aide de DSC | Microsoft Docs"
description: "Exemple de script Azure CLI - Création d’une machine virtuelle Windows Server 2016 avec IIS à l’aide de DSC"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 8c5874e88fe70bef2b6b1bda666c2c69142e376d
ms.openlocfilehash: 3fdc27c54ad52dfce42f6d039a8453cdfdce00ee
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-iis-using-dsc"></a>Créer une machine virtuelle avec IIS à l’aide de DSC

Ce script crée une machine virtuelle, puis utilise l’extension DSC du script personnalisé de machine virtuelle Azure pour installer et configurer IIS. 

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`. En outre, vous devez modifier la variable $AdminPassword au début du script afin de satisfaire les exigences de complexité de mots de passe uniques et de réunion.

Cet exemple fonctionne dans un interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI sur un client Windows, consultez la page [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Utilisation de l’interface Azure CLI dans Windows).

## <a name="create-vm-sample"></a>Créer un exemple de machine virtuelle

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-windows-iis-using-dsc/create-windows-iis-using-dsc.sh "Création rapide de machine virtuelle")]

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
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#create) | Ajoute une extension de script personnalisé à la machine virtuelle, qui appelle un script pour installer IIS. |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | Crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant. Dans cet exemple, le port 80 est ouvert pour le trafic HTTP. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Windows Azure](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
