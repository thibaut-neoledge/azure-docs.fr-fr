---
title: "Copie d’une machine virtuelle Linux à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment créer une copie de votre machine virtuelle Linux Azure dans le modèle de déploiement Resource Manager à l’aide d’Azure CLI 2.0 (version préliminaire)."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1beaca707bfc5058083213b564b3898545f57556
ms.openlocfilehash: edf98951d4ed6709a9894e36ec5ae80b589dd639
ms.lasthandoff: 02/27/2017


---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-20-preview"></a>Créer une copie de machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire)
Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Linux dans le modèle de déploiement Resource Manager.

Copiez les disques du système d’exploitation et des données sur un nouveau conteneur, configurez les ressources réseau puis créez la machine virtuelle.

Vous pouvez également [charger et créer une machine virtuelle à partir d’une image de disque personnalisée](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions"></a>Versions CLI
Vous pouvez mener à bien la tâche en utilisant l’une des versions suivantes de l’interface CLI :

* [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : pour les modèles de déploiement Classic et Resource Manager.
* Azure CLI 2.0 (version préliminaire) : l’interface CLI nouvelle génération pour le modèle de déploiement Resource Manager (cet article).

## <a name="prerequisites"></a>Composants requis
* [Azure CLI 2.0 (version préliminaire)](/cli/azure/install-az-cli2), installée et connectée à un compte Azure avec la commande [az login](/cli/azure/#login).
* Une machine virtuelle Azure à utiliser en tant que source pour votre copie.

## <a name="step-1-stop-the-source-vm"></a>Étape 1 : Arrêter la machine virtuelle source
Libérez la machine virtuelle source à l’aide de la commande [az vm deallocate](/cli/azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="step-2-copy-the-source-vm"></a>Étape 2 : Copier la machine virtuelle source
Pour copier une machine virtuelle, vous devez créer une copie du disque dur virtuel sous-jacent. Ce processus vous permet de créer une machine virtuelle spécialisée qui contient la même configuration et les mêmes paramètres que la machine virtuelle source.

Le processus de copie d’un disque virtuel n’est pas identique pour Azure Managed Disks et les disques non gérés. Les disques gérés sont traités par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Les disques gérés étant une ressource de niveau supérieur, ils sont plus faciles à manipuler. Autrement dit, vous pouvez effectuer une copie directe de la ressource de disque.

Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md).

Selon le disque de stockage de votre machine virtuelle source, suivez les instructions des 2 prochaines sections, puis accédez à l’« Étape 3 : Configurer un réseau virtuel ».

### <a name="managed-disks"></a>Disques gérés

1. Répertoriez chaque machine virtuelle et le nom de leur disque de système d’exploitation géré avec la commande [az vm list](/cli/azure/vm#list). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
```

2. Pour copier le disque, créez un nouveau disque géré à l’aide de la commande [az disk create](/cli/azure/disk#create). L’exemple suivant permet de créer un disque `myCopiedDisk` à partir du disque géré `myDisk` :

    ```azurecli
    az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
    ```

3. Examinez les disques gérés qui appartiennent désormais à votre groupe de ressources, à l’aide de la commande [az disk list](/cli/azure/disk#list). L’exemple suivant répertorie les disques gérés dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```

4. Passer à l’[Étape 3 : Configurer un réseau virtuel](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Disques non gérés

1. Pour créer une copie d’un disque dur virtuel, vous devez disposer d’un compte de stockage Azure et de l’URI du disque. Utilisez la commande [az storage account keys list](/cli/azure/storage/account/keys#list) pour afficher les clés du compte de stockage.

 L’exemple suivant répertorie les clés associées au compte de stockage `mystorageaccount` dans le groupe de ressources `myResourceGroup` :

    ```azurecli
    az storage account keys list --resource-group myResourceGroup \
        --name mystorageaccount --output table
    ```

 Le résultat ressemble à l’exemple suivant :

    ```azurecli
    KeyName    Permissions    Value
    ---------  -------------  ----------------------------------------------------------------------------------------
    key1       Full           gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug==
    key2       Full           UMoyQjWuKCBUAVDr1ANRe/IWTE2o0ZdmQH2JSZzXKNmDKq83368Fw9nyTBcTEkSKI9cm5tlTL8J15ArbPMo8eA==
    ```

2. Utilisez la commande [az vm list](/cli/azure/vm#list) pour afficher une liste des machines virtuelles et de leur URI. L’exemple suivant répertorie les machines virtuelles dans le groupe de ressources nommé `myResourceGroup` :

    ```azurecli
    az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
    ```

    Le résultat ressemble à l’exemple suivant :

    ```azurecli
    Name    URI
    ------  -------------------------------------------------------------
    myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
    ```

3. Exécutez la commande [az storage blob copy start](/cli/azure/storage/blob/copy#start) pour copier le disque dur virtuel. Pour fournir les clés de compte de stockage et les URI du disque dur virtuel requis, valorisez les données des listes `az storage account keys` et `az vm`.

    ```azurecli
    az storage blob copy start \
        --account-name mystorageaccount \
        --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
        --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
        --destination-container vhds --destination-blob myCopiedVHD.vhd
    ```

## <a name="step-3-set-up-a-virtual-network"></a>Étape 3 : Configurer un réseau virtuel
Les étapes facultatives suivantes consistent à créer un nouveau réseau virtuel, un nouveau sous-réseau, une nouvelle adresse IP publique et une nouvelle carte réseau virtuelle.

Si vous copiez une machine virtuelle à des fins de dépannage ou dans le cadre de déploiements supplémentaires, vous pouvez ne pas vouloir utiliser une machine virtuelle hébergée dans un réseau virtuel existant.

Si vous souhaitez créer une infrastructure de réseau virtuel pour vos machines virtuelles copiées, exécutez les procédures suivantes. Si vous ne souhaitez pas créer de réseau virtuel, passez à l’[Étape 4 : Créer une machine virtuelle](#create-a-vm).

1. Exécutez la commande [az network vnet create](/cli/azure/network/vnet#create) pour créer le réseau virtuel. L’exemple suivant permet de créer un réseau virtuel `myVnet` et un sous-réseau `mySubnet` :

    ```azurecli
    az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
        --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
    ```

2. Exécutez la commande [az network public-ip create](/cli/azure/network/public-ip#create) pour créer une adresse IP publique. L’exemple suivant permet de créer une adresse IP publique `myPublicIP` avec le nom DNS `mypublicdns`. (Le nom DNS doit être unique, fournissez donc votre propre nom unique.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup --location westus \
        --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
    ```

3. Exécutez la commande [az network nic create](/cli/azure/network/nic#create) pour créer la carte réseau. L’exemple suivant permet de créer une carte réseau nommée `myNic` et associée au sous-réseau `mySubnet` :

    ```azurecli
    az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
        --vnet-name myVnet --subnet mySubnet
    ```

## <a name="step-4-create-a-vm"></a>Étape 4 : Créer une machine virtuelle
Vous pouvez désormais créer une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). Comme lorsque vous copiez un disque, le processus diffère légèrement selon qu’il s’agisse de disques gérés ou de disques non gérés. En fonction du type de stockage de votre machine virtuelle source, suivez les instructions de l’une des&2; prochaines sections.

### <a name="managed-disks"></a>Disques gérés
1. Exécutez la commande [az vm create](/cli/azure/vm#create) pour créer une machine virtuelle.
2. Spécifiez le disque géré copié à utiliser en tant que disque du système d’exploitation (`--attach-os-disk`), tel que défini ci-dessous :

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image UbuntuLTS
        --attach-os-disk myCopiedDisk
    ```

### <a name="unmanaged-disks"></a>Disques non gérés
1. Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create).
2. Spécifiez le compte de stockage, le nom de conteneur et le disque dur virtuel que vous avez utilisés lors de la création du disque copié `az storage blob copy start` (`--image`), comme suit :

    ```azurecli
    az vm create --resource-group myResourceGroup --name myCopiedVM  \
        --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
        --nics myNic --size Standard_DS1_v2 --os-type Linux \
        --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
        --use-unmanaged-disk
    ```

## <a name="next-steps"></a>Étapes suivantes
Pour découvrir comment utiliser l’interface Azure CLI pour gérer votre nouvelle machine virtuelle, consultez la page [Commande de l’interface de ligne de commande Azure en mode Azure Resource Manager](azure-cli-arm-commands.md).

