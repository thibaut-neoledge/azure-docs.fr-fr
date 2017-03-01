---
title: "Charger un disque dur virtuel spécialisé dans Azure à utiliser pour la création d’une machine virtuelle | Microsoft Docs"
description: "Chargez un disque dur virtuel spécialisé dans Azure à utiliser pour la création d’une machine virtuelle."
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
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: fc8cb82e952a05b161f00ef9ebfbd4852d3987d4


---

# <a name="upload-a-specialized-vhd-to-azure-to-use-for-creating-a-new-vm"></a>Charger un disque dur virtuel spécialisé dans Azure à utiliser pour la création d’une machine virtuelle

Un disque dur virtuel spécialisé gère les comptes d’utilisateurs, les applications et les autres données d’état à partir de votre machine virtuelle d’origine. Vous pouvez charger un disque dur virtuel spécialisé dans Azure et l’utiliser pour créer une machine virtuelle qui utilise Managed Disks ou un compte de stockage non géré. Nous vous recommandons d’utiliser [Managed Disks](../storage/storage-managed-disks-overview.md) pour tirer parti de la gestion simplifiée et des fonctionnalités supplémentaires offertes par Managed Disks.


> [!IMPORTANT]
> Avant de charger un disque dur virtuel dans Azure, vous devez apprendre à [préparer un disque dur virtuel Windows ou un disque VHDX à charger sur Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


* Pour plus d’informations sur la tarification des différentes tailles de machines virtuelles, consultez [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
* Pour plus d’informations sur la tarification du stockage, consultez [Tarification du storage](https://azure.microsoft.com/pricing/details/storage/blobs/). 
* Pour la disponibilité des tailles de machine virtuelle dans les régions Azure, consultez [Disponibilité des produits par région](https://azure.microsoft.com/regions/services/).
* Pour connaître les limites générales des machines virtuelles Azure, consultez [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## <a name="before-you-begin"></a>Avant de commencer
Si vous utilisez PowerShell, assurez-vous que vous disposez de la dernière version du module PowerShell AzureRM.Compute. Exécutez la commande suivante pour l’installer.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Pour plus d’informations, consultez la page relative au [contrôle de version d’Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).


## <a name="prepare-the-vm"></a>Préparation de la machine virtuelle
 
Si vous prévoyez d’utiliser le disque dur virtuel spécialisé tel quel pour créer une machine virtuelle, veillez à effectuer les étapes suivantes. 
  * Si vous prévoyez d’utiliser Managed Disks, consultez [Planification de la migration vers Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
  * [Préparer un disque dur virtuel Windows à charger sur Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Ne généralisez pas** la machine virtuelle à l’aide de Sysprep.
  * Supprimez tous les outils de virtualisation invité et les agents installés sur la machine virtuelle (c’est-à-dire les outils VMware).
  * Vérifiez que la machine virtuelle est configurée pour extraire son adresse IP et ses paramètres DNS via DHCP. Cela permet de s’assurer que le serveur obtient une adresse IP dans le réseau virtuel lors de son démarrage. 
  * Arrêtez la machine virtuelle avant de continuer.

## <a name="log-in-to-azure"></a>Connexion à Azure
Si vous n’avez pas déjà installé PowerShell version 1.4 ou ultérieure, lisez [Installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure. Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenez les ID d’abonnement de vos abonnements disponibles.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement. Remplacez `<subscriptionID>` par l’ID de l’abonnement approprié.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obtention du compte de stockage
Vous avez besoin d’un compte de stockage dans Azure pour stocker l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un. 

Si vous prévoyez d’utiliser le disque dur virtuel pour créer un disque géré pour une machine virtuelle, l’emplacement du compte de stockage doit être le même que celui où vous allez créer la machine virtuelle.

Pour afficher les comptes de stockage disponibles, tapez :

```powershell
Get-AzureRmStorageAccount
```

Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#upload-the-vm-vhd-to-your-storage-account).

Si vous devez créer un compte de stockage, procédez comme suit :

1. Vous avez besoin du nom du groupe de ressources dans lequel doit être créé le compte de stockage. Pour rechercher tous les groupes de ressources dans votre abonnement, tapez :
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Pour créer un groupe de ressources nommé **MyResourceGroup** dans la région **Ouest des États-Unis**, tapez :

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Créez un compte de stockage nommé **mystorageaccount** dans ce groupe de ressources en utilisant l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Les valeurs valides pour -SkuName sont :
   
   * **Standard_LRS** - Stockage localement redondant. 
   * **Standard_ZRS** - Stockage redondant dans une zone.
   * **Standard_GRS** - Stockage géo-redondant. 
   * **Standard_RAGRS** - Stockage géo-redondant avec accès en lecture. 
   * **Premium_LRS** - Stockage Premium localement redondant. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Téléchargement du disque dur virtuel vers votre compte de stockage

Utilisez l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) pour charger le disque dur virtuel vers un conteneur de votre compte de stockage. Cet exemple télécharge le fichier **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` vers un compte de stockage nommé **mystorageaccount** dans le groupe de ressources **myResourceGroup**. Le fichier est placé dans le conteneur nommé **mycontainer** et le nouveau nom de fichier est **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Selon votre connexion réseau et la taille de votre fichier de disque dur virtuel, l’exécution de cette commande peut prendre un certain temps.

Enregistrez le chemin d’accès **URI de destination** pour l’utiliser ultérieurement si vous prévoyez de créer un disque géré ou une nouvelle machine virtuelle à l’aide du disque dur virtuel chargé.

### <a name="other-options-for-uploading-a-vhd"></a>Autres options de téléchargement d’un disque dur virtuel

Vous pouvez également télécharger un disque dur virtuel sur votre compte de stockage en utilisant l’un des moyens suivants :

-   [API de copie d’un objet blob de stockage Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Téléchargement d’objets blob dans Storage Explorer](https://azurestorageexplorer.codeplex.com/)

-   [Référence sur l’API REST du service Import/Export Storage](https://msdn.microsoft.com/library/dn529096.aspx)

    Nous recommandons l’utilisation de du service d’importation/exportation si l’estimation du temps de téléchargement est de plus de 7 jours. Vous pouvez utiliser [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) pour estimer le temps à partir de la taille des données et de l’unité de transfert. 

    Le service Import/Export permet de copier sur un compte de stockage Standard. Vous devez effectuer une copie d’un compte de stockage Standard vers un compte de stockage Premium à l’aide d’un outil tel qu’AzCopy.

    
## <a name="create-the-subnet-and-vnet"></a>Créer le sous-réseau et le réseau virtuel

Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Créez le sous-réseau. L’exemple suivant crée un sous-réseau nommé **mySubnet** dans le groupe de ressources **myResourceGroup** et définit le préfixe d’adresse de sous-réseau sur **10.0.0.0/24**.
   
    ```powershell
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
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

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

Pour plus d’informations sur les points de terminaison et les règles NSG, consultez [Ouverture de ports sur une machine virtuelle dans Azure avec PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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

Le système d’exploitation spécialisé peut être un disque dur virtuel que vous avez [chargé dans Azure](virtual-machines-windows-upload-image.md) ou une [copie du disque dur virtuel d’une machine virtuelle Azure existante](virtual-machines-windows-vhd-copy.md). 

Vous pouvez choisir l’une des deux options suivantes :
- **Option 1** : créer un disque géré spécialisé à partir d’un disque dur virtuel spécialisé dans un compte de stockage existant à utiliser en tant que disque du système d’exploitation.

ou 

- **Option 2** : utiliser un disque dur virtuel spécialisé stocké dans votre propre compte de stockage (un disque non géré). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Option 1 : Créer un disque géré à partir d’un disque spécialisé non géré

1. Créez un disque géré à partir du disque dur virtuel existant spécialisé dans votre compte de stockage. Cet exemple utilise **myOSDisk1** en tant que nom de disque, place le disque dans un stockage **StandardLRS** et utilise **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** en tant qu’URI du disque dur virtuel source.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. Ajoutez le disque du système d’exploitation à la configuration. Cet exemple définit la taille du disque sur **128 Go** et attache le disque géré en tant que disque de système d’exploitation **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Facultatif : attachez des disques gérés supplémentaires en tant que disques de données. Cette option suppose que vous avez créé vos disques de données gérés à l’aide de la page [Create managed data disks](virtual-machines-windows-create-managed-disk-ps.md) (Créer des disques de données gérés). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Option 2 : Attacher un disque dur virtuel qui se trouve dans un compte de stockage existant

1. Définissez l’URI du disque dur virtuel que vous souhaitez utiliser. Dans cet exemple, le fichier de disque dur virtuel nommé **myOsDisk.vhd** est conservé dans un compte de stockage nommé **myStorageAccount** dans un conteneur portant le nom **myContainer**.

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
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
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

```
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
Pour vous connecter à votre nouvelle machine virtuelle, accédez à la machine virtuelle dans le [portail](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP Bureau à distance. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle. Pour plus d’informations, consultez [Connexion à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Feb17_HO2-->


