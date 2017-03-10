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
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 73bd1f9b018e49ad8a536b80275cbf0faa030b74
ms.lasthandoff: 03/01/2017

---

# <a name="troubleshoot-a-vms-operating-system-disk"></a>Résoudre les problèmes liés au disque du système d’exploitation de machines virtuelles

Ce script monte le disque de système d’exploitation d’une machine virtuelle en échec ou posant problème en tant que disque de données d’une seconde machine virtuelle. Cette procédure peut s’avérer utile lors de la résolution de problèmes de disque ou la récupération de données. 

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de la commande `az login`. Une machine virtuelle existante sera également nécessaire. Mettez à jour le nom et le groupe de ressources de la machine virtuelle existante dans l’exemple de script.

Cet exemple fonctionne dans une interface d’interpréteur de commandes Bash. Pour en savoir plus les options d’exécution de scripts Azure CLI dans le client Windows, consultez la page [Running the Azure CLI in Windows (Exécution d’Azure CLI dans Windows)](../virtual-machines-windows-cli-options.md).

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[principal](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Créer rapidement une machine virtuelle")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#show) | Renvoie la liste des machines virtuelles. Dans ce cas, l’option de requête permet de renvoyer le disque de système d’exploitation de la machine virtuelle. Cette valeur est ensuite ajoutée à un nom de variable « uri ». |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#delete) | Supprime une machine virtuelle. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crée une machine virtuelle.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) | Attache un disque à une machine virtuelle. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Renvoie les adresses IP d’une machine virtuelle. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

