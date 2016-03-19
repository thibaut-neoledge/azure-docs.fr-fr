<properties 
   pageTitle="Déployer une machine virtuelle avec une adresse IP publique statique à l’aide de PowerShell dans Resource Manager | Microsoft Azure"
   description="Découvrir comment déployer des machines virtuelles avec une adresse IP publique statique à l’aide de PowerShell dans Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="telmos" />

# Déployer une machine virtuelle avec une adresse IP publique statique à l’aide de PowerShell

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Étape 1 : démarrer votre script

Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

1. Remplacez les valeurs des variables suivantes par celles que vous souhaitez utiliser pour votre déploiement. Les valeurs ci-dessous sont mappées au scénario utilisé dans ce document.

		# Set variables resource group
		$rgName                = "IaaSStory"
		$location              = "West US"
		
		# Set variables for VNet
		$vnetName              = "WTestVNet"
		$vnetPrefix            = "192.168.0.0/16"
		$subnetName            = "FrontEnd"
		$subnetPrefix          = "192.168.1.0/24"
		
		# Set variables for storage
		$stdStorageAccountName = "iaasstorystorage"
		
		# Set variables for VM
		$vmSize                = "Standard_A1"
		$diskSize              = 127
		$publisher             = "MicrosoftWindowsServer"
		$offer                 = "WindowsServer"
		$sku                   = "2012-R2-Datacenter"
		$version               = "latest"
		$vmName                = "WEB1"
		$osDiskName            = "osdisk"
		$nicName               = "NICWEB1"
		$privateIPAddress      = "192.168.1.101"
		$pipName               = "PIPWEB1"
		$dnsName               = "iaasstoryws1"

## Étape 2 : créer les ressources nécessaires pour vos machines virtuelles

Avant de créer une machine virtuelle, vous devez mettre à sa disposition un groupe de ressources, un réseau virtuel, une adresse IP publique et une carte réseau.

1. Créez un groupe de ressources.

		New-AzureRmResourceGroup -Name $rgName -Location $location
		
2. Créez le réseau virtuel et le sous-réseau.

		$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
		    -AddressPrefix $vnetPrefix -Location $location   
		
		Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
		    -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
		
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. Créez la ressource IP publique.

		$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
		    -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. Créez la carte réseau pour la machine virtuelle dans le sous-réseau créé ci-dessus, avec l’adresse IP publique. Notez que la première applet de commande récupère le réseau virtuel d’Azure. Cette opération est nécessaire dans la mesure où l'applet de commande **Set-AzureRmVirtualNetwork** a été exécutée pour modifier le réseau virtuel existant.

		$vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
		$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
		    -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
		    -PublicIpAddress $pip

5. Créez un compte de stockage pour héberger le lecteur du système d’exploitation de la machine virtuelle.

		$stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
		    -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## Étape 3 : créer la machine virtuelle 

Une fois toutes les ressources nécessaires en place, vous pouvez créer une machine virtuelle.

1. Créez l’objet de configuration pour la machine virtuelle.

		$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. Obtenez les informations d’identification du compte d’administrateur local de la machine virtuelle.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. Créez un objet de configuration de machine virtuelle.

		$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
    		-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. Définissez l’image du système d’exploitation pour la machine virtuelle.

		$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
    		-Offer $offer -Skus $sku -Version $version

4. Configurez le disque du système d’exploitation.

		$osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. Ajoutez la carte réseau à la machine virtuelle.

		$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. Créez la machine virtuelle.

		New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. Enregistrez le fichier de script.

## Étape 4 : exécuter le script

Une fois que vous avez effectué les modifications nécessaires et compris le script ci-dessus, exécutez le script.

1. À partir d’une console PowerShell ou de PowerShell ISE, exécutez le script ci-dessus.
2. La sortie ci-dessous doit s’afficher après quelques minutes.

		ResourceGroupName : IaaSStory
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
				
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {}
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
		
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {
		                      "DnsServers": []
		                    }
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
				
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Status              : Succeeded
		StatusCode          : OK
		Output              : 
		StartTime           : 1/12/2016 12:57:56 PM -08:00
		EndTime             : 1/12/2016 12:59:13 PM -08:00
		Error               : 
		ErrorText           : 

   

<!---HONumber=AcomDC_0114_2016-->