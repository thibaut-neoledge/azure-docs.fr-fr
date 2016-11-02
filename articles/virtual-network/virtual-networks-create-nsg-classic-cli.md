<properties
   pageTitle="Création de groupes de sécurité réseau en mode classique à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
   description="Découvrez comment créer et déployer des groupes de sécurité réseau en mode classique à l'aide de l’interface de ligne de commande Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# Création de NSG (classiques) dans l’interface de ligne de commande Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement classique. Vous pouvez également [créer un groupe de sécurité réseau dans le modèle de déploiement Resource Manager](virtual-networks-create-nsg-arm-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test décrit dans [Création d’un réseau virtuel](virtual-networks-create-vnet-classic-cli.md).

## Création du groupe de sécurité réseau pour le sous-réseau frontal
Pour créer un groupe de sécurité réseau nommé **NSG-FrontEnd** selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.

2. Exécutez la commande **`azure config mode`** pour passer en mode classique, comme illustré ci-dessous.

		azure config mode asm

	Sortie attendue :

		info:    New mode is asm

3. Exécutez la commande **`azure network nsg create`** pour créer un groupe de sécurité réseau.

		azure network nsg create -l uswest -n NSG-FrontEnd

	Sortie attendue :

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : NSG-FrontEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Paramètres :

	- **-l (ou --location)**. Région Azure où le groupe de sécurité réseau sera créé. Pour notre scénario, *westus*.
	- **-n (ou --name)**. Nom du nouveau groupe de sécurité réseau. Pour notre scénario, *NSG-FrontEnd*.

4. Exécutez la commande **`azure network nsg rule create`** pour créer une règle qui autorise l'accès au port 3389 (RDP) à partir d'Internet.

		azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Sortie attendue :

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : rdp-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 3389
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Paramètres :

	- **-a (ou --nsg-name)**. Nom du groupe de sécurité réseau dans lequel la règle sera créée. Pour notre scénario, *NSG-FrontEnd*.
	- **-n (ou --name)**. Nom de la nouvelle règle. Pour notre scénario, *rdp-rule*.
	- **-c (ou--action)**. Niveau d’accès de la règle (Deny ou Allow).
	- **-p (ou --protocol)**. Protocole (TCP, UDP ou *) de la règle.
	- **-r (ou --type)**. Direction de la connexion (Inbound ou Outbound).
	- **-y (ou --priority)**. Priorité de la règle.
	- **-f (ou --source-address-prefix)**. Préfixe de l’adresse source dans CIDR ou à l’aide de balises par défaut.
	- **-o (ou --source-port-range)**. Port source ou plage de ports.
	- **-e (ou --destination-address-prefix)**. Préfixe de l’adresse de destination dans CIDR ou à l’aide de balises par défaut.
	- **-u (ou --destination-port-range)**. Port de destination ou plage de ports.

5. Exécutez la commande **`azure network nsg rule create`** pour créer une règle qui autorise l'accès au port 80 (HTTP) à partir d'Internet.

		azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Sortie attendue :

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : INTERNET
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Exécutez la commande **`azure network nsg subnet add`** pour lier le groupe de sécurité réseau au sous-réseau frontal.

		azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd

	Sortie attendue :

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-FrontEnd"
		info:    network nsg subnet add command OK

## Création du groupe de sécurité réseau pour le sous-réseau principal
Pour créer un groupe de sécurité réseau nommé *NSG-BackEnd* selon le scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécutez la commande **`azure network nsg create`** pour créer un groupe de sécurité réseau.

		azure network nsg create -l uswest -n NSG-BackEnd

	Sortie attendue :

		info:    Executing command network nsg create
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : NSG-BackEnd
		data:    Location                        : West US
		data:    Security group rules:
		data:    Name                               Source IP           Source Port  Destination IP   Destination Port  Protocol  Type      Action  Prior
		ity  Default
		data:    ---------------------------------  ------------------  -----------  ---------------  ----------------  --------  --------  ------  -----
		---  -------
		data:    ALLOW VNET OUTBOUND                VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Outbound  Allow   65000
		     true   
		data:    ALLOW VNET INBOUND                 VIRTUAL_NETWORK     *            VIRTUAL_NETWORK  *                 *         Inbound   Allow   65000
		     true   
		data:    ALLOW AZURE LOAD BALANCER INBOUND  AZURE_LOADBALANCER  *            *                *                 *         Inbound   Allow   65001
		     true   
		data:    ALLOW INTERNET OUTBOUND            *                   *            INTERNET         *                 *         Outbound  Allow   65001
		     true   
		data:    DENY ALL OUTBOUND                  *                   *            *                *                 *         Outbound  Deny    65500
		     true   
		data:    DENY ALL INBOUND                   *                   *            *                *                 *         Inbound   Deny    65500
		     true   
		info:    network nsg create command OK

	Paramètres :

	- **-l (ou --location)**. Région Azure où le groupe de sécurité réseau sera créé. Pour notre scénario, *westus*.
	- **-n (ou --name)**. Nom du nouveau groupe de sécurité réseau. Pour notre scénario, *NSG-FrontEnd*.

4. Exécutez la commande **`azure network nsg rule create`** pour créer une règle qui autorise l'accès au port 1433 (SQL) à partir du sous-réseau frontal.

		azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Sortie attendue :

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : sql-rule
		data:    Source address prefix           : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination address prefix      : *
		data:    Destination Port                : 1433
		data:    Protocol                        : TCP
		data:    Type                            : Inbound
		data:    Action                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK


5. Exécutez la commande **`azure network nsg rule create`** pour créer une règle qui refuse l'accès à Internet.

		azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80

	Sortie attendue :

		info:    Executing command network nsg rule create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Name                            : web-rule
		data:    Source address prefix           : *
		data:    Source Port                     : *
		data:    Destination address prefix      : INTERNET
		data:    Destination Port                : 80
		data:    Protocol                        : TCP
		data:    Type                            : Outbound
		data:    Action                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Exécutez la commande **`azure network nsg subnet add`** pour lier le groupe de sécurité réseau au sous-réseau back-end.

		azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd

	Sortie attendue :

		info:    Executing command network nsg subnet add
		info:    Looking up the network security group "NSG-BackEndX"
		info:    Looking up the subnet "BackEnd"
		info:    Looking up network configuration
		info:    Creating a network security group "NSG-BackEndX"
		info:    network nsg subnet add command OK

<!---HONumber=AcomDC_0810_2016-->