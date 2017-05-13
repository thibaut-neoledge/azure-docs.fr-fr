---
title: "Créer des images de machine virtuelle personnalisées avec Azure PowerShell | Microsoft Docs"
description: "Tutoriel : créez une image de machine virtuelle personnalisée à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/02/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: ab19073735816ebb32a9840ec03b31b358ccb565
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017

---

# <a name="create-a-custom-image-of-an-azure-vm-using-powershell"></a>Créer une image personnalisée d’une machine virtuelle Azure à l’aide de PowerShell

Dans ce tutoriel, vous allez apprendre à définir votre propre image personnalisée d’une machine virtuelle Azure. Les images personnalisées vous permettent de créer des machines virtuelles à l’aide d’une image que vous avez déjà configurée. Les images personnalisées peuvent être utilisées pour amorcer le préchargement des fichiers binaires et applications, les configurations d’applications, les définitions de disques de données de machine virtuelle et les autres configurations de systèmes d’exploitation antérieurs. Lorsque vous créez une image personnalisée, la machine virtuelle que vous personnalisez, ainsi que tous les disques attachés, sont inclus dans l’image.

Les étapes de ce tutoriel peuvent être effectuées à l’aide de la dernière version du module [Azure PowerShell](/powershell/azure/overview).

## <a name="before-you-begin"></a>Avant de commencer

Les étapes ci-dessous expliquent comment prendre une machine virtuelle existante et la transformer en une image personnalisée réutilisable que vous pouvez utiliser pour créer de nouvelles instances de machines virtuelles.

Pour exécuter l’exemple dans ce tutoriel, vous devez disposer d’une machine virtuelle. Si nécessaire, cet [exemple de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) peut en créer une pour vous. Au cours du tutoriel, remplacez les noms du groupe de ressources et de la machine virtuelle si nécessaire.

## <a name="prepare-vm"></a>Préparer la machine virtuelle

Pour créer une image de machine virtuelle, vous devez préparer la machine virtuelle en la généralisant, en la libérant et en la marquant comme généralisée dans Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Généraliser la machine virtuelle Windows à l’aide de Sysprep

Sysprep supprime toutes les informations personnelles de votre compte, entre autres, et prépare la machine de façon à pouvoir l’utiliser comme image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).


1. Connectez-vous à la machine virtuelle.
2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par *%windir%\system32\sysprep*, puis exécutez *sysprep.exe*.
3. Dans la boîte de dialogue **Outil de préparation du système**, sélectionnez *Entrer en mode OOBE (Out-of-Box Experience)* et vérifiez que la case *Généraliser* est cochée.
4. Dans **Options d’arrêt**, sélectionnez *Arrêter*, puis cliquez sur **OK**.
5. Une fois l’opération Sysprep terminée, elle arrête la machine virtuelle. **Ne redémarrez pas la machine virtuelle**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Libérer la machine virtuelle et la marquer comme généralisée

Pour créer une image, la machine virtuelle doit être libérée et marquée comme généralisée dans Azure.

Libérez la machine virtuelle à l’aide de [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Force
```

Définissez l’état de la machine virtuelle sur `-Generalized` à l’aide de [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```powershell
Set-AzureRmVM -ResourceGroupName myResourceGroupImages -Name myVM -Generalized
```


## <a name="create-the-image"></a>Création de l’image

Créez à présent une image de la machine virtuelle à l’aide de [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) et [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). L’exemple suivant crée une image nommée *myimage* à partir d’une machine virtuelle nommée *myimage*.

Accédez à la machine virtuelle. 

```powershell
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroupImages
```

Créez la configuration de l’image.

```powershell
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Créez l’image.

```powershell
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroupImages
```    

 
## <a name="create-vms-from-the-image"></a>Créer des machines virtuelles à partir de l’image

Maintenant que vous avez une image, vous pouvez créer une ou plusieurs nouvelles machines virtuelles à partir de l’image. La création d’une machine virtuelle à partir d’une image personnalisée est très similaire à la création d’une machine virtuelle à l’aide d’une image de la Place de marché. Lorsque vous utilisez une image de la Place de marché, vous devez entrer des informations sur l’image, le fournisseur de l’image, l’offre, la référence et la version. Avec une image personnalisée, vous devez simplement fournir l’ID de la ressource d’image personnalisée. 

Dans le script suivant, nous créons une variable *$image* pour stocker des informations sur l’image personnalisée à l’aide de [Get-AzureRmImage](/powershell/module/azurerm.compute/get-azurermimage) et nous utilisons ensuite [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) et spécifions l’ID à l’aide de la variable *$image* que nous venons de créer. 

Le script crée une machine virtuelle nommée *myVMfromImage* à partir de notre image personnalisée dans un groupe de ressources nommé *myResourceGroupFromImage* dans l’emplacement *Ouest des États-Unis*.


```powershell
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

New-AzureRmResourceGroup -Name myResourceGroupFromImage -Location EastUS

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name "mypublicdns$(Get-Random)" `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4

  $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

  $nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$vmConfig = New-AzureRmVMConfig `
    -VMName myVMfromImage `
    -VMSize Standard_D1 | Set-AzureRmVMOperatingSystem -Windows `
        -ComputerName myComputer `
        -Credential $cred 

# Here is where we create a variable to store information about the image 
$image = Get-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroupImages

# Here is where we specify that we want to create the VM from and image and provide the image ID
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -Id $image.Id

$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

New-AzureRmVM `
    -ResourceGroupName myResourceGroupFromImage `
    -Location EastUS `
    -VM $vmConfig
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez approfondi vos connaissances sur la création d’images de machine virtuelle personnalisées. Passez au tutoriel suivant pour en savoir plus sur les machines virtuelles hautement disponibles.

[Créer des machines virtuelles hautement disponibles](tutorial-availability-sets.md)




