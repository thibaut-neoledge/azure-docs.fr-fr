## Création d’un réseau virtuel à l’aide de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour gérer vos ressources Azure à partir de l’invite de commandes sur n’importe quel ordinateur exécutant Windows, Linux ou OSX. Pour créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure, procédez comme suit :

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez la page [Installer et configurer l’interface de ligne de commande Azure](xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Au besoin, exécutez l’applet de commande **azure group create** pour créer un groupe de ressources, comme illustré ci-dessous. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés. Pour plus d’informations sur les groupes de ressources, consultez la page [Présentation d’Azure Resource Manager](resource-group-overview.md/#resource-groups).

		azure group create -n TestRG -l centralus

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (ou --name)**. Nom du nouveau groupe de ressources. Pour notre scénario, *TestRG*.
	- **-l (ou --location)**. Région Azure où le nouveau groupe de ressources sera créé. Pour notre scénario, *centralus*.

4. Exécutez la commande **azure network vnet create** pour créer un réseau virtuel et un sous-réseau, comme illustré ci-dessous.

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (ou --resource-group)**. Nom du groupe de ressources dans lequel sera créé le réseau virtuel. Pour notre scénario, *TestRG*.
	- **-n (ou --name)**. Nom du réseau virtuel à créer. Pour notre scénario, *TestVNet*.
	- **-a (ou --address-prefix)**. Liste de blocs CIDR utilisés pour l’espace d’adressage du réseau virtuel. Pour notre scénario, *192.168.0.0/16*.
	- **-l (ou --location)**. Région Azure où le réseau virtuel doit être créé. Pour notre scénario, *centralus*.

5. Exécutez la commande **azure network vnet subnet create** pour créer un sous-réseau, comme illustré ci-dessous. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-t (ou --vnet-name)**. Nom du réseau virtuel où sera créé le sous-réseau. Pour notre scénario, *TestVNet*.
	- **-n (ou --name)**. Nom du nouveau sous-réseau. Pour notre scénario, *FrontEnd*.
	- **-a (ou --address-prefix)**. Bloc CIDR de sous-réseau. Pour notre scénario, *192.168.1.0/24*.

6. Répétez l’étape 5 ci-dessus pour créer d’autres sous-réseaux, si nécessaire. Pour notre scénario, exécutez la commande ci-dessous pour créer le sous-réseau *BackEnd*.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Exécutez la commande **azure network vnet show** pour afficher les propriétés du nouveau réseau virtuel, comme illustré ci-dessous.

		azure network vnet show -g TestRG -n TestVNet

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=Oct15_HO3-->