---
title: "Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés - Azure Managed Disks | Microsoft Docs"
description: "Conversion d’une machine virtuelle Windows qui utilise des disques non gérés pour qu’elle utilise des disques gérés à l’aide de PowerShell dans le modèle de déploiement Resource Manager"
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
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 8e6950fb44207824baf18d666db7ba26f0e360f0
ms.contentlocale: fr-fr
ms.lasthandoff: 07/12/2017

---

# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertir les disques non gérés d’une machine virtuelle Windows en disques gérés

Si vos machines virtuelles Windows existantes utilisent des disques non gérés, vous pouvez les convertir pour qu’elles utilisent des disques gérés par le biais du service [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Ce processus convertit le disque du système d’exploitation ainsi que tous les autres disques de données attachés.

Cet article explique comment convertir des machines virtuelles à l’aide d’Azure PowerShell. Si vous devez installer ou mettre à niveau ce dernier, consultez [Installer et configurer Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Avant de commencer


* Consultez [Planification de la migration vers Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertir des machines virtuelles à instance unique
Cette section explique comment convertir vos machines virtuelles Azure à instance unique à partir de disques non gérés vers des disques gérés. (Si vos machines virtuelles sont dans un groupe à haute disponibilité, voir la section suivante.) 

1. Libérez la machine virtuelle à l’aide de l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L’exemple suivant libère la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` : 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertissez la machine virtuelle pour qu’elle utilise des disques gérés à l’aide de l’applet de commande [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Le processus suivant convertit la machine virtuelle précédente, y compris le disque du système d’exploitation et tous les disques de données :

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Démarrez la machine virtuelle une fois la conversion vers les disques gérés effectuée à l’aide de la commande [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). L’exemple suivant redémarre la machine virtuelle précédente :

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Convertir des machines virtuelles dans un groupe à haute disponibilité

Si les machines virtuelles que vous souhaitez convertir pour utiliser des disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en groupe à haute disponibilité géré.

1. Convertissez le groupe à haute disponibilité à l’aide de l’applet de commande [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L’exemple suivant met à jour le groupe à haute disponibilité nommé `myAvailabilitySet` dans le groupe de ressources nommé `myResourceGroup` :

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

2. Désallouer et convertir des machines virtuelles dans le groupe à haute disponibilité. Le script suivant libère chaque machine virtuelle à l’aide de l’applet de commande [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la convertit à l’aide de [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) et la redémarre à l’aide de [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm) :

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


## <a name="convert-standard-managed-disks-to-premium"></a>Convertir des disques gérés standard en premium
Une fois que vous avez converti votre machine virtuelle vers des disques gérés, vous pouvez passer d’un type de stockage à un autre. Vous pouvez également utiliser un mélange de disques qui utilisent le stockage standard et le stockage premium. 

Dans l’exemple suivant, nous montrons comment passer du stockage standard au stockage premium. Pour utiliser des disques gérés premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](sizes.md) qui prend en charge le stockage premium. Cet exemple passe également à une taille prenant en charge le stockage premium.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change the VM size to a size that supports premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
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

## <a name="troubleshooting"></a>Résolution de problèmes

Si une erreur survient lors de la conversion, ou si une machine virtuelle est dans un état d’échec en raison de problèmes dans une conversion précédente, exécutez l’applet de commande `ConvertTo-AzureRmVMManagedDisk` à nouveau. Généralement, une simple nouvelle tentative suffit à débloquer la situation.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Disques gérés et chiffrement du service Stockage Azure

Vous ne pouvez pas utiliser les étapes précédentes pour convertir un disque non géré en disque géré s’il se trouve dans un compte de stockage qui a déjà été chiffré à l’aide d’[Azure SSE (Storage Service Encryption)](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Les étapes suivantes décrivent comment copier et utiliser des disques non gérés qui ont été dans un compte de stockage chiffré :

1. Copiez le VHD à l’aide d’[AzCopy](../../storage/storage-use-azcopy.md) dans un compte de stockage pour lequel Azure Storage Service Encryption n’a jamais été activé.

2. Utilisez la machine virtuelle copiée de l’une des manières suivantes :

   * Créez une machine virtuelle qui utilise des disques gérés et spécifiez ce fichier VHD lors de la création à l’aide de la commande `New-AzureRmVm`.

   * Attachez le VHD copié à l’aide de la commande `Add-AzureRmVmDataDisk` à une machine virtuelle en cours d’exécution qui utilise des disques gérés.


## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](snapshot-copy-managed-disk.md).


