---
title: Convertir le stockage Managed Disks Azure de standard en premium, et vice versa | Microsoft Docs
description: "Comment convertir le stockage Managed Disks Azure de standard en premium, et vice versa, à l’aide de l’interface de ligne de commande Azure."
services: virtual-machines-linux
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 0380b4aaa23b4aaba4c67d05e2d62f3ef41d6a32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Convertir le stockage Managed Disks Azure de standard en premium, et vice versa

Managed Disks propose deux options de stockage : [Premium](../../storage/storage-premium-storage.md) (SSD) et [Standard](../../storage/storage-standard-storage.md) (HDD). Il vous permet de basculer facilement entre les deux options avec une interruption minimale adaptée à vos besoins de performances. Cette fonctionnalité n’est pas disponible pour les disques non gérés. Toutefois, vous pouvez facilement [effectuer des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) pour basculer facilement entre les deux options.

Cet article vous montre comment convertir des disques gérés de standard en premium, et vice versa, à l’aide de l’interface de ligne de commande Azure. Si vous devez installer ou mettre à niveau l’interface, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Avant de commencer

* La conversion nécessite un redémarrage de la machine virtuelle. Par conséquent, planifiez la migration de vos stockages sur disques au cours d’une fenêtre de maintenance préexistante. 
* Si vous utilisez des disques non gérés, tout d’abord [effectuez des conversions en disques gérés](convert-unmanaged-to-managed-disks.md) afin d’utiliser cet article pour basculer entre les deux options de stockage. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Convertir tous les disques gérés d’une machine virtuelle de standard en premium, et vice versa

Dans l’exemple suivant, nous montrons comment faire passer tous les disques d’une machine virtuelle du stockage standard au stockage premium. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Convertir un disque géré de standard en premium, et vice versa

Pour votre charge de travail de développement/test, vous souhaiterez peut-être mélanger disques standard et disques premium pour réduire les coûts. Vous pouvez le faire en effectuant une mise à niveau vers le stockage premium, uniquement pour les disques qui nécessitent de meilleures performances. Dans l’exemple suivant, nous montrons comment faire passer un seul disque d’une machine virtuelle du stockage standard au stockage premium, et vice versa. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).

