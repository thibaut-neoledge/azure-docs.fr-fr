<properties
   pageTitle="Azure Hybrid Use Benefit pour Windows Server | Microsoft Azure"
   description="Découvrez comment optimiser les avantages de votre contrat Software Assurance pour Windows Server pour mettre des licences locales sur Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/13/2016"
   ms.author="georgem"/>

# Azure Hybrid Use Benefit pour Windows Server

Si vous utilisez Windows Server avec Software Assurance, vous pouvez mettre vos licences Windows Server locales sur Azure et exécuter des machines virtuelles Windows Server dans Azure à moindre coût. Azure Hybrid Use Benefit vous permet d’exécuter des machines virtuelles Windows Server dans Azure et de n’être facturé que pour la vitesse de calcul de base. Pour plus d’informations, consultez la [page web de la licence d’Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Cet article explique comment déployer des machines virtuelles Windows Server dans Azure pour profiter de cette licence.

> [AZURE.NOTE] Vous ne pouvez pas utiliser les images Azure Marketplace pour déployer des machines virtuelles Windows Server utilisant Azure Hybrid Use Benefit. Vous devez déployer vos machines virtuelles à l’aide de PowerShell ou de modèles Resource Manager pour désigner correctement vos machines virtuelles comme éligibles à la remise sur la vitesse de calcul de base.

## Conditions préalables
Plusieurs conditions sont requises pour utiliser Azure Hybrid Use Benefit sur des machines virtuelles Windows Server dans Azure :

- disposer d’une installation du module Azure PowerShell ;
- charger votre disque dur virtuel Windows Server dans Azure Storage

### Installation d'Azure PowerShell
Vérifiez que vous avez [installé et configuré la dernière version d’Azure PowerShell](../powershell-install-configure.md). Même si vous vous apprêtez à déployer vos machines virtuelles à l’aide de modèles Resource Manager, Azure PowerShell doit être installé pour le chargement de votre disque dur virtuel Windows Server (voir l’étape suivante).

### Téléchargement d’un disque dur virtuel Windows Server

Pour déployer une machine virtuelle Windows Server dans Azure, vous devez d’abord créer un disque dur virtuel contenant votre build Windows Server de base. Ce disque dur virtuel doit être correctement préparé par Sysprep avant son téléchargement sur Azure. Découvrez [plus d’information sur la configuration requise du disque dur virtuel et le processus Sysprep](./virtual-machines-windows-upload-image.md) et la [prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sauvegardez la machine virtuelle avant d’exécuter Sysprep. Une fois votre disque dur virtuel préparé, chargez-le dans votre compte Azure Storage en utilisant l’applet de commande `Add-AzureRmVhd` comme suit :

```
Add-AzureRmVhd -ResourceGroupName MyResourceGroup -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd" -LocalFilePath 'C:\Path\To\myvhd.vhd'
```

> [AZURE.NOTE] Microsoft SQL Server, SharePoint Server et Dynamics peuvent également utiliser vos licences Software Assurance. Vous devez encore préparer l’image Windows Server en installant vos composants d’application et en fournissant les clés de licence appropriées, puis en téléchargeant l’image de disque vers Azure. Passez en revue la documentation appropriée pour l’exécution de Sysprep avec votre application, telle que [Considérations relatives à l’installation de SQL Server à l’aide de Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Créer une image de référence SharePoint Server 2016 (Sysprep)).

Pour en savoir plus sur le chargement du disque dur virtuel vers Azure, [cliquez ici](./virtual-machines-windows-upload-image.md#upload-the-vm-image-to-your-storage-account).

> [AZURE.TIP] Cet article se concentre sur le déploiement de machines virtuelles Windows Server. Vous pouvez également déployer des machines virtuelles du client Windows de la même manière. Dans les exemples suivants, remplacez `Server` par `Client` de façon appropriée.

## Déployer une machine virtuelle via le démarrage rapide de PowerShell
Lors du déploiement de votre machine virtuelle Windows Server par le biais de PowerShell, vous disposez d’un paramètre supplémentaire pour `-LicenseType`. Une fois votre disque dur virtuel chargé dans Azure, vous créez une machine virtuelle en utilisant `New-AzureRmVM` et spécifiez le type de licence comme suit :

```
New-AzureRmVM -ResourceGroupName MyResourceGroup -Location "West US" -VM $vm -LicenseType Windows_Server
```

Vous trouverez des informations plus détaillées sur le [déploiement d’une machine virtuelle dans Azure par le biais de PowerShell](./virtual-machines-windows-hybrid-use-benefit-licensing.md#deploy-windows-server-vm-via-powershell-detailed-walkthrough) ci-dessous, ainsi qu’une description plus détaillée des [étapes de création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](./virtual-machines-windows-ps-create.md).

## Déployer une machine virtuelle à l’aide de Resource Manager
Dans vos modèles Resource Manager, vous pouvez spécifier un paramètre supplémentaire pour `licenseType`. Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../resource-group-authoring-templates.md). Une fois que votre disque dur virtuel téléchargé dans Azure, modifiez votre modèle Resource Manager pour inclure le type de licence dans le fournisseur de calcul et déployez votre modèle normalement :

```
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   },
```
 
## Vérifiez que votre machine virtuelle utilise l’avantage de licence
Une fois votre machine virtuelle déployée par le biais de PowerShell ou de Resource Manager, vérifiez le type de licence avec `Get-AzureRmVM` comme suit :
 
```
Get-AzureRmVM -ResourceGroup MyResourceGroup -Name MyVM
```

Le résultat ressemble à ce qui suit :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ce cas de figure est différent de celui de la machine virtuelle suivante déployée sans la licence Azure Hybrid Use Benefit, comme une machine virtuelle déployée directement depuis la Galerie Azure :

```
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : 
```
 
## Procédure détaillée avec PowerShell

La procédure détaillée avec PowerShell montre le déploiement complet d’une machine virtuelle. Vous trouverez plus de contexte sur les applets de commande et les différents composants créés dans [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](./virtual-machines-windows-ps-create.md). Vous créez votre groupe de ressources, votre compte de stockage et votre réseau virtuel, puis définissez et créez votre machine virtuelle.
 
Tout d’abord, procurez-vous des informations d’identification de manière sécurisée, puis définissez un emplacement et le nom du groupe de ressources :

```
$cred = Get-Credential
$location = "West US"
$resourceGroupName = "TestLicensing"
```

Créez une adresse IP publique :

```
$publicIPName = "testlicensingpublicip"
$publicIP = New-AzureRmPublicIpAddress -Name $publicIPName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
```

Définissez votre sous-réseau, votre carte réseau et votre réseau virtuel :

```
$subnetName = "testlicensingsubnet"
$nicName = "testlicensingnic"
$vnetName = "testlicensingvnet"
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/8
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix 10.0.0.0/8 -Subnet $subnetconfig
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $resourceGroupName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $publicIP.Id
```

Nommez votre machine virtuelle et créez une configuration de machine virtuelle :

```
$vmName = "testlicensing"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
```

Définissez votre serveur DNS :

```
$computerName = "testlicensing"
$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Ajoutez votre carte réseau à la machine virtuelle :

```
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Définissez le compte de stockage à utiliser :

```
$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -AccountName testlicensing
```

Téléchargez votre disque dur virtuel préparé convenablement et attachez-y la machine virtuelle à utiliser :

```
$osDiskName = "licensing.vhd"
$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
$urlOfUploadedImageVhd = "https://testlicensing.blob.core.windows.net/vhd/licensing.vhd"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption FromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
```

Enfin, créez votre machine virtuelle et définissez le type de licence pour utiliser Azure Hybrid Use Benefit :

```
New-AzureRmVM -ResourceGroupName $resourceGroupName -Location $location -VM $vm -LicenseType Windows_Server
```

## Étapes suivantes

En savoir plus sur les [licences Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

En savoir plus sur [l’utilisation de modèles Resource Manager](../resource-group-overview.md).

<!---HONumber=AcomDC_0831_2016-->