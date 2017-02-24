---
title: "Copier une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire) | Microsoft Docs"
description: "Découvrez comment créer une copie de votre machine virtuelle Linux Azure dans le modèle de déploiement Resource Manager à l’aide d’Azure CLI 2.0 (version préliminaire)"
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
ms.sourcegitcommit: 344949f10929d9a1ba8f63c5b30f242ddc66fdec
ms.openlocfilehash: 967e2bafdc702c9ddba634089899afee382c5b71


---
# <a name="create-a-copy-of-a-linux-virtual-machine-with-the-azure-cli-20-preview"></a>Créer une copie d’une machine virtuelle Linux à l’aide d’Azure CLI 2.0 (version préliminaire)
Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Linux dans le modèle de déploiement Resource Manager. Copiez tout d’abord les disques du système d’exploitation et les disques de données dans un nouveau conteneur, configurez les ressources réseau puis créez la nouvelle machine virtuelle.

Vous pouvez également [charger et créer une machine virtuelle à partir d’une image de disque personnalisée](virtual-machines-linux-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](virtual-machines-linux-copy-vm-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager
- Azure CLI 2.0 (version préliminaire) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager (cet article)

## <a name="prerequisites"></a>Composants requis
- Vous devez disposer de la dernière version [d’Azure CLI 2.0 (version préliminaire)](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#login).
- Vous avez besoin d’une machine virtuelle Azure à utiliser comme source lorsque vous créez une copie.

## <a name="stop-the-vm"></a>Arrêtez la machine virtuelle.
Libérez la machine virtuelle source à l’aide de la commande [az vm deallocate](/cli/azure/vm#deallocate). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

## <a name="copy-the-vm"></a>Copier la machine virtuelle
Pour copier une machine virtuelle, vous devez créer une copie du disque dur virtuel sous-jacent. Ce processus vous permet de créer une machine virtuelle spécialisée qui contient la même configuration et les mêmes paramètres que la machine virtuelle source. Le processus de copie du disque virtuel varie selon que le système utilise des disques gérés Azure (Azure Managed Disks) ou des disques non gérés. Les disques gérés sont traités par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Les disques gérés sont des ressources de niveau supérieur et sont donc plus faciles à utiliser. Vous pouvez effectuer une copie directe de la ressource de disque. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../storage/storage-managed-disks-overview.md). Choisissez l’une des étapes suivantes selon le type de stockage de votre machine virtuelle source :

- [Disques gérés](#managed-disks)
- [Disques non gérés](#unmanaged-disks) 

### <a name="managed-disks"></a>Disques gérés
Répertoriez chaque machine virtuelle et le nom de leur disque de système d’exploitation géré avec la commande [az vm list](/cli/azure/vm#list). L’exemple suivant répertorie toutes les machines virtuelles dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm list -g myTestRG --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' --output table
```

Le résultat ressemble à l’exemple suivant :

```azurecli
Name    DiskName
------  --------
myVM    myDisk
```

Copiez le disque en créant un nouveau disque géré avec la commande [az disk create](/cli/azure/disk#create). L’exemple suivant crée un disque nommé `myCopiedDisk` à partir du disque géré nommé `myDisk` :

```azurecli
az disk create --resource-group myResourceGroup --name myCopiedDisk --source myDisk
``` 

Affichez les disques gérés qui appartiennent désormais à votre groupe de ressources avec la commande [az disk list](/cli/azure/disk#list). L’exemple suivant répertorie les disques gérés dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az disk list --resource-group myResourceGroup --output table
```

Accédez à la rubrique [Création et vérification des paramètres du réseau virtuel](#set-up-the-virtual-network).


### <a name="unmanaged-disks"></a>Disques non gérés
Pour créer une copie d’un disque dur virtuel, vous devez disposer des clés du compte de stockage et de l’URI du disque. Utilisez la commande [az storage account keys list](/cli/azure/storage/account/keys#list) pour afficher les clés du compte de stockage. L’exemple suivant répertorie les clés du compte de stockage nommé `mystorageaccount` dans le groupe de ressources nommé `myResourceGroup`

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

Utilisez la commande [az vm list](/cli/azure/vm#list) pour afficher la liste des machines virtuelles et de leur URI. L’exemple suivant répertorie les machines virtuelles dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm list -g myResourceGroup --query '[].{Name:name,URI:storageProfile.osDisk.vhd.uri}' --output table
```

Le résultat ressemble à l’exemple suivant :

```azurecli
Name    URI
------  -------------------------------------------------------------
myVM    https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd
```

Copiez le disque dur virtuel à l’aide de la commande [az storage blob copy start](/cli/azure/storage/blob/copy#start). Utilisez les informations obtenues à l’aide des commandes **az storage account keys list** et **az vm list** pour fournir les clés du compte de stockage et l’URI du disque dur virtuel requis.

```azurecli
az storage blob copy start \
    --account-name mystorageaccount \
    --account-key gi7crXhD8PMs8DRWqAM7fAjQEFmENiGlOprNxZGbnpylsw/nq+lQQg0c4jiKoV3Nytr3dAiXZRpL8jflpAr2Ug== \
    --source-uri https://mystorageaccount.blob.core.windows.net/vhds/myVHD.vhd \
    --destination-container vhds --destination-blob myCopiedVHD.vhd
```

## <a name="set-up-the-virtual-network"></a>Configurer le réseau virtuel
Les étapes suivantes consistent à créer un nouveau réseau virtuel, un nouveau sous-réseau, une nouvelle adresse IP publique et une nouvelle carte réseau virtuelle. Ces étapes sont facultatives. Si vous copiez une machine virtuelle à des fins de dépannage ou dans le cadre de déploiements supplémentaires, vous pouvez ne pas vouloir utiliser une machine virtuelle hébergée dans un réseau virtuel existant. Si vous souhaitez créer une infrastructure de réseau virtuel pour vos machines virtuelles copiées, suivez ces quelques étapes. Sinon, passez directement à la section [Créer une machine virtuelle](#create-a-vm).

Créez le réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#create). L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` et un sous-réseau nommé `mySubnet` :

```azurecli
az network vnet create --resource-group myResourceGroup --location westus --name myVnet \
    --address-prefix 192.168.0.0/16 --subnet-name mySubnet --subnet-prefix 192.168.1.0/24
```

Créez une adresse IP publique avec la commande [az network public-ip create](/cli/azure/network/public-ip#create). L’exemple suivant permet de créer une adresse IP publique nommée `myPublicIP` avec le nom DNS de `mypublicdns`. (Le nom DNS doit être unique ; choisissez donc un autre nom qui n’est pas susceptible d’être déjà utilisé.)

```azurecli
az network public-ip create --resource-group myResourceGroup --location westus \
    --name myPublicIP --dns-name mypublicdns --allocation-method static --idle-timeout 4
```

Créez la carte d’interface réseau avec la commande [az network nic create](/cli/azure/network/nic#create). L’exemple suivant permet de créer une carte d’interface réseau nommée `myNic` et associée au sous-réseau `mySubnet` :

```azurecli
az network nic create --resource-group myResourceGroup --location westeurope --name myNic \
    --vnet-name myVnet --subnet mySubnet
```

## <a name="create-a-vm"></a>Créer une machine virtuelle
Vous pouvez désormais créer une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). Comme pour la copie de disque, le processus varie selon que le système utilise des disques gérés ou non gérés. Créez une machine virtuelle en utilisant l’une des commandes suivantes.

### <a name="managed-disks"></a>Disques gérés
Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). Spécifiez le disque géré copié à utiliser en tant que disque du système d’exploitation (`--attach-os-disk`), tel que défini ci-dessous :

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image UbuntuLTS
    --attach-os-disk myCopiedDisk
```

### <a name="unmanaged-disks"></a>Disques non gérés
Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). Spécifiez le compte de stockage, le nom du conteneur et le disque dur virtuel que vous avez utilisé lors de la création de la copie à l’aide de la commande **az storage blob copy start** (`--image`), tel que défini ci-dessous :

```azurecli
az vm create --resource-group myResourceGroup --name myCopiedVM  \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic --size Standard_DS1_v2 --os-type Linux \
    --image https://mystorageaccount.blob.core.windows.net/vhds/myCopiedVHD.vhd \
    --use-unmanaged-disk
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer votre nouvelle machine virtuelle, voir [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager](azure-cli-arm-commands.md).




<!--HONumber=Feb17_HO2-->


