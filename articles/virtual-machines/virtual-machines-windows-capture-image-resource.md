---
title: "Créer une image managée dans Azure | Microsoft Docs"
description: "Créer une image managée d’une machine virtuelle ou d’un disque dur virtuel généralisé(e) dans Azure. Les images peuvent être utilisées pour créer différentes machines virtuelles utilisant des disques gérés."
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
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: 12b2d12c5152c6e9b8f2e8c78fbbd6663cb24e45


---
# <a name="how-to-capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Procédure de capture d’une image managée d’une machine virtuelle généralisée dans Azure

Une ressource d’image managée peut être créée à partir d’une machine virtuelle généralisée stockée comme un disque géré ou non géré dans un compte de stockage. L’image peut ensuite être utilisée pour créer différentes machines virtuelles utilisant des disques gérés pour le stockage. 


## <a name="prerequisites"></a>Composants requis
Vous devez déjà avoir [généralisé la machine virtuelle](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) et l’avoir arrêtée/libérée. La généralisation d’une machine virtuelle supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image.



## <a name="create-a-managed-image-in-the-portal"></a>Créer une image managée dans le portail 

1. Ouvrez le [portail](https://portal.azure.com).
2. Cliquez sur le signe (+) pour créer une ressource.
3. Dans le filtre de recherche, tapez **Image**.
4. Sélectionnez **Image** dans les résultats.
5. Dans le panneau **Image**, cliquez sur **Créer**.
6. Dans **Nom**, entrez le nom de l’image.
7. Dans **Groupe de ressources**, sélectionnez **Créer nouveau** et entrez un nom ou sélectionnez **À partir d’un groupe existant** et choisissez un groupe de ressources à utiliser dans la liste déroulante.
8. Dans **Emplacement**, choisissez l’emplacement de votre groupe de ressources.
9. Dans **Type de système d’exploitation**, sélectionnez le type de système d’exploitation, soit Windows, soit Linux.
11. Dans **Stockage d’objets blob**, cliquez sur **Parcourir** pour rechercher le disque dur virtuel dans le stockage Azure.
12. Dans **Type de compte**, choisissez Standard_LRS ou Premium_LRS. Le type Standard utilise des disques durs et Premium, des disques SSD. Les deux utilisent le stockage localement redondant.
13. Dans **Mise en cache du disque**, sélectionnez l’option de mise en cache de disque appropriée. Options possibles : **Aucune**, **Lecture seule** et **Lecture/écriture**.
14. Facultatif : vous pouvez également ajouter un disque de données existant à l’image.  
15. Une fois les sélections terminées, cliquez sur **Créer**.
16. Une fois que l’image est créée, elle s’affiche en tant que ressource **Image** dans la liste des ressources du groupe de ressources que vous avez choisi.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Créer une image managée d’une machine virtuelle à l’aide de Powershell

La création d’une image directement à partir de la machine virtuelle permet de s’assurer qu’elle comprend tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et tous les disques de données.


Avant de commencer, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


1. Définissez des variables. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assurez-vous que la machine virtuelle a été libérée.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -VMName $vmName -Force
    ```
    
3. Définissez l’état de la machine virtuelle sur **Généralisé**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Accédez à la machine virtuelle. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Créez la configuration de l’image.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Créez l’image.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Créer une image managée d’un disque dur virtuel à l’aide de Powershell

Créez une image managée à l’aide de votre disque dur virtuel généralisé de système d’exploitation.


1.  Tout d’abord, définissez les paramètres communs :

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Arrêtez/libérez la machine virtuelle.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -VMName $vmName -Force
    ```
    
3. Indiquez que la machine virtuelle est généralisée.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  Créez l’image à l’aide de votre disque dur virtuel généralisé de système d’exploitation.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Créer une image managée à partir d’une capture instantanée à l’aide de Powershell

Vous pouvez également créer une image managée à partir d’une capture instantanée du VHD d’une machine virtuelle généralisée.

    
1. Définissez des variables. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Accédez à la capture instantanée.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Créez la configuration de l’image.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Créez l’image.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>Étapes suivantes
- Vous pouvez à présent [créer une machine virtuelle à partir de l’image managée généralisée](virtual-machines-windows-create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    




<!--HONumber=Feb17_HO2-->


