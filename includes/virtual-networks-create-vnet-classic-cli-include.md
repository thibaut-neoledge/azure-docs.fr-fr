## Création d'un réseau virtuel classique à l'aide de l'interface de ligne de commande Azure

Vous pouvez utiliser l'interface de ligne de commande Azure pour gérer vos ressources Azure à partir de l'invite de commande sur n'importe quel ordinateur exécutant Windows, Linux ou OSX. Pour créer un réseau virtuel à l'aide de l'interface de ligne de commande Azure, procédez comme suit :

1. Si vous n'avez jamais utilisé l'interface de ligne de commande Azure, consultez [Installer et configurer l'interface de ligne de commande Azure](xplat-cli.md) et suivez les instructions jusqu'à l'étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure network vnet create** pour créer un réseau virtuel et un sous-réseau, comme illustré ci-dessous. Observez le résultat de la commande de l'interface de ligne de commande. La liste affichée après le résultat présente les différents paramètres utilisés.

			azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
	
			info:    Executing command network vnet create
			+ Looking up network configuration
			+ Looking up locations
			+ Setting network configuration
			info:    network vnet create command OK

	- **--vnet**. Nom du réseau virtuel à créer. Pour notre scénario, *TestVNet*.
	- **-e (ou --address-space)**. Espace d'adressage du réseau virtuel. Pour notre scénario, *192.168.0.0*
	- **-i (ou -cidr)**. Masque de réseau au format CIDR. Pour notre scénario, *16*.
	- **-n (ou --subnet-name**). Nom du premier sous-réseau. Pour notre scénario, *FrontEnd*.
	- **-p (ou --subnet-start-ip)**. Adresse IP de début pour le sous-réseau, ou espace d'adressage du sous-réseau. Pour notre scénario, *192.168.1.0*.
	- **-r (ou --subnet-cidr)**. Masque de réseau au format CIDR pour le sous-réseau. Pour notre scénario, *24*.
	- **-l (ou --location)**. Région Azure où le réseau virtuel doit être créé. Pour notre scénario, *Centre des États-Unis*.

3. Exécutez la commande **azure network vnet subnet create** pour créer un sous-réseau, comme illustré ci-dessous. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés.

			azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
	
			info:    Executing command network vnet subnet create
			+ Looking up network configuration
			+ Creating subnet "BackEnd"
			+ Setting network configuration
			+ Looking up the subnet "BackEnd"
			+ Looking up network configuration
			data:    Name                            : BackEnd
			data:    Address prefix                  : 192.168.2.0/24
			info:    network vnet subnet create command OK

	- **-t (ou --vnet-name**. Nom du réseau virtuel où sera créé le sous-réseau. Pour notre scénario, *TestVNet*.
	- **-n (ou --name)**. Nom du nouveau sous-réseau. Pour notre scénario, *BackEnd*.
	- **-a (ou --address-prefix)**. Bloc CIDR de sous-réseau. Pour notre scénario, *192.168.2.0/24*.

4. Exécutez la commande **azure network vnet show** pour afficher les propriétés du nouveau réseau virtuel, comme illustré ci-dessous.

			azure network vnet show

			info:    Executing command network vnet show
			Virtual network name: TestVNet
			+ Looking up the virtual network sites
			data:    Name                            : TestVNet
			data:    Location                        : Central US
			data:    State                           : Created
			data:    Address space                   : 192.168.0.0/16
			data:    Subnets:
			data:      Name                          : FrontEnd
			data:      Address prefix                : 192.168.1.0/24
			data:
			data:      Name                          : BackEnd
			data:      Address prefix                : 192.168.2.0/24
			data:
			info:    network vnet show command OK

<!---HONumber=August15_HO9-->