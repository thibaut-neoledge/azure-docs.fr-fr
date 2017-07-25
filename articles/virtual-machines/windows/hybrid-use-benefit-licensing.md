---
title: "Azure Hybrid Use Benefit pour Windows Server et client Windows | Microsoft Docs"
description: "Découvrez comment optimiser les avantages de votre contrat Software Assurance pour Windows pour mettre des licences locales sur Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/26/2017
ms.author: xujing
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 46b0895dc33fc13a1296301ed096fd3871b38952
ms.contentlocale: fr-fr
ms.lasthandoff: 05/31/2017


---
# <a name="azure-hybrid-use-benefit-for-windows-server-and-windows-client"></a>Azure Hybrid Use Benefit pour Windows Server et client Windows
Pour les clients avec Software Assurance, Azure Hybrid Use Benefit permet d’utiliser les licences Windows Server et client Windows locales et d’exécuter des machines virtuelles Windows dans Azure à moindre coût. Azure Hybrid Use Benefit pour Windows Server inclut Windows Server 2008R2, Windows Server 2012, Windows Server 2012R2 et Windows Server 2016. Azure Hybrid Use Benefit pour le client Windows inclut Windows 10. Pour plus d’informations, consultez la [page web de la licence d’Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

>[!IMPORTANT]
>Azure Hybrid Use Benefit pour le client Windows est actuellement en préversion utilisant l’image système Windows 10 dans la Place de marché Azure. Seuls les clients Enterprise avec Windows 10 Entreprise E3/E5 par utilisateur ou Windows VDA par utilisateur (licences d’abonnement utilisateur ou module complémentaire de licence d’abonnement utilisateur) (« licences éligibles ») sont éligibles.
>
>

## <a name="ways-to-use-azure-hybrid-use-benefit"></a>Méthodes d’utilisation d’Azure Hybrid Use Benefit
Il existe deux manières de déployer des machines virtuelles Windows avec Azure Hybrid Use Benefit :

1. Vous pouvez déployer des machines virtuelles à partir [d’images spécifiques de la Place de marché](#deploy-a-vm-using-the-azure-marketplace) qui sont préconfigurées avec Azure Hybrid Use Benefit - Windows Server 2016, Windows Server 2012R2, Windows Server 2012 et Windows Server 2008SP1.
2. Vous pouvez [télécharger une machine virtuelle personnalisée](#upload-a-windows-vhd) et [effectuez le déploiement à l’aide d’un modèle Resource Manager](#deploy-a-vm-via-resource-manager) ou [d’Azure PowerShell](#detailed-powershell-deployment-walkthrough).

## <a name="deploy-a-vm-using-the-azure-marketplace"></a>Déploiement d’une machine virtuelle à l’aide de la Place de marché Azure
Les images suivantes sont disponibles sur la Place de marché et sont préconfigurées avec Azure Hybrid Use Benefit : Windows Server 2016, Windows Server 2012R2, Windows Server 2012 et Windows Server 2008SP1. Ces images peuvent être déployées directement à partir du portail Azure, de modèles Resource Manager ou d’Azure PowerShell.

Vous pouvez déployer ces images directement à partir du portail Azure. Pour une utilisation dans des modèles Resource Manager et avec Azure PowerShell, affichez la liste des images comme suit :

Pour Windows Server :
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
- 2016-Datacenter version 2016.127.20170406 ou ultérieure

- 2012-R2-Datacenter version 4.127.20170406 ou ultérieure

- 2012-Datacenter version 3.127.20170406 ou ultérieure

- 2008-R2-SP1 version 2.127.20170406 ou ultérieure

Pour le client Windows :
```powershell
Get-AzureRMVMImageSku -Location "West US" -Publisher "MicrosoftWindowsServer" `
    -Offer "Windows-HUB"
```

## <a name="upload-a-windows-server-vhd"></a>Téléchargement d’un disque dur virtuel Windows Server
Pour déployer une machine virtuelle Windows Server dans Azure, vous devez d’abord créer un disque dur virtuel contenant votre build Windows de base. Ce disque dur virtuel doit être correctement préparé par Sysprep avant son téléchargement sur Azure. Découvrez [plus d’information sur la configuration requise du disque dur virtuel et le processus Sysprep](upload-generalized-managed.md) et la [prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sauvegardez la machine virtuelle avant d’exécuter Sysprep. 

Vérifiez que vous avez [installé et configuré la dernière version d’Azure PowerShell](/powershell/azure/overview). Une fois votre disque dur virtuel préparé, chargez-le dans votre compte Azure Storage en utilisant l’applet de commande `Add-AzureRmVhd` comme suit :

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server et Dynamics peuvent également utiliser vos licences Software Assurance. Vous devez encore préparer l’image Windows Server en installant vos composants d’application et en fournissant les clés de licence appropriées, puis en téléchargeant l’image de disque vers Azure. Passez en revue la documentation appropriée pour l’exécution de Sysprep avec votre application, telle que [Considérations relatives à l’installation de SQL Server à l’aide de Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Créer une image de référence SharePoint Server 2016 (Sysprep)).
>
>

Pour en savoir plus sur le chargement du disque dur virtuel vers Azure, [cliquez ici](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).


## <a name="deploy-a-vm-via-resource-manager-template"></a>Déployer une machine virtuelle à l’aide du modèle Resource Manager
Dans vos modèles Resource Manager, vous pouvez spécifier un paramètre supplémentaire pour `licenseType` . Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../../resource-group-authoring-templates.md). Une fois que votre disque dur virtuel téléchargé dans Azure, modifiez votre modèle Resource Manager pour inclure le type de licence dans le fournisseur de calcul et déployez votre modèle normalement :

Pour Windows Server :
```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

Pour le client Windows à utiliser avec l’image de la Place de marché Azure uniquement :
```json
"properties": {  
   "licenseType": "Windows_Client",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Déployer une machine virtuelle à l’aide du démarrage rapide de PowerShell
Lors du déploiement de votre machine virtuelle Windows Server par le biais de PowerShell, vous disposez d’un paramètre supplémentaire pour `-LicenseType`. Une fois votre disque dur virtuel chargé dans Azure, vous créez une machine virtuelle en utilisant `New-AzureRmVM` et spécifiez le type de licence comme suit :

Pour Windows Server :
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Pour le client Windows à utiliser avec l’image de la Place de marché Azure uniquement :
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

Vous trouverez des informations plus détaillées sur le [déploiement d’une machine virtuelle dans Azure par le biais de PowerShell](hybrid-use-benefit-licensing.md#detailed-powershell-deployment-walkthrough) ci-dessous, ainsi qu’une description plus détaillée des [étapes de création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Vérifiez que votre machine virtuelle utilise l’avantage de licence
Une fois votre machine virtuelle déployée par le biais de PowerShell ou de Resource Manager, vérifiez le type de licence avec `Get-AzureRmVM` comme suit :

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Le résultat ressemble à l’exemple suivant pour Windows Server :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ce résultat est différent de celui de la machine virtuelle suivante déployée sans la licence Azure Hybrid Use Benefit, comme une machine virtuelle déployée directement depuis la Galerie Azure :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="detailed-powershell-deployment-walkthrough"></a>Procédure détaillée de déploiement avec PowerShell
La procédure détaillée avec PowerShell montre le déploiement complet d’une machine virtuelle. Vous trouverez plus de contexte sur les applets de commande et les différents composants créés dans [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous créez votre groupe de ressources, votre compte de stockage et votre réseau virtuel, puis définissez et créez votre machine virtuelle.

Tout d’abord, procurez-vous des informations d’identification de manière sécurisée, puis définissez un emplacement et le nom du groupe de ressources :

```powershell
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "myResourceGroup"
```

Créez une adresse IP publique :

```powershell
$publicIPName = "myPublicIP"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName `
    -Location $location -AllocationMethod "Dynamic"
```

Définissez votre sous-réseau, votre carte réseau et votre réseau virtuel :

```powershell
$subnetName = "mySubnet"
$nicName = "myNIC"
$vnetName = "myVnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location `
    -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location `
    -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nommez votre machine virtuelle et créez une configuration de machine virtuelle :

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Définissez votre serveur DNS :

```powershell
$computerName = "myVM"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred `
    -ProvisionVMAgent -EnableAutoUpdate
```

Ajoutez votre carte réseau à la machine virtuelle :

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Définissez le compte de stockage à utiliser :

```powershell
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName mystorageaccount
```

Téléchargez votre disque dur virtuel préparé convenablement et attachez-y la machine virtuelle à utiliser :

```powershell
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/vhd/myvhd.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage `
    -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Enfin, créez votre machine virtuelle et définissez le type de licence pour utiliser Azure Hybrid Use Benefit :

Pour Windows Server :
```powershell
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType "Windows_Server"
```

## <a name="deploy-a-virtual-machine-scale-set-via-resource-manager-template"></a>Déployez un groupe de machines virtuelles identiques avec un modèle Resource Manager
Dans vos modèles Resource Manager de groupe de machines virtuelles identiques, vous pouvez spécifier un paramètre supplémentaire pour `licenseType` . Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../../resource-group-authoring-templates.md). Modifiez votre modèle Resource Manager afin d’inclure la propriété licenseType dans le cadre du paramètre virtualMachineProfile du groupe identique, puis déployez votre modèle comme d’habitude. Consultez l’exemple d’utilisation d’une image de Windows Server 2016 ci-dessous :


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```

> [!NOTE]
> La prise en charge du déploiement d’un groupe de machines virtuelles identiques avec des avantages AHUB via PowerShell et d’autres outils de kit SDK sera bientôt disponible.
>

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les [licences Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

En savoir plus sur [l’utilisation de modèles Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Découvrez comment [Azure Hybrid Use Benefit et Azure Site Recovery rendent la migration d’applications vers Azure encore plus rentable](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

