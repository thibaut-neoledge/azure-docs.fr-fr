---
title: Création d'une machine virtuelle Azure à l'aide de PowerShell | Microsoft Docs
description: Utilisez Azure PowerShell et Azure Resource Manager pour créer facilement une machine virtuelle exécutant Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: davidmu

---
# Création d'une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell
Cet article vous montre comment créer rapidement une machine virtuelle Azure exécutant Windows Server et les ressources dont elle a besoin à l’aide de [Resource Manager](../resource-group-overview.md) et de PowerShell.

Toutes les étapes décrites dans cet article sont nécessaires pour créer une machine virtuelle. La procédure globale dure environ 30 minutes.

## Étape 1 : installer Azure PowerShell
Pour plus d’informations sur l’installation de la version la plus récente d’Azure PowerShell, la sélection de votre abonnement et la connexion à votre compte, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

## Étape 2 : création d'un groupe de ressources
Commencez par créer un groupe de ressources.

1. Obtenez la liste des emplacements disponibles où créer des ressources.
   
        Get-AzureRmLocation | sort Location | Select Location
   
    Un résultat comme l’exemple suivant devrait s’afficher :
   
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus
2. Remplacez la valeur de **$locName** par un emplacement de la liste. Créez la variable.
   
        $locName = "centralus"
3. Remplacez la valeur de **$rgName** par le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.
   
        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

## Étape 3 : création d’un compte de stockage
Un [compte de stockage](../storage/storage-introduction.md) est nécessaire pour stocker le disque dur virtuel utilisé par la machine virtuelle que vous créez.

1. Remplacez la valeur de **$stName** par le nom du compte de stockage. Testez l’unicité du nom choisi.
   
        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName
   
    Si cette commande renvoie **True**, le nom proposé est unique dans Azure. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres.
2. Exécutez maintenant la commande pour créer le compte de stockage.
   
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName

## Étape 4 : création d'un réseau virtuel
Toutes les machines virtuelles font partie d’un [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Remplacez la valeur de **$subnetName** par le nom du sous-réseau. Créez la variable et le sous-réseau.
   
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
2. Remplacez la valeur de **$vnetName** par le nom du réseau virtuel. Créez la variable et le réseau virtuel avec le sous-réseau.
   
        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
   
    Utilisez des valeurs pertinentes pour votre application et votre environnement.

## Étape 5 : création d'une adresse IP publique et une interface réseau
Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Remplacez la valeur de **$ipName** par le nom de l’adresse IP publique. Créez la variable et l’adresse IP publique.
   
        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
2. Remplacez la valeur de **$nicName** par le nom de l’interface réseau. Créez la variable et l'interface réseau.
   
        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## Étape 6 : création d'une machine virtuelle
Maintenant que tous les éléments sont en place, il est temps de créer la machine virtuelle.

1. Exécutez la commande pour définir le nom du compte administrateur et le mot de passe pour la machine virtuelle.
   
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
   
    Le mot de passe doit compter 12 à 123 caractères et au moins un caractère minuscule, un caractère majuscule, un chiffre et un caractère spécial.
2. Remplacez la valeur de **$vmName** par le nom de la machine virtuelle. Créez la variable et la configuration de la machine virtuelle.
   
        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
   
    Consultez la rubrique [Tailles des machines virtuelles dans Azure](virtual-machines-windows-sizes.md) pour obtenir la liste des tailles disponibles pour une machine virtuelle.
3. Remplacez la valeur de **$compName** par le nom de l’ordinateur de la machine virtuelle. Créez la variable et ajoutez à la configuration les informations concernant le système d'exploitation.
   
        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. Définissez l'image à utiliser pour configurer la machine virtuelle.
   
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
   
    Pour plus d’informations sur la sélection des images à utiliser, consultez [Parcourir et sélectionner des images de machines virtuelles Windows dans Azure avec l’interface CLI ou PowerShell](virtual-machines-windows-cli-ps-findimage.md).
5. Ajoutez l'interface réseau que vous avez créée à la configuration.
   
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
6. Remplacez la valeur de **$blobPath** par le chemin d’accès et le nom de fichier dans le stockage du disque dur virtuel. Le fichier du disque dur virtuel est généralement stocké dans un conteneur, par exemple **vhds/WindowsVMosDisk.vhd**. Créez les variables.
   
        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
7. Remplacez la valeur de **$diskName** par le nom du disque du système d’exploitation. Créez la variable et ajoutez à la configuration les informations concernant le disque.
   
        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
8. Pour finir, créez la machine virtuelle
   
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm
   
    Vous devriez voir le groupe de ressources et toutes ses ressources dans le portail Azure ainsi qu'un état de réussite dans la fenêtre PowerShell :
   
        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Étapes suivantes
* Si vous rencontrez des problèmes de déploiement, consultez [Résolution des problèmes liés aux déploiements de groupes de ressources avec le Portail Azure](../resource-manager-troubleshoot-deployments-portal.md).
* Pour apprendre à gérer la machine virtuelle que vous avez créée, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).
* Tirez parti de l’utilisation d’un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Création d’une machine virtuelle Windows avec un modèle Resource Manager](virtual-machines-windows-ps-template.md).

<!---HONumber=AcomDC_1005_2016-->