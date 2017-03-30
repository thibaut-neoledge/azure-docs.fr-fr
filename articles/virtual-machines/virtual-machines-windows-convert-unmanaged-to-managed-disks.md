---
title: "Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés - Azure | Microsoft Docs"
description: "Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés à l’aide de PowerShell dans le modèle de déploiement Resource Manager"
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
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 96137d49d3caa0444400edcfcfaa097dc71ba8e3
ms.lasthandoff: 03/25/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés

Si vous avez des machines virtuelles Azure existantes qui utilisent des disques non gérés dans des comptes de stockage et que vous souhaitez tirer parti des [disques gérés](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), vous pouvez convertir les machines virtuelles. Le processus convertit à la fois le disque du système d’exploitation et les disques de données attachés de disques non gérés dans un compte de stockage vers des disques gérés. Les machines virtuelles sont arrêtées et libérées. Vous utilisez ensuite Powershell pour convertir la machine virtuelle afin qu’elle utilise des disques gérés. Après la conversion, vous redémarrez la machine virtuelle, qui utilisera désormais des disques gérés.

Avant de commencer, n’oubliez pas d’étudier la [planification de la migration vers des disques gérés](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
Testez le processus de migration en migrant une machine virtuelle de test avant d’effectuer la migration de production, car le processus de migration n’est pas réversible.


> [!IMPORTANT] 
> Lors de la conversion, vous libèrerez la machine virtuelle. La libération de la machine virtuelle signifie qu’elle se verra attribuer une nouvelle adresse IP lorsqu’elle redémarrera après la conversion. Si vous avez une dépendance sur une adresse IP fixe, vous devez utiliser une adresse IP réservée.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Disques gérés et chiffrement du service de stockage Azure (SSE)

Vous ne pouvez pas convertir une machine virtuelle non gérée créée dans le modèle de déploiement Resource Manager vers des disques gérés si l’un des disques non gérés attachés fait partie d’un compte de stockage qui est, ou a été, chiffré à l’aide du [chiffrement du service de stockage Azure (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Les étapes suivantes décrivent comment convertir une machine virtuelle non gérée qui est, ou a été, dans un compte de stockage chiffré :

**Disques de données** :
1.    Détachez le disque de données de la machine virtuelle.
2.    Copiez le disque dur virtuel sur un compte de stockage sur lequel le chiffrement SSE n’a jamais été activé. Pour copier le disque vers un autre compte de stockage, utilisez [AzCopy](../storage/storage-use-azcopy.md) : `AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    Attachez le disque copié à la machine virtuelle et convertissez cette dernière.

**Disque du système d’exploitation** :
1.    Arrêtez la libération de la machine virtuelle. Si nécessaire, enregistrez la configuration de la machine virtuelle.
2.    Copiez le disque dur virtuel du système d’exploitation sur un compte de stockage sur lequel le chiffrement SSE n’a jamais été activé. Pour copier le disque vers un autre compte de stockage, utilisez [AzCopy](../storage/storage-use-azcopy.md) : `AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    Créez une machine virtuelle qui utilise des disques gérés et, lors de sa création, attachez ce fichier de disque dur virtuel en tant que disque du système d’exploitation.

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>Convertir des machines virtuelles d’un groupe à haute disponibilité vers des disques gérés dans un groupe à haute disponibilité géré

Si les machines virtuelles que vous souhaitez convertir en disques gérés se trouvent dans un groupe à haute disponibilité, vous devez tout d’abord convertir ce dernier en un groupe à haute disponibilité géré.

Le script suivant met à jour le groupe à haute disponibilité pour qu’il devienne un groupe à haute disponibilité géré, puis libère et convertit les disques, avant de redémarrer chaque machine virtuelle du groupe à haute disponibilité.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>Convertir des machines virtuelles Azure existantes vers des disques gérés utilisant le même type de stockage

> [!IMPORTANT]
> Après avoir effectué la procédure suivante, un seul objet blob reste dans le conteneur de disques durs virtuels par défaut. Le nom du fichier est : « VMName.xxxxxxx.status ». Ce fichier est créé par Azure uniquement lorsque vous avez installé les [extensions de machine virtuelle](windows/classic/agents-and-extensions.md) sur la machine virtuelle. Ne supprimez pas cet objet d’état restant. Les étapes suivantes résoudront ce problème.

Cette section explique comment convertir vos machines virtuelles Azure existantes de disques non gérés dans des comptes de stockage vers des disques gérés en utilisant le même type de stockage. Vous pouvez utiliser ce processus pour passer de disques non gérés Premium (SSD) à des disques gérés Premium, ou de disques non gérés Standard (HDD) à des disques gérés Standard. 

1. Créez des variables et libérez la machine virtuelle. Cet exemple définit le nom du groupe de ressources sur **myResourceGroup** et le nom de la machine virtuelle sur **myVM**.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    *L’état* de la machine virtuelle dans le Portail Azure passe de **Arrêté** à **Arrêté (libéré)**.
    
2. Convertissez tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et les disques de données.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Migrer des machines virtuelles Azure existantes utilisant des disques non gérés Standard vers des disques gérés Premium

Cette section vous expliquera comment convertir vos machines virtuelles Azure existantes utilisant des disques non gérés Standard en disques gérés Premium. Pour utiliser des disques gérés Premium, votre machine virtuelle doit utiliser une [taille de machine virtuelle](virtual-machines-windows-sizes.md) qui prend en charge le stockage Premium.


1.  Tout d’abord, définissez les paramètres communs. Assurez-vous que la [taille de machine virtuelle](virtual-machines-windows-sizes.md) que vous sélectionnez prend en charge le stockage Premium.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  Obtenir la machine virtuelle avec des disques non gérés

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  Arrêtez (libérez) la machine virtuelle.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  Mettez à jour la taille de la machine virtuelle par rapport à la capacité de stockage Premium disponible dans la région où se trouve la machine virtuelle.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  Convertissez la machine virtuelle avec des disques non gérés vers des disques gérés. 

    Si une erreur interne du serveur s’affiche, veuillez réessayer 2 à 3 fois avant de contacter notre équipe de support.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. Arrêtez (libérez) la machine virtuelle.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  Mettez à niveau tous les disques vers le stockage Premium.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. Démarrez la machine virtuelle.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
Vous pouvez également utiliser un mélange de disques qui utilisent le stockage Standard et le stockage Premium.
    

## <a name="next-steps"></a>Étapes suivantes

Créez une copie en lecture seule d’une machine virtuelle en utilisant des [captures instantanées](virtual-machines-windows-snapshot-copy-managed-disk.md).


