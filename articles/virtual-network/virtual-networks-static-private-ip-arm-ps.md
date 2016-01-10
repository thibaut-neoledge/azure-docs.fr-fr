<properties 
   pageTitle="Définition d’une adresse IP privée statique en mode ARM à l’aide de PowerShell | Microsoft Azure"
   description="Présentation des adresses IP privées statiques (adresses IP dynamiques) et de leur gestion en mode ARM à l’aide de PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

# Définition d’une adresse IP privée statique dans PowerShell

[AZURE.INCLUDE [Virtual-Networks-static-Private-IP-Selectors-ARM-Include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Créer un réseau virtuel](virtual-networks-create-vnet-arm-ps.md).

## Spécification d’une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet* avec une adresse IP privée statique de *192.168.1.101*, procédez comme suit :

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Définissez des variables pour le compte de stockage, l’emplacement, le groupe de ressources et les informations d’identification à utiliser. Vous devez entrer un nom d’utilisateur et un mot de passe pour la machine virtuelle. Le compte de stockage et le groupe de ressources doivent exister.

		$stName = "vnetstorage"
		$locName = "Central US"
		$rgName = "TestRG"
	    $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. Récupérez le réseau virtuel et le sous-réseau dans lesquels vous voulez créer la machine virtuelle.

	    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet	
	    $subnet = $vnet.Subnets[0].Id

4. Si nécessaire, créez une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet.

		$pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. Créez une carte réseau à l’aide de l’adresse IP privée statique que vous souhaitez affecter à la machine virtuelle. Assurez-vous que l’adresse IP fait partie de la plage de sous-réseau à laquelle vous ajoutez la machine virtuelle. Il s’agit de l’étape principale de cet article, dans laquelle vous définissez l’adresse IP privée comme statique.

		$nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. Créer la machine virtuelle à l’aide de la carte réseau créée ci-dessus.

		$vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
		$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
		$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
		$vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
		New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 

	Sortie attendue :

		EndTime             : 9/8/2015 2:32:09 PM -07:00
		Error               : 
		Output              : 
		StartTime           : 9/8/2015 2:27:42 PM -07:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK 


## Récupération d’informations d’adresse IP privée statique pour une machine virtuelle
Pour visualiser les informations d’adresse IP privée statique relatives à la machine virtuelle créée avec le script ci-dessus, exécutez la commande PowerShell ci-après et examinez les valeurs de *PrivateIpAddress* et *PrivateIpAllocationMethod* :

	Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Sortie attendue :

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/DNS01"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Static",
	                           "Subnet": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Suppression d’une adresse IP privée statique d’une machine virtuelle
Pour supprimer l’adresse IP privée statique ajoutée à la machine virtuelle dans le script ci-dessus, exécutez les commandes PowerShell suivantes :
	
	$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
	Set-AzureRmNetworkInterface -NetworkInterface $nic

Sortie attendue :

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/WindowsVM"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Dynamic",
	                           "Subnet": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Ajout d’une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
	$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
	Set-AzureRmNetworkInterface -NetworkInterface $nic

## Étapes suivantes

- En savoir plus sur les adresses [IP publiques réservées](../virtual-networks-reserved-public-ip).
- En savoir plus sur les adresses [IP publiques de niveau d’instance (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consultez les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)

<!----HONumber=AcomDC_1203_2015-->