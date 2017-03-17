---
title: "Développement d’un disque de données joint à une machine virtuelle Windows dans Azure | Microsoft Docs"
description: "Augmentez la taille d’un disque de données joint à une machine virtuelle Windows à l’aide de PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: f7816f6e2120c02d970de640b397a1d2d570e35d
ms.lasthandoff: 03/04/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Augmenter la taille d’un disque de données joint à une machine virtuelle Windows

Si vous avez besoin d’augmenter la taille du disque de données associé à votre machine virtuelle, vous pouvez augmenter la taille à l’aide de PowerShell. Après avoir augmenté la taille du disque de données dans les paramètres de la machine virtuelle Azure, vous devez également affecter le nouvel espace de disque au sein de la machine virtuelle.


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Utilisation de Powershell pour augmenter la taille d’un disque de données gérées

Pour augmenter la taille d’un disque de données géré, utilisez les applets de commande PowerShell suivants :

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/Get-AzureRMReseourceGroup) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |
<br>

Le script suivant vous aidera à obtenir les informations sur la machine virtuelle en sélectionnant le disque de données et en spécifiant la nouvelle taille.

```powershell
# Select resource group
     
    $rg = Get-AzureRMReseourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru
     
    $rgName = $rg.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk
     
    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru
    
# Specify a larger size for the data disk 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size
    
    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)
    
    $vm.StorageProfile.DataDisks

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    
    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Utilisation de PowerShell pour augmenter la taille d’un disque de données non géré

Pour augmenter la taille des disques de données non gérés dans un compte de stockage, utilisez les applets de commande PowerShell suivantes :

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/Get-AzureRMStorageAccount) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |

<br>

Le script suivant vous aidera à obtenir les informations sur la machine virtuelle et le compte de stockage en sélectionnant le disque de données et en spécifiant la nouvelle taille.

```powershell

# Select Azure Storage Account
     
    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru
     
    $rgName = $storageAccount.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize
     
    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru
     
    
# Specify a larger data disk size 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name
    
```

## <a name="allocate-the-unallocated-disk-space"></a>Allocation de l’espace disque non alloué 

Une fois que vous avez rendu le lecteur plus volumineux, vous devez allouer le nouvel espace non alloué à partir de la machine virtuelle. Pour allouer l’espace, vous pouvez vous connecter à la machine virtuelle à l’aide de Disk Management (diskmgmt.msc). Ou, si vous avez activé WinRM et un certificat sur la machine virtuelle lors de sa création, vous pouvez utiliser PowerShell à distance pour initialiser le disque. Vous pouvez également utiliser une extension de script personnalisée : 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
Le fichier de script peut contenir les éléments de code suivants pour augmenter l’allocation de disque à la taille maximale :

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>Étapes suivantes
- [En savoir plus sur les disques et disques durs virtuels](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
