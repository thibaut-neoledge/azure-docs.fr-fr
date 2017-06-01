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
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 4ffbe7e28d8b4c7d421bec477455a94609cd127a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Capturer une image managée d’une machine virtuelle généralisée dans Azure

Une ressource d’image managée peut être créée à partir d’une machine virtuelle généralisée stockée comme un disque géré ou non géré dans un compte de stockage. L’image peut ensuite être utilisée pour créer différentes machines virtuelles utilisant des disques gérés pour le stockage. 


## <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Si vous exécutez Sysprep avant de charger votre disque dur virtuel vers Azure pour la première fois, vérifiez que vous avez [préparé votre machine virtuelle](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) avant d’exécuter Sysprep. 
> 
> 

1. Connectez-vous à la machine virtuelle Windows
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case **Généraliser** est cochée.
4. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
5. Cliquez sur **OK**.
   
    ![Démarrer Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. Ne redémarrez pas la machine virtuelle.


## <a name="create-a-managed-image-in-the-portal"></a>Créer une image managée dans le portail 

1. Ouvrez le [portail](https://portal.azure.com).
2. Cliquez sur le signe (+) pour créer une ressource.
3. Dans le filtre de recherche, tapez **Image**.
4. Sélectionnez **Image** dans les résultats.
5. Dans le panneau **Image**, cliquez sur **Créer**.
6. Dans **Nom**, entrez le nom de l’image.
7. Si vous avez plusieurs abonnements, sélectionnez le bon dans la liste déroulante **Abonnement**.
7. Dans **Groupe de ressources**, sélectionnez **Créer** et tapez un nom, ou sélectionnez **À partir d’un groupe existant** et choisissez un groupe de ressources à utiliser dans la liste déroulante.
8. Dans **Emplacement**, choisissez l’emplacement de votre groupe de ressources.
9. Dans **Type de système d’exploitation**, sélectionnez le type de système d’exploitation, soit Windows, soit Linux.
11. Dans **Stockage Blob**, cliquez sur **Parcourir** pour rechercher le disque dur virtuel dans votre stockage Azure.
12. Dans **Type de compte**, choisissez Standard_LRS ou Premium_LRS. Le type Standard utilise des disques durs et Premium, des disques SSD. Les deux utilisent le stockage localement redondant.
13. Dans **Mise en cache du disque**, sélectionnez l’option de mise en cache de disque appropriée. Options possibles : **Aucune**, **Lecture seule** et **Lecture/écriture**.
14. Facultatif : vous pouvez également ajouter un disque de données existant à l’image en cliquant sur **+ Ajouter des données**.  
15. Une fois les sélections terminées, cliquez sur **Créer**.
16. Une fois que l’image est créée, elle s’affiche en tant que ressource **Image** dans la liste des ressources du groupe de ressources que vous avez choisi.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Créer une image managée d’une machine virtuelle à l’aide de Powershell

La création d’une image directement à partir de la machine virtuelle permet de s’assurer qu’elle comprend tous les disques associés à la machine virtuelle, y compris le disque du système d’exploitation et tous les disques de données.


Avant de commencer, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).


1. Définissez des variables. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Assurez-vous que la machine virtuelle a été libérée.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
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
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
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
- Vous pouvez à présent [créer une machine virtuelle à partir de l’image managée généralisée](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    


