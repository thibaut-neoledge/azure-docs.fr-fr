<properties 
	pageTitle="Création d’une machine virtuelle Windows avec Azure Resource Manager et PowerShell" 
	description="Utilisez le mode de gestion de ressources d’Azure PowerShell pour créer facilement une machine virtuelle Windows." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="josephd"/>

# Création d’une machine virtuelle Windows avec Azure Resource Manager et PowerShell

Cette rubrique décrit comment créer rapidement une machine virtuelle Azure Windows à l’aide d’Azure Resource Manager et de PowerShell.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Créer une machine virtuelle Windows avec PowerShell et la gestion des services Azure](virtual-machines-create-windows-powershell-service-manager.md)

## Créer la machine virtuelle Windows

Si vous avez déjà installé Azure PowerShell, vous devez disposer d'Azure PowerShell version 0.9.0 ou ultérieure. Vous pouvez vérifier la version d'Azure PowerShell que vous avez installée à l'aide de cette commande à l'invite de commandes Azure PowerShell.

	Get-Module azure | format-table version

Si ce n’est pas encore fait ou que vous devez mettre à jour la version d’Azure PowerShell installée, veuillez suivre les instructions de la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md) pour installer Azure PowerShell sur votre ordinateur local. Ouvrez ensuite une invite de commande Azure PowerShell.

Tout d'abord, vous devez vous connecter à Azure avec cette commande.

	Add-AzureAccount

Spécifiez l'adresse de messagerie électronique et le mot de passe de votre compte Azure dans la boîte de dialogue de connexion à Microsoft Azure.

Ensuite, si vous avez plusieurs abonnements, vous devez sélectionner l’abonnement Azure à utiliser. Pour afficher une liste de vos abonnements en cours, exécutez la commande suivante.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

À présent, remplacez tous les éléments entre guillemets, y compris les caractères < and >, par le nom de l’abonnement approprié, puis exécutez les commandes suivantes.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Ensuite, vous devez créer un compte de stockage Vous devez choisir un nom unique qui contient uniquement des chiffres et des lettres en minuscules. Vous pouvez tester l'unicité du nom du compte de stockage avec cette commande.

	Test-AzureName -Storage <Proposed storage account name>

Si cette commande renvoie « False », le nom proposé est unique.

Vous devez spécifier l'emplacement d’un centre de données Azure. Pour obtenir une liste des centres de données Azure, exécutez cette commande.

	Get-AzureLocation | sort Name | Select Name

Ensuite, vous devez basculer du mode Azure PowerShell au mode Resource Manager. Exécutez cette commande.

	Switch-AzureMode AzureResourceManager

À présent, copiez le bloc de commandes PowerShell suivant dans un éditeur de texte. Entrez le compte de stockage et l’emplacement de votre choix, en remplaçant tous les éléments entre guillemets, y compris les caractères < and >.

	$stName="<chosen storage account name>"	
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm 

Enfin, copiez le jeu de commandes précédent dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Le jeu de commandes est exécuté comme une série de commandes PowerShell. Vous devez entrer le nom et le mot de passe de votre compte administrateur local avant de créer votre machine virtuelle Azure.

Voici un exemple de résultat possible :

	PS C:> $stName="contosost"
	PS C:> $locName="West US"
	PS C:> $rgName="TestRG"
	PS C:> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'
	
	
	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *
	
	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG
	
	
	PS C:> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	
	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d'Azure Resource Manager](../resource-group-overview.md)

[Création d’une machine virtuelle Windows avec un modèle Resource Manager et PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Créer une machine virtuelle Windows avec PowerShell et la gestion des services Azure](virtual-machines-create-windows-powershell-service-manager.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)


 

<!---HONumber=62-->