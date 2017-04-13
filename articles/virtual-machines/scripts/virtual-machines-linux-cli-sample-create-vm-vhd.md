---
title: "Exemple de script Azure CLI - Créer une machine virtuelle avec un disque dur virtuel (VHD) | Microsoft Docs"
description: "Exemple de script Azure CLI - Créez une machine virtuelle avec un disque dur virtuel (VHD)."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/09/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c654db0142df904acc5212b0f53991c54cbf2087
ms.lasthandoff: 04/06/2017

---

# <a name="create-a-vm-with-a-virtual-hard-disk"></a>Créer une machine virtuelle avec un disque dur virtuel (VHD)

Cet exemple crée une machine virtuelle à l’aide d’un disque dur virtuel (VHD).
Il crée un groupe de ressources, un compte de stockage et un conteneur, puis il crée une machine virtuelle en chargeant le disque dur virtuel (VHD) dans le conteneur.
Il remplace le clé publique SSH par votre clé publique afin que vous ayez accès à la machine virtuelle.

Vous devez disposer d’un VHD amorçable.
Vous pouvez télécharger le VHD que nous avons utilisé à partir de https://azclisamples.blob.core.windows.net/vhds/sample.vhd, ou utiliser votre propre VHD. Le script recherche `~/sample.vhd`.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-vhd/create-vm-vhd.sh "Créer une machine virtuelle à l’aide d’un disque dur virtuel (VHD)")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```azurecli
az group delete -n az-cli-vhd
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une machine virtuelle, un groupe à haute disponibilité, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az storage account list](https://docs.microsoft.com/cli/azure/storage/account#list) | Répertorie les comptes de stockage |
| [az storage account check-name](https://docs.microsoft.com/cli/azure/storage/account#check-name) | Vérifie qu’un nom de compte de stockage est valide et qu’il n’existe pas déjà |
| [az storage account keys list](https://docs.microsoft.com/cli/azure/storage/account/keys#list) | Répertorie les clés des comptes de stockage |
| [az storage blob exists](https://docs.microsoft.com/cli/azure/storage/blob#exists) | Vérifie si l’objet blob existe |
| [az storage container create](https://docs.microsoft.com/cli/azure/storage/container#create) | Crée un conteneur dans un compte de stockage. |
| [az storage blob upload](https://docs.microsoft.com/cli/azure/storage/blob#upload) | Crée un objet blob dans le conteneur en chargeant le disque dur virtuel (VHD). |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | Utilisée avec `--query` pour vérifier si le nom de la machine virtuelle est en cours d’utilisation. | 
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | Crée les machines virtuelles. |
| [az vm access set-linux-user](https://docs.microsoft.com/cli/azure/vm/access#set-linux-user) | Réinitialise la clé SSH pour autoriser l’utilisateur actuel à accéder à la machine virtuelle. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Obtient l’adresse IP de la machine virtuelle qui a été créée. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’interface Azure CLI, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts CLI de machine virtuelle dans la [documentation relative aux machines virtuelles Linux Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

