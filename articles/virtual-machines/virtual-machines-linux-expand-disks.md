---
title: "Développer les disques durs virtuels sur la machine virtuelle Linux dans Azure | Microsoft Docs"
description: "Apprenez à développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI 2.0"
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
ms.date: 02/22/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: e8eab5aae3160c017d4b6b18aa21c66f728a6a7a
ms.lasthandoff: 03/16/2017

---

# <a name="how-to-expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli-20"></a>Comment développer des disques durs virtuels sur une machine virtuelle Linux avec Azure CLI 2.0
La taille par défaut de disque virtuel pour le système d’exploitation est généralement de 30 Go sur une machine virtuelle Linux dans Azure. Vous pouvez [ajouter des disques de données](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) afin d’offrir un espace de stockage supplémentaire, mais vous pouvez également développer le disque du système d’exploitation ou un disque de données existant. Cet article vous explique comment développer les disques gérés pour une machine virtuelle Linux à l’aide de l’interface CLI Azure 2.0. Vous pouvez également développer le disque du système d’exploitation non managé avec [Azure CLI 1.0](virtual-machines-linux-expand-disks-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="expand-managed-disk"></a>Développer un disque géré
Assurez-vous que vous avez installé la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et que vous êtes connecté à un compte Azure avec la commande [az login](/cli/azure/#login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup` et `myVM`.

1. Il est impossible d’exécuter les opérations sur les disques durs virtuels avec la machine virtuelle en cours d’exécution. Libérez la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > `az vm stop` ne publie pas les ressources de calcul. Pour publier les ressources de calcul, utilisez `az vm deallocate`. La machine virtuelle doit être libérée pour développer le disque dur virtuel.

2. Affichez la liste des disques gérés dans un groupe de ressources avec la commande [az disk list](/cli/azure/disk#list). L’exemple suivant affiche la liste des disques gérés dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az disk list -g myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    Développez le disque requis avec la commande [az disk update](/cli/azure/disk#update). L’exemple suivant développe le disque géré nommé `myDataDisk` pour qu’il ait une taille de `200` Go :

    ```azurecli
    az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 200
    ```

    > [!NOTE]
    > Lorsque vous développez un disque géré, la taille mise à jour est mappée à la taille de disque géré la plus proche. Pour obtenir un tableau des tailles et des niveaux de disques gérés disponibles, consultez [Vue d’ensemble des disques gérés Azure - Tarification et facturation](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#pricing-and-billing).

3. Démarrez votre machine virtuelle avec [az vm start](/cli/azure/vm#start). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

4. Établissez une connexion SSH à votre machine virtuelle à l’aide des informations d’identification appropriées. Pour vérifier que le disque du système d’exploitation a été redimensionné, utilisez `df -h`. La sortie de l’exemple suivant indique que la partition primaire (`/dev/sda1`) est désormais de 200 Go :

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        194G   52M   193G   1% /datadrive
    ```

## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin de stockage supplémentaire, vous pouvez également [ajouter des disques de données à une machine virtuelle Linux](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Pour plus d’informations sur le chiffrement de disque, consultez la section [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure (CLI)](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

