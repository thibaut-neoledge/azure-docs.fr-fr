---
title: "Créer une machine virtuelle à partir d’un disque dur virtuel généralisé | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Windows à partir d’une image de disque dur virtuel généralisée dans un compte de stockage, à l’aide d’Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: 12832620d94226b6cfe391471c22fad2d1e3cf7e
ms.lasthandoff: 03/22/2017


---
# <a name="create-a-vm-from-a-generalized-vhd-image-in-a-storage-account"></a>Création d’une machine virtuelle à partir d’une image de disque dur virtuel généralisée dans un compte de stockage 

Cette rubrique décrit la création d’une machine virtuelle à partir d’un disque non managé généralisé qui se trouve dans un compte de stockage. Une image de disque dur virtuel généralisée a supprimé toutes les informations de votre compte personnel avec [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez créer un disque dur virtuel généralisé en exécutant Sysprep sur une machine virtuelle locale, puis en [chargeant le disque dur virtuel dans Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ou en exécutant Sysprep sur une machine virtuelle Azure existante, puis en [copiant le disque dur virtuel](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Si vous souhaitez créer une machine virtuelle à partir d’un disque dur virtuel spécialisé qui se trouve dans un compte de stockage, consultez [Créer une machine virtuelle à partir d’un disque dur virtuel spécialisé](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Pour plus d’informations sur l’utilisation de disques managés à la place de disques de compte de stockage, consultez [Créer une image d’une machine virtuelle gérée](virtual-machines-windows-capture-image-resource.md) et [Créer une machine virtuelle à partir d’une image gérée](virtual-machines-windows-create-vm-generalized-managed.md).

## <a name="prerequisites"></a>Composants requis
Si vous vous apprêtez à utiliser un disque dur virtuel chargé à partir d’une machine virtuelle locale, par exemple créé avec Hyper-V, veillez tout d’abord à suivre les instructions de [Préparer un disque dur virtuel Windows à charger vers Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Les disques durs virtuels chargés, comme les disques durs virtuels de machines virtuelles Azure existantes, doivent être généralisés pour pouvoir créer une machine virtuelle suivant cette méthode. Pour plus d’informations, consultez [Généraliser une machine virtuelle Windows avec Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

## <a name="set-the-uri-of-the-vhd"></a>Définir l’URI du disque dur virtuel

L’URI du disque dur virtuel à utiliser est au format : https://**moncomptedestockage**.blob.core.windows.net/**monconteneur**/**mondisquedurvirtuel**.vhd. Dans cet exemple, le disque dur virtuel nommé **mondisquedurvirtuel** se trouve dans le compte de stockage **moncomptedestockage** dans le conteneur **monconteneur**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** avec le préfixe d’adresse **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Création du réseau virtuel. L’exemple suivant crée un réseau virtuel nommé **myVnet** à l’emplacement **Ouest des États-Unis** avec le préfixe d’adresse **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Création d'une adresse IP publique et une interface réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez une adresse IP publique. Cet exemple crée une adresse IP publique nommée **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Créez la carte réseau. Cet exemple crée une carte réseau nommée **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP
Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un groupe de sécurité réseau nommé **myNsg** qui contient une règle nommée **myRdpRule** autorisant le trafic RDP sur le port 3389. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Créer une variable pour le réseau virtuel
Créez une variable pour le réseau virtuel terminé. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Création de la machine virtuelle
Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle téléchargée comme source de la nouvelle installation.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


