---
title: "Création d&quot;une machine virtuelle Azure à l&quot;aide de PowerShell | Microsoft Docs"
description: "Utilisez Azure PowerShell et Azure Resource Manager pour créer facilement une machine virtuelle exécutant Windows Server."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.topic: get-started-article
ms.date: 02/14/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: c1262b7708918cbdc8ce35f3e65a47a04797f195
ms.openlocfilehash: 8a67352a65e755f2177fb4870f34c41440bf90a2

---

# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Création d'une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell

Cet article vous montre comment créer rapidement une machine virtuelle Azure exécutant Windows Server et les ressources dont elle a besoin à l’aide de [Resource Manager](../azure-resource-manager/resource-group-overview.md) et de PowerShell. Toutes les étapes décrites dans cet article sont nécessaires pour créer une machine virtuelle. La procédure globale dure environ 30 minutes. Remplacez les exemples de valeurs de paramètre dans les commandes par des noms appropriés à votre environnement.

## <a name="step-1-install-azure-powershell"></a>Étape 1 : installer Azure PowerShell

Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="step-2-create-a-resource-group"></a>Étape 2 : création d'un groupe de ressources

Toutes les ressources doivent être contenues dans un groupe de ressources. Nous allons donc tout d’abord en créer un.  

1. Obtenez la liste des emplacements disponibles où créer des ressources.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```

2. Définissez l’emplacement des ressources. Cette commande définit l’emplacement sur **centralus**.
   
    ```powershell
    $location = "centralus"
    ```

3. Créez un groupe de ressources. Cette commande crée le groupe de ressources nommé **myResourceGroup** à l’emplacement que vous définissez.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-optional-create-a-storage-account"></a>Étape 3 : (facultative) Création d'un compte de stockage

Lorsque vous créez une machine virtuelle, vous pouvez actuellement choisir si vous souhaitez utiliser [Azure Managed Disks](../storage/storage-managed-disks-overview.md) ou des disques non gérés. Si vous souhaitez utiliser un disque non géré, un [compte de stockage](../storage/storage-introduction.md) est nécessaire pour stocker le disque dur virtuel utilisé par la machine virtuelle que vous créez. Si vous choisissez d’utiliser un disque géré, le compte de stockage n’est pas nécessaire. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.

1. Vérifiez que le nom du compte de stockage est unique. Cette commande teste le nom **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Si cette commande renvoie **True**, le nom proposé est unique dans Azure. 

2. Créez maintenant le compte de stockage.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Étape 4 : création d'un réseau virtuel

Toutes les machines virtuelles font partie d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créez un sous-réseau pour le réseau virtuel. Cette commande crée un sous-réseau nommé **mySubnet** avec le préfixe d’adresse 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```

2. Créez maintenant le réseau virtuel. Cette commande crée un réseau virtuel nommé **myVnet** à l’aide du sous-réseau que vous avez créé et le préfixe d’adresse **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Étape 5 : création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Créez l’adresse IP publique. Cette commande crée une adresse IP publique nommée **myPublicIp** avec la méthode d’allocation **dynamique**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Créez l’interface réseau. Cette commande crée une interface réseau nommée **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Étape 6 : création d'une machine virtuelle

Maintenant que tous les éléments sont en place, il est temps de créer la machine virtuelle. Vous pouvez créer une machine virtuelle en utilisant une [image de la place de marché](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), une [image personnalisée généralisée (préparée avec Sysprep)](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou une [image personnalisée spécialisée (non préparée avec Sysprep)](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cet exemple utilise une image Windows Server issue de la place de marché. 

1. Exécutez cette commande pour définir le nom du compte administrateur et le mot de passe pour la machine virtuelle.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    Le mot de passe doit compter 12 à 123 caractères et au moins un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial.

2. Créez l’objet de configuration pour la machine virtuelle. Cette commande crée un objet de configuration nommé **myVmConfig** qui définit le nom de la machine virtuelle et la taille de la machine virtuelle.
   
    ```powershell
    $myVM = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Consultez la rubrique [Tailles des machines virtuelles dans Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pour obtenir la liste des tailles disponibles pour une machine virtuelle.

3. Configurez les paramètres de système d’exploitation pour la machine virtuelle. Cette commande définit le nom de l’ordinateur, le type de système d’exploitation et les informations d’identification du compte pour la machine virtuelle.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```

4. Définissez l’image à utiliser pour configurer la machine virtuelle. Cette commande définit l’image de Windows Server à utiliser pour la machine virtuelle. 

    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```

5. Ajoutez l'interface réseau que vous avez créée à la configuration.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```

6. Si vous utilisez un disque non géré, exécutez cette commande pour définir le nom et l’emplacement du disque dur de la machine virtuelle. Sinon, ignorez cette étape. Le fichier du disque dur virtuel d’un disque non géré est stocké dans un conteneur. Cette commande crée le disque dans un conteneur nommé **vhds/myOsDisk1.vhd** dans le compte de stockage que vous avez créé.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```

7. Ajoutez à la configuration de la machine virtuelle les informations concernant le disque du système d’exploitation. Cette commande crée un disque nommé **myOsDisk1**.
   
    Si vous utilisez un disque géré, exécutez cette commande pour définir le disque du système d’exploitation dans la configuration :

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -StorageAccountType PremiumLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
    ```

    Si vous utilisez un disque non géré, exécutez cette commande pour définir le disque du système d’exploitation dans la configuration :

    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```

8. Pour finir, créez la machine virtuelle
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Étapes suivantes

* Si vous rencontrez des problèmes lors du déploiement, nous vous conseillons de consulter la section [Résolution des erreurs courantes dans des déploiements Azure avec Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Création d’une machine virtuelle Windows avec un modèle Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Feb17_HO3-->


