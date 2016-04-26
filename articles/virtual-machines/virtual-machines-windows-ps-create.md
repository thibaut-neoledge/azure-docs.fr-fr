<properties
	pageTitle="Création d'une machine virtuelle Azure à l'aide de PowerShell | Microsoft Azure"
	description="Utilisez Azure PowerShell et Azure Resource Manager pour créer facilement une machine virtuelle exécutant Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Création d'une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell

Cet article vous montre comment créer rapidement une machine virtuelle Azure exécutant Windows Server et ses ressources associées à l'aide de Resource Manager et de PowerShell.

Il vous faudra environ 30 minutes pour effectuer les étapes décrites dans cet article.

## Étape 1 : installer Azure PowerShell

Consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour plus d'informations sur l’installation de la dernière version d'Azure PowerShell, sélectionnez l’abonnement vous souhaitez utiliser et connectez-vous à votre compte Azure.
        
## Étape 2 : création d'un groupe de ressources

Toutes les ressources doivent être déployées dans un groupe de ressources. Pour plus d’informations, consultez la page [Présentation d’Azure Resource Manager](../resource-group-overview.md).

1. Obtenez la liste des emplacements disponibles auxquels les ressources peuvent être créées.

	    Get-AzureLocation | sort Name | Select Name

2. Remplacez la valeur de **$locName** par un emplacement de la liste, par exemple **États-Unis du Centre**. Créez la variable.

        $locName = "location name"
        
3. Remplacez la valeur de **$rgName** avec le nom du nouveau groupe de ressources. Créez la variable et le groupe de ressources.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Étape 3 : création d’un compte de stockage

Un compte de stockage est nécessaire pour stocker le disque dur virtuel associé à la machine virtuelle que vous créez.

1. Remplacez la valeur de **$stName** (minuscules et chiffres uniquement) par le nom du compte de stockage. Testez l’unicité du nom choisi.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Si cette commande renvoie **False**, le nom proposé est unique.
    
2. Exécutez maintenant la commande pour créer le compte de stockage.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Étape 4 : création d'un réseau virtuel

Toutes les machines virtuelles doivent être associées à un réseau virtuel.

1. Remplacez la valeur de **$subnetName** par le nom du sous-réseau. Créez la variable et le sous-réseau.
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Remplacez la valeur de **$vnetName** par le nom du réseau virtuel. Créez la variable et le réseau virtuel avec le sous-réseau.

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## Étape 5 : création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d'une adresse IP publique et d’une interface réseau.

1. Remplacez la valeur de **$ipName** par le nom de l’adresse IP publique. Créez la variable et l’adresse IP publique.

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Remplacez la valeur de **$nicName** par le nom de l’interface réseau. Créez la variable et l'interface réseau.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Étape 6 : création d'une machine virtuelle

Maintenant que tous les éléments sont en place, il est temps de créer la machine virtuelle.

1. Exécutez la commande pour définir le nom du compte administrateur et le mot de passe pour la machine virtuelle.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. Remplacez la valeur de **$vmName** avec le nom de la machine virtuelle. Créez la variable et la configuration de la machine virtuelle.

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Consultez la rubrique [Tailles des machines virtuelles dans Azure](virtual-machines-windows-sizes.md) pour obtenir la liste des tailles disponibles pour une machine virtuelle.
    
3. Remplacez la valeur de **$compName** par le nom de l'ordinateur de la machine virtuelle. Créez la variable et ajoutez à la configuration les informations concernant le système d'exploitation.

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Définissez l'image à utiliser pour configurer la machine virtuelle.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Consultez la rubrique [Parcourir et sélectionner des images de machines virtuelles Windows dans Azure avec l’interface CLI ou PowerShell](virtual-machines-windows-cli-ps-findimage.md) pour plus d’informations sur la sélection des images à utiliser.
        
5. Ajoutez l'interface réseau que vous avez créée à la configuration.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Remplacez la valeur de **$blobPath** par le chemin d'accès et le nom de fichier dans le stockage du disque dur virtuel. Le fichier du disque dur virtuel est généralement stocké dans un conteneur, par exemple « vhds/WindowsVMosDisk.vhd ». Créez les variables.

        $blobPath = "vhd path and file name"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Remplacez la valeur de **$diskName** par le nom du disque du système d'exploitation. Créez la variable et ajoutez à la configuration les informations concernant le disque.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Pour finir, créez la machine virtuelle

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Vous devriez voir le groupe de ressources et toutes ses ressources dans le portail Azure ainsi qu'un état de réussite dans la fenêtre PowerShell :

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Étapes suivantes

- Si vous rencontrez des problèmes avec le déploiement, vous pouvez essayer de consulter [Résolution des problèmes liés aux déploiements de groupes de ressources avec le portail Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Pour savoir comment gérer la machine virtuelle que vous venez de créer, consultez la page [Gestion des machines virtuelles à l’aide d’Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).
- Tirez parti de l'utilisation d'un modèle pour créer une machine virtuelle en utilisant les informations contenues dans [Création d'une machine virtuelle Windows avec un modèle Resource Manager](virtual-machines-windows-ps-template.md)

<!---HONumber=AcomDC_0420_2016-->