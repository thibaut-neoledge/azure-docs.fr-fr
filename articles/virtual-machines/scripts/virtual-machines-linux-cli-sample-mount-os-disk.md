---
title: "Exemple de script Azure CLI - Monter un disque de système d’exploitation | Microsoft Docs"
description: "Exemple de script Azure CLI - Monter un disque de système d’exploitation"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c32ea5e6cade34a9c8dac0eab523ebcaa10ef039
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>Résoudre les problèmes liés au disque du système d’exploitation de machines virtuelles

Ce script monte le disque de système d’exploitation d’une machine virtuelle en échec ou posant problème en tant que disque de données d’une seconde machine virtuelle. Cette procédure peut s’avérer utile lors de la résolution de problèmes de disque ou la récupération de données. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | Renvoie la liste des machines virtuelles. Dans ce cas, l’option de requête permet de renvoyer le disque de système d’exploitation de la machine virtuelle. Cette valeur est ensuite ajoutée à un nom de variable « uri ». |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | Supprime une machine virtuelle. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | Crée une machine virtuelle.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#az_vm_disk_attach) | Attache un disque à une machine virtuelle. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | Renvoie les adresses IP d’une machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
