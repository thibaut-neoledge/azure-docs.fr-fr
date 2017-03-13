---
title: "Convertir une machine virtuelle Linux dans Azure à partir de disques non gérés vers des disques gérés | Microsoft Docs"
description: "Procédure de conversion d’une machine virtuelle à partir de disques non gérés vers Azure Managed Disks à l’aide d’Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 6fda4b6e77104b6022b86010b53b46ae5df1b82e
ms.openlocfilehash: 937b22dd9ad26211b006326b39cafe9c5da4e8bd
ms.lasthandoff: 02/27/2017

---

# <a name="how-to-convert-a-linux-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Procédure de conversion d’une machine virtuelle Linux à partir de disques non gérés vers Azure Managed Disks

Si vous avez des machines virtuelles Linux existantes dans Azure qui utilisent des disques non gérés dans des comptes de stockage et que vous souhaitez qu’elles puissent tirer parti des disques gérés, vous pouvez les convertir. Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés. Le processus de conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos machines virtuelles au cours d’une fenêtre de maintenance préexistante. Le processus de migration n’est pas réversible. Veillez à tester le processus en migrant une machine virtuelle de test avant d’effectuer la migration en production.

> [!IMPORTANT] 
> Lors de la conversion, vous libérez la machine virtuelle. Celle-ci reçoit une nouvelle adresse IP lorsqu’elle est démarrée après la conversion. Si vous avez une dépendance sur une adresse IP fixe, utilisez une adresse IP réservée.

Vous ne pouvez pas convertir un disque non géré vers un disque géré s’il se trouve dans un compte de stockage qui est, ou qui a été à un moment donné, chiffré à l’aide [d’Azure SSE (Storage Service Encryption)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Les étapes suivantes décrivent comment convertir des disques non gérés qui sont, ou ont été, dans un compte de stockage chiffré :

- [Copiez le disque dur virtuel (VHD)](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unmanaged-disks) avec la commande [az storage blob copy start](/cli/azure/storage/blob/copy#start) dans un compte de stockage qui n’a jamais été activé pour Azure Storage Service Encryption.
- Créez une machine virtuelle qui utilise des disques gérés et spécifiez ce fichier de disque dur virtuel lors de la création avec la commande [az vm create](/cli/azure/vm#create), ou
- Attachez le disque dur virtuel copié avec la commande [az vm disk attach](/cli/azure/vm/disk#attach) à une machine virtuelle en cours d’exécution avec des disques gérés.

## <a name="convert-vm-to-azure-managed-disks"></a>Convertir une machine virtuelle vers Azure Managed Disks
Cette section explique comment convertir vos machines virtuelles Azure existantes à partir de disques non gérés en disques gérés. Vous pouvez utiliser ce processus pour convertir des disques non gérés Premium (SSD) en disques gérés Premium, ou des disques non gérés Standard (HDD) en disques gérés Standard. 

> [!IMPORTANT]
> Après avoir effectué la procédure suivante, un seul objet blob de blocs reste dans le conteneur de disques durs virtuels par défaut. Le nom du fichier est : « VMName.xxxxxxx.status ». Ne supprimez pas cet objet d’état restant. Les étapes suivantes devraient résoudre ce problème.

1. Libérez la machine virtuelle avec la commande [az vm deallocate](/cli/azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertissez la machine virtuelle afin qu’elle utilise des disques gérés avec la commande [az vm convert](/cli/azure/vm#convert). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques gérés effectuée avec la commande [az vm start](/cli/azure/vm#start). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vm-in-an-availability-set-to-managed-disks"></a>Convertir une machine virtuelle dans un groupe à haute disponibilité en disques gérés

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

Toutes les machines virtuelles dans le groupe à haute disponibilité doivent être libérées avant de convertir le groupe à haute disponibilité. Prévoyez de convertir toutes les machines virtuelles en disques gérés une fois que la disponibilité a été elle-même convertie en groupe à haute disponibilité géré. Vous pouvez ensuite démarrer toutes les machines virtuelles et poursuivre un fonctionnement normal.

1. Répertoriez toutes les machines virtuelles contenues dans un groupe à haute disponibilité avec la commande [az vm availability-set list](/cli/azure/vm/availability-set#list). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set show --resource-group myResourceGroup \
        --name myAvailabilitySet --query [virtualMachines[*].id] --output table
    ```

2. Libérez toutes les machines virtuelles avec la commande [az vm deallocate](/cli/azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertissez le groupe à haute disponibilité avec la commande [az vm availability-set convert](/cli/azure/vm/availability-set#convert). L’exemple suivant convertit le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm availability-set convert --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertissez toutes les machines virtuelles en disques gérés avec la commande [az vm convert](/cli/azure/vm#convert). Le processus suivant convertit la machine virtuelle nommée `myVM`, y compris le disque du système d’exploitation et tous les disques de données :

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Démarrez toutes les machines virtuelles une fois la conversion en disques gérés effectuée avec la commande [az vm start](/cli/azure/vm#start). L’exemple suivant démarre la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les options de stockage, voir la page [Azure Managed Disks overview](../storage/storage-managed-disks-overview.md) (Vue d’ensemble d’Azure Managed Disks).

