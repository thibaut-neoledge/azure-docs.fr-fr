---
title: "Convertir une machine virtuelle Windows à partir de disques non gérés vers des disques gérés - Azure | Microsoft Docs"
description: "Guide de conversion d’une machine virtuelle Windows à partir de disques non gérés en Azure Managed Disks à l’aide de PowerShell dans le modèle de déploiement Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Conversion d’une machine virtuelle Windows à partir de disques non gérés vers Azure Managed Disks

Si vous avez des machines virtuelles Windows existantes qui utilisent des disques non gérés, vous pouvez convertir ces machines virtuelles pour qu’elles utilisent [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Cet article explique comment convertir des machines virtuelles avec Azure PowerShell. Si vous devez installer ou mettre à niveau, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Avant de commencer


* Consultez [Planification de la migration vers Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non gérés en disques gérés. (Voir la section suivante si vos machines virtuelles sont dans un groupe à haute disponibilité). 

1. Libérez la machine virtuelle avec l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` : 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertissez la machine virtuelle en disques gérés avec l’applet de commande [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Le processus suivant convertit la machine virtuelle précédente, y compris le disque du système d’exploitation et tous les disques de données :

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques gérés effectuée avec la commande [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). L’exemple suivant redémarre la machine virtuelle précédente :

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

1. Convertissez le groupe à haute disponibilité avec l’applet de commande [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L’exemple suivant met à jour le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Si la région où votre groupe à haute disponibilité se situe n’a que 2 domaines d’erreur gérés, mais que le nombre de domaines d’erreur non gérés est de 3, cette commande affiche une erreur semblable à « Le nombre de domaines d’erreur spécifié 3 doit être compris entre 1 et 2 ». Pour résoudre l’erreur, mettez à jour le domaine par défaut sur 2 et `Sku` sur `Aligned`, comme suit :

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Désallouer et convertir des machines virtuelles dans le groupe à haute disponibilité. Le script suivant libère chaque machine virtuelle avec l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la convertit avec [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) et la redémarre avec [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Convertir des disques gérés standards pour le stockage Premium
Une fois que vous avez converti votre machine virtuelle en disques gérés, vous pouvez également basculer entre les types de stockage. Vous pouvez également utiliser un mélange de disques qui utilisent le stockage Standard et le stockage Premium. Dans l’exemple suivant, nous montrons comment passer du stockage normal au stockage Premium. Pour utiliser des disques gérés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage Premium. Cet exemple passe également à une taille prenant en charge le stockage Premium.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>résolution des problèmes

Si une erreur survient lors de la conversion, ou si une machine virtuelle est dans un état d’échec en raison de problèmes dans une conversion précédente, exécutez l’applet de commande `ConvertTo-AzureRmVMManagedDisk` à nouveau. Généralement, une simple nouvelle tentative suffit à débloquer la situation.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Disques gérés et chiffrement du service de stockage Azure

Vous ne pouvez pas utiliser les étapes précédentes pour convertir un disque non géré vers un disque géré s’il se trouve dans un compte de stockage qui a été chiffré à l’aide [d’Azure SSE (Storage Service Encryption)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) à un moment donné. Les étapes suivantes décrivent comment copier et utiliser des disques non gérés ont été dans un compte de stockage chiffré :

1. Copiez le disque dur virtuel (VHD) avec [AzCopy](../../storage/storage-use-azcopy.md) dans un compte de stockage pour lequel Azure Storage Service Encryption n’a jamais été activé.

2. Utilisez la machine virtuelle copiée d’une des manières suivantes :

  * Créez une machine virtuelle qui utilise des disques gérés et spécifiez ce fichier de disque dur virtuel lors de la création avec la commande `New-AzureRmVm`

  * Attachez le disque dur virtuel copié avec la commande `Add-AzureRmVmDataDisk` à une machine virtuelle en cours d’exécution avec des disques gérés


## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).


