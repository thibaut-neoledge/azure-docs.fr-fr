---
title: "Créer un disque géré à partir d’un disque dur virtuel dans Azure | Microsoft Docs"
description: "Créez un disque géré à partir d’un disque dur virtuel hébergé dans un compte de stockage Azure à l’aide du modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 51dbf591138434f1639dd6d4e93758927cff6d33
ms.lasthandoff: 03/31/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>Créer des disques gérés à partir de disques non gérés dans un compte de stockage

Un disque géré peut être créé à partir d’un disque de données ou d’un disque du système d’exploitation existant hébergé dans un compte de stockage Azure. Vous pouvez également créer un disque vide qui peut être utilisé comme nouveau disque de données pour une machine virtuelle. 

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Créer un disque géré à partir d’un disque dur virtuel dans un compte de stockage Azure

Dans l’exemple suivant, nous allons créer un disque géré à partir d’un disque dur virtuel et lui attribuer le paramètre **$disk1** pour pouvoir l’utiliser ultérieurement. 

Le disque géré sera créé sous **West-US**, dans un groupe de ressources nommé **myResourceGroup**. Le disque sera nommé **myDisk** et créé à partir d’un fichier de disque dur virtuel nommé **myDisk.vhd** que nous avons précédemment téléchargé dans un compte de stockage nommé **mystorageaccount**. Le disque géré sera créé dans un compte de stockage Premium localement redondant (LRS). Les disques gérés peuvent uniquement être créés dans un compte dont le paramètre **-AccountType** est défini sur StandardLRS et PremiumLRS. 

1.  Définir les paramètres requis

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. Créez le disque de données. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>Créez un disque de données vide comme disque géré

Dans l’exemple suivant, nous allons créer un disque de données vide comme disque géré et lui attribuer le paramètre **$dataDisk2** pour pouvoir l’utiliser ultérieurement. Vous devrez initialiser le disque de données vide en le connectant à la machine virtuelle et en utilisant diskmgmt.msc ou [à l’aide d’un accès à distance WinRM et d’un script](attach-disk-ps.md#initialize-the-disk) une fois que vous aurez attaché le disque à une machine virtuelle en cours d’exécution.

Le disque de données vide sera créé sous **West Central US**, dans un groupe de ressources nommé **myResourceGroup**. Le disque sera nommé **myEmptyDataDisk**. Le disque vide sera créé dans un compte de stockage Premium localement redondant (LRS). Les disques gérés peuvent uniquement être créés dans un compte dont le paramètre **-AccountType** est défini sur StandardLRS et PremiumLRS.

Le disque utilisé dans cet exemple fait 128 Go, mais vous devrez choisir une taille qui répond aux besoins des applications en cours d’exécution sur votre machine virtuelle.

1.  Définir les paramètres requis

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. Créez le disque de données.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>Étapes suivantes    
- Si vous disposez déjà d’une machine virtuelle, vous pouvez y [attacher un disque de données](attach-disk-portal.md).
