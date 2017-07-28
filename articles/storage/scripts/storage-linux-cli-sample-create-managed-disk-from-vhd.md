---
title: "Exemple de script avec l’interface de ligne de commande Azure CLI - Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement | Microsoft Docs"
description: "Exemple de script avec l’interface de ligne de commande Azure CLI - Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 2e0d668c1fc419abde95207654cab35ea8b058fb
ms.contentlocale: fr-fr
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>Créer un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement avec l’interface de ligne de commande

Ce script crée un disque managé à partir d’un fichier de VHD dans un compte de stockage dans le même abonnement. Utilisez ce script pour importer un VHD spécialisé (non généralisé/préparé avec Sysprep) vers un disque de système d’exploitation managé pour créer une machine virtuelle. Ou bien, utilisez-le pour importer un VHD de données vers un disque de données managées. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Créer un disque managé à partir d’un VHD")]


## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer un disque managé à partir d’un VHD. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | Crée un disque managé en utilisant l’URI d’un VHD dans un compte de stockage dans le même abonnement |

## <a name="next-steps"></a>Étapes suivantes

[Créer une machine virtuelle en joignant un disque managé en tant que disque de système d’exploitation](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle et de disques managés dans la [documentation relative aux machines virtuelles Linux Azure](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

