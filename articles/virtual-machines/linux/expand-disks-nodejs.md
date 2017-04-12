---
title: "Développer le disque du système d’exploitation sur une machine virtuelle Linux avec Azure CLI 1.0 | Microsoft Docs"
description: "Cet article explique comment développer le disque virtuel du système d’exploitation sur une machine virtuelle Linux à l’aide de l’interface Azure CLI 1.0 et du modèle de déploiement Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6ac399b4aa81e849aacb37e55e804b935dde7419
ms.lasthandoff: 04/03/2017


---

# <a name="expand-os-disk-on-a-linux-vm-using-the-azure-cli-with-the-azure-cli-10"></a>Développer le disque de système d’exploitation sur une machine virtuelle Linux à l’aide de l’interface Azure CLI 1.0
La taille par défaut de disque virtuel pour le système d’exploitation est généralement de 30 Go sur une machine virtuelle Linux dans Azure. Vous pouvez [ajouter des disques de données](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) afin d’offrir un espace de stockage supplémentaire, mais vous pouvez également développer le disque du système d’exploitation. Cet article vous explique comment développer le disque du système d’exploitation sur une machine virtuelle Linux à l’aide de disques non managés avec l’interface Azure CLI 1.0.

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#prerequisites) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](expand-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager

## <a name="prerequisites"></a>Composants requis
La [dernière version de l’interface Azure CLI 1.0](../../cli-install-nodejs.md) doit être installée et connectée à un [compte Azure](https://azure.microsoft.com/pricing/free-trial/) à l’aide du mode Resource Manager comme suit :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup` et `myVM`.

## <a name="expand-os-disk"></a>Développer le disque du système d’exploitation

1. Il est impossible d’exécuter les opérations sur les disques durs virtuels avec la machine virtuelle en cours d’exécution. L’exemple suivant arrête et redéploie la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    azure vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `azure vm stop` ne publie pas les ressources de calcul. Pour publier les ressources de calcul, utilisez `azure vm deallocate`. La machine virtuelle doit être libérée pour développer le disque dur virtuel.

2. Modifiez la taille du disque non géré du système d’exploitation à l’aide de la commande `azure vm set`. L’exemple suivant met à jour la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`, sur la valeur de `50` Go :

    ```azurecli
    azure vm set --resource-group myResourceGroup --name myVM --new-os-disk-size 50
    ```

3. Démarrez votre machine virtuelle comme suit :

    ```azurecli
    azure vm start --resource-group myResourceGroup --name myVM
    ```

4. Établissez une connexion SSH à votre machine virtuelle à l’aide des informations d’identification appropriées. Pour vérifier que le disque du système d’exploitation a été redimensionné, utilisez `df -h`. La sortie de l’exemple suivant indique que la partition primaire (`/dev/sda1`) est désormais de 50 Go :

    ```bash
    Filesystem      Size  Used Avail Use% Mounted on
    udev            1.7G     0  1.7G   0% /dev
    tmpfs           344M  5.0M  340M   2% /run
    /dev/sda1        49G  1.3G   48G   3% /
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin de stockage supplémentaire, vous pouvez également [ajouter des disques de données à une machine virtuelle Linux](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pour plus d’informations sur le chiffrement de disque, consultez la section [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure (CLI)](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

