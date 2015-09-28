<properties 
   pageTitle="Comment définir une adresse IP privée statique en mode ARM à l’aide de l’interface de ligne de commande PowerShell | Microsoft Azure"
   description="Connaître les adresses IP statiques et les gérer en mode ARM à l’aide de l’interface de ligne de commande"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Définir une adresse IP privée statique dans l’invite de commande AZURE

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite de la création du modèle de déploiement Resource Manager. Vous pouvez également [gérer une adresse IP privée statique dans le modèle de déploiement classique](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [réseaux-virtuels-statique-scénario-ip-inclut](../../includes/virtual-networks-static-ip-scenario-include.md)]

Les commandes d’interface de ligne de commande Azure attendent un environnement simple déjà créé. Si vous souhaitez exécuter les commandes telles qu’elles sont affichées dans ce document, créez d’abord l’environnement de test décrit dans [créer un réseau virtuel](virtual-networks-create-vnet-arm-cli.md).

## Comment spécifier une adresse IP privée statique lors de la création d’une machine virtuelle
Pour créer une machine virtuelle nommée *DNS01* dans le sous-réseau *FrontEnd* d’un réseau virtuel nommé *TestVNet* avec une adresse IP privée statique de *192.168.1.101*, procédez comme suit :

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](xplat-cli.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.

2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Sortie attendue :

		info:    New mode is arm

3. Exécutez la commande **azure network public-ip create** pour créer une adresse IP publique de la machine virtuelle. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure network public-ip create -g TestRG -n TestPIP -l centralus
	
	Sortie attendue :

		info:    Executing command network public-ip create
		+ Looking up the public ip "TestPIP"
		+ Creating public ip address "TestPIP"
		+ Looking up the public ip "TestPIP"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
		data:    Name                            : TestPIP
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Dynamic
		data:    Idle timeout                    : 4
		info:    network public-ip create command OK

	- **-g (ou --resource-group)**. Nom du groupe de ressources dans l’IP public, lequel sera créé le réseau virtuel.
	- **-n (ou --name)**. Nom de l’adresse IP publique.
	- **-l (ou --location)**. Région Azure où le réseau virtuel doit être créé. Pour notre scénario, *centralus*.

3. Exécutez la commande **azure network nic create** pour créer une carte réseau avec une adresse IP privée statique. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

	Sortie attendue :

		+ Looking up the network interface "TestNIC"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC"
		+ Looking up the network interface "TestNIC"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
		data:    Name                            : TestNIC
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

	- **-a (or --private-ip-address)**. Adresse IP privée statique pour la carte réseau.
	- **-m (or --subnet-vnet-name)**. Nom du réseauVNet où sera créé le sous-réseau.
	- **-k (or --subnet-name)**. Nom du réseau virtuel où sera créé le sous-réseau.

4. Exécutez la commande **azure vm create** pour créer la machine virtuelle à l’aide de l’adresse IP publique et la carte réseau créées ci-dessus. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

	Sortie attendue :

		info:    Executing command vm create
		+ Looking up the VM "DNS01"
		info:    Using the VM Size "Standard_A1"
		warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Looking up the storage account vnetstorage
		+ Looking up the NIC "TestNIC"
		info:    Found an existing NIC "TestNIC"
		info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "TestPIP"
		info:    Found an existing PublicIP "TestPIP"
		info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
		+ Updating NIC "TestNIC"
		+ Looking up the NIC "TestNIC"
		+ Creating VM "DNS01"
		info:    vm create command OK

	- **-y (ou type de système d’exploitation--)**. Type de système d’exploitation pour la machine virtuelle, soit *Windows* ou *Linux*.
	- **-f (ou nom de la carte réseau--)**. Nom de la carte réseau que la machine virtuelle utilisera.
	- **-i (ou --nom ip public)**. Nom de l’adresse IP publique que la machine virtuelle utilisera.
	- **-F (ou --nom vnet)**. Nom du réseauVNet où sera créée la machine virtuelle.
	- **-j (or --vnet-subnet-name)**. Nom du sous-réseau où sera créée la machine virtuelle.

## Comment récupérer des informations d’adresse IP privée statique pour une machine virtuelle

Pour visualiser les informations d’adresse IP privée statique concernant la machine virtuelle créée avec le script ci-dessus, exécutez la commande d’interface de ligne de commande Azure, et examinez les valeurs *Private IP alloc-method* et *Adresse IP privée* :

	azure vm show -g TestRG -n DNS01

Sortie attendue :

	info:    Executing command vm show
	+ Looking up the VM "DNS01"
	+ Looking up the NIC "TestNIC"
	+ Looking up the public ip "TestPIP
	data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    ProvisioningState               :Succeeded
	data:    Name                            :DNS01
	data:    Location                        :centralus
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_A1
	data:
	data:    Storage Profile:
	data:      Source image:
	data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
	data:
	data:      OS Disk:
	data:        OSType                      :Windows
	data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :DNS01
	data:      User Name                     :adminuser
	data:      Windows Configuration:
	data:        Provision VM Agent          :true
	data:        Enable automatic updates    :true
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-90-1A-A8
	data:          Provisioning State        :Succeeded
	data:          Name                      :TestNIC
	data:          Location                  :centralus
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.122.213.159
	info:    vm show command OK

## Comment supprimer une adresse IP privée statique d’une machine virtuelle
Vous ne pouvez pas supprimer une adresse IP privée statique à partir d’une carte réseau dans l’interface de ligne de commande de Resource Manager. Vous devez créer une nouvelle carte réseau qui utilise une adresse IP dynamique, supprimer la carte réseau précédente de la machine virtuelle et ajouter la nouvelle carte réseau à la machine virtuelle. Pour modifier la carte réseau de la machine virtuelle utilisée dans les commandes ci-dessus, suivez les étapes ci-dessous.
	
1. Exécutez la commande **azure network nic create** pour créer une nouvelle carte réseau à l’aide de l’allocation d’adresses IP dynamiques. Notez que cette fois, vous n’avez pas besoin de spécifier l’adresse IP.

		azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

	Sortie attendue :

		info:    Executing command network nic create
		+ Looking up the network interface "TestNIC2"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC2"
		+ Looking up the network interface "TestNIC2"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
		data:    Name                            : TestNIC2
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.6
		data:      Private IP Allocation Method  : Dynamic
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

2. exécutez la commande **azure vm set** pour modifier la carte réseau utilisée par la machine virtuelle.

		azure vm set -g TestRG -n DNS01 -N TestNIC2

	Sortie attendue :

		info:    Executing command vm set
		+ Looking up the VM "DNS01"
		+ Looking up the NIC "TestNIC2"
		+ Updating VM "DNS01"
		info:    vm set command OK

3. le cas échéant, exécutez la commande **azure network nic delete** pour supprimer l’ancienne carte réseau.

		azure network nic delete -g TestRG -n TestNIC --quiet

	Sortie attendue :

		info:    Executing command network nic delete
		+ Looking up the network interface "TestNIC"
		+ Deleting network interface "TestNIC"
		info:    network nic delete command OK

## Comment ajouter une adresse IP privée statique à une machine virtuelle existante
Pour ajouter une adresse IP privée statique à la carte d’interface réseau utilisée par la machine virtuelle créée à l’aide du script ci-dessus, exécutez la commande suivante :

	azure netwrok nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Sortie attendue :

	info:    Executing command network nic set
	+ Looking up the network interface "TestNIC2"
	+ Updating network interface "TestNIC2"
	+ Looking up the network interface "TestNIC2"
	data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
	data:    Name                            : TestNIC2
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : centralus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-90-29-25
	data:    Enable IP forwarding            : false
	data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 192.168.1.101
	data:      Private IP Allocation Method  : Static
	data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

## Étapes suivantes

- En savoir plus sur les adresses [IP publiques réservées](../virtual-networks-reserved-public-ip).
- En savoir plus sur [les adresses IP publiques de niveau d’instance (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consultez les [API REST d’adresse IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)

<!---HONumber=Sept15_HO3-->