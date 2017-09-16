---
title: "Exemple de script Azure CLI - Redémarrage de machines virtuelles | Microsoft Docs"
description: "Exemple de script Azure CLI - Redémarrer des machines virtuelles par balise et ID"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: ea114f484c774573b7d219cff9102a7308af356e
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---

# <a name="restart-vms"></a>Redémarrer les machines virtuelles

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Cet exemple montre plusieurs façons d’obtenir des machines virtuelles et de les redémarrer.

Le premier redémarre toutes les machines virtuelles dans le groupe de ressources.

```bash
az vm restart --ids $(az vm list --resource-group myResourceGroup --query "[].id" -o tsv)
```

Le second obtient des machines virtuelles marquées à l’aide de `az resouce list` et filtre les ressources qui sont des machines virtuelles, puis les redémarre.

```bash
az vm restart --ids $(az resource list --tag "restart-tag" --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv)
```

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../windows/cli-options.md).


## <a name="sample-script"></a>Exemple de script

L’exemple comporte trois scripts.
Le premier configure les machines virtuelles.
Il utilise l’option no-wait. Ainsi, la commande renvoie le résultat sans attendre l’approvisionnement de chaque machine virtuelle.
Le second attend que les machines virtuelles soient entièrement approvisionnées.
Le troisième script redémarre toutes les machines virtuelles qui ont été configurées, puis simplement les machines virtuelles marquées.

### <a name="provision-the-vms"></a>Approvisionnement des machines virtuelles

Ce script crée un groupe de ressources, puis trois machines virtuelles à redémarrer.
Deux présentent un indicateur.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/provision.sh "Approvisionnement des machines virtuelles")]

### <a name="wait"></a>Wait

Ce script vérifie l’état de configuration toutes les 20 secondes jusqu'à ce que les trois machines virtuelles soient approvisionnées, ou qu’une d’entre elles ne puisse l’être.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/wait.sh "Attente du provisionnement des machines virtuelles")]

### <a name="restart-the-vms"></a>Redémarrer les machines virtuelles

Ce script redémarre toutes les machines virtuelles dans le groupe de ressources, puis redémarre uniquement les machines virtuelles marquées.

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart.sh "Redémarrer les machines virtuelles par balise")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer les groupes de ressources, les machines virtuelles et toutes les ressources associées.

```azurecli-interactive 
az group delete -n myResourceGroup --no-wait --yes
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#az_vm_availability_set_create) | Crée les machines virtuelles.  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilisé avec `--query` pour garantir que les machines virtuelles sont configurées avant de les redémarrer, puis pour obtenir les ID des machines virtuelles pour les redémarrer. |
| [az resource list](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Utilisé avec `--query` pour obtenir les ID des machines virtuelles qui utilisent l’indicateur. |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#az_vm_list) | Redémarre les machines virtuelles. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az_vm_extension_set) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

