---
title: "Créer une machine virtuelle à partir d’un disque spécialisé dans Azure | Microsoft Docs"
description: "Créer une machine virtuelle en attachant un disque géré ou non géré spécialisé, dans le modèle de déploiement Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 78f993ce9bab6266479cdd121eeea4965724d9bd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Créer une machine virtuelle à partir d'un disque spécialisé

Créez une machine virtuelle en attachant un disque spécialisé en tant que disque du système d’exploitation à l’aide de Powershell. Un disque spécialisé est une copie du VHD d’une machine virtuelle existante qui gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. Vous pouvez utiliser un [disque géré](../../storage/storage-managed-disks-overview.md) spécialisé ou un disque non géré spécialisé pour créer la machine virtuelle.

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](/powershell/azure/overview).


## <a name="create-the-subnet-and-vnet"></a>Créer le sous-réseau et le réseau virtuel

Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** et définit le préfixe d’adresse de sous-réseau sur **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Créez le réseau virtuel. Cet exemple définit le nom de réseau virtuel sur **myVnetName**, l’emplacement sur **West US** et le préfixe d’adresse du réseau virtuel sur **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Créer une adresse IP publique et une carte réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez l’adresse IP publique. Dans cet exemple, le nom d’adresse IP publique est défini sur **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Créez la carte réseau. Dans cet exemple, le nom de carte réseau est défini sur **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Créer le groupe de sécurité réseau et une règle RDP
Pour pouvoir vous connecter à votre machine virtuelle avec le protocole RDP, vous devez disposer d’une règle de sécurité qui autorise l’accès RDP sur le port 3389. Du fait que le disque dur virtuel de la nouvelle machine virtuelle a été créé à partir d’une machine virtuelle spécialisée, une fois que la machine virtuelle est créée, vous pouvez utiliser un compte existant à partir de la machine virtuelle source qui a l’autorisation de se connecter à l’aide de RDP.
Cet exemple définit le nom du groupe de sécurité réseau sur **myNsg** et le nom de règle RDP sur **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Pour plus d’informations sur les points de terminaison et les règles NSG, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Définir le nom et la taille de machine virtuelle

Cet exemple définit « myVM » en tant que nom de la machine virtuelle et « Standard_A2 » pour sa taille.
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Ajouter la carte réseau
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configurer le disque du système d’exploitation

Le système d’exploitation spécialisé peut être un disque dur virtuel que vous avez [chargé dans Azure](upload-image.md) ou une [copie du disque dur virtuel d’une machine virtuelle Azure existante](vhd-copy.md). 

Vous pouvez choisir l’une des deux options suivantes :
- **Option 1** : créer un disque géré spécialisé à partir d’un disque dur virtuel spécialisé dans un compte de stockage existant à utiliser en tant que disque du système d’exploitation.

ou 

- **Option 2** : utiliser un disque dur virtuel spécialisé stocké dans votre propre compte de stockage (un disque non géré). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Option 1 : Créer un disque géré à partir d’un disque spécialisé non géré

1. Créez un disque géré à partir du disque dur virtuel existant spécialisé dans votre compte de stockage. Cet exemple utilise **myOSDisk1** en tant que nom de disque, place le disque dans un stockage **StandardLRS** et utilise **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** en tant qu’URI du disque dur virtuel source.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Ajoutez le disque du système d’exploitation à la configuration. Cet exemple définit la taille du disque sur **128 Go** et attache le disque géré en tant que disque de système d’exploitation **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Facultatif : attachez des disques gérés supplémentaires en tant que disques de données. Cette option suppose que vous avez créé vos disques de données gérés à l’aide de la page [Create managed data disks](create-managed-disk-ps.md) (Créer des disques de données gérés). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Option 2 : Attacher un disque dur virtuel qui se trouve dans un compte de stockage existant

1. Définissez l’URI du disque dur virtuel que vous souhaitez utiliser. Dans cet exemple, le fichier de disque dur virtuel nommé **myOsDisk.vhd** est conservé dans un compte de stockage nommé **myStorageAccount** dans un conteneur portant le nom **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Ajoutez le disque du système d’exploitation à l’aide de l’URL du VHD du système d’exploitation copié. Dans cet exemple, lorsque le disque du système d’exploitation est créé, le terme « osDisk » est ajouté au nom de la machine virtuelle pour créer le nom du disque du système d’exploitation. Cet exemple spécifie également que ce disque dur virtuel basé sur Windows doit être attaché à la machine virtuelle en tant que disque de système d’exploitation.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Facultatif : si vous avez des disques de données qui doivent être attachés à la machine virtuelle, ajoutez les disques de données à l’aide des URL des disques durs virtuels de données et du numéro d’unité logique (LUN) approprié.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Si vous utilisez un compte de stockage, les URL des disques de données et des disques du système d’exploitation se présentent comme suit : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pour obtenir les URL, rendez-vous sur le portail, accédez au conteneur de stockage de destination, cliquez sur le disque dur virtuel du système d’exploitation ou de données qui a été copié, puis copiez le contenu de l’URL.


## <a name="create-the-vm"></a>Création de la machine virtuelle

Créez la machine virtuelle en utilisant les configurations que nous venons de créer.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Vérifier que la machine virtuelle a été créée
Vous devez voir la machine virtuelle nouvellement créée soit dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles**, soit en utilisant les commandes PowerShell suivantes :

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Étapes suivantes
Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](connect-logon.md).


