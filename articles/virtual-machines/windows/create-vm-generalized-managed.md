---
title: "Créer une machine virtuelle à partir d’une image de machine virtuelle managée dans Azure | Microsoft Docs"
description: "Créez une machine virtuelle Windows à partir d’une image de machine virtuelle managée généralisée à l’aide d’Azure PowerShell, avec le modèle de déploiement Resource Manager."
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
ms.date: 05/22/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 4b2593067193fe928cb8ac2b662fc3fd8ba7aedf
ms.contentlocale: fr-fr
ms.lasthandoff: 07/22/2017

---
# <a name="create-a-vm-from-a-managed-image"></a>Créer une machine virtuelle à partir d’une image gérée

Vous pouvez créer plusieurs machines virtuelles à partir d’une image de machine virtuelle managée dans Azure. Une image de machine virtuelle managée contient les informations nécessaires pour créer une machine virtuelle, y compris le disque du système d’exploitation et les disques de données. Les disques durs virtuels qui composent l’image, y compris les disques du système d’exploitation et les disques de données, sont stockés en tant que disques managés. 


## <a name="prerequisites"></a>Composants requis

Vous devez déjà avoir [créé une image de machine virtuelle managée](capture-image-resource.md) à utiliser pour créer la nouvelle machine virtuelle. 

Assurez-vous que vous disposez de la dernière version des modules PowerShell AzureRM.Compute et AzureRM.Network. Ouvrez une invite PowerShell en tant qu’administrateur, puis exécutez la commande suivante pour les installer.

```powershell
Install-Module AzureRM.Compute,AzureRM.Network
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).



## <a name="collect-information-about-the-image"></a>Collecter des informations sur l’image

Nous devons tout d’abord collecter des informations de base sur l’image et créer une variable pour celle-ci. Cet exemple utilise une image de machine virtuelle gérée nommée **myImage** qui se trouve dans le groupe de ressources **myResourceGroup** dans l’emplacement **Ouest-Centre des États-Unis**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. Cet exemple crée un sous-réseau nommé **mySubnet** avec le préfixe d’adresse **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Création du réseau virtuel. Cet exemple crée un réseau virtuel nommé **myVnet** avec le préfixe d’adresse **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

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

Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité réseau (NSG) qui autorise l’accès RDP sur le port 3389. 

Cet exemple crée un groupe de sécurité réseau nommé **myNsg** qui contient une règle nommée **myRdpRule** autorisant le trafic RDP sur le port 3389. Pour plus d’informations sur les groupes de sécurité réseau, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
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

## <a name="get-the-credentials-for-the-vm"></a>Obtenir les informations d’identification pour la machine virtuelle

L’applet de commande suivante ouvre une fenêtre dans laquelle vous allez entrer un nouveau nom d’utilisateur et un mot de passe à utiliser comme compte d’administrateur local pour accéder à distance à la machine virtuelle. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Définir des variables pour le nom de la machine virtuelle, le nom de l’ordinateur et la taille de la machine virtuelle

1. Créez des variables pour le nom de la machine virtuelle et le nom de l’ordinateur. Cet exemple définit **myVM** en tant que nom de la machine virtuelle et **myComputer** en tant que nom d’ordinateur.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Définissez la taille de la machine virtuelle. Cet exemple crée une machine virtuelle de taille **Standard_DS1_v2**. Pour plus d’informations, consultez la documentation relative aux [tailles de machines virtuelles](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Ajoutez le nom et la taille de la machine virtuelle à sa configuration.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Définir l’image de machine virtuelle en tant qu’image source pour la nouvelle machine virtuelle

Définissez l’image source à l’aide de l’ID de l’image de machine virtuelle gérée.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Définissez la configuration du système d’exploitation et ajoutez la carte réseau.

Indiquez le type de stockage (PremiumLRS ou StandardLRS) et la taille du disque du système d’exploitation. Cet exemple définit le type de compte sur **PremiumLRS**, la taille du disque sur **128 Go** et la mise en cache du disque sur **Lecture/écriture**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez la nouvelle machine virtuelle à l’aide de la configuration que nous avons créée et stockée dans la variable **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, voir [Créer et gérer des machines virtuelles Windows avec le module Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


