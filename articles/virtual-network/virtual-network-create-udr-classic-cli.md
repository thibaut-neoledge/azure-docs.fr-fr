<properties 
   pageTitle="Contrôle du routage et utilisation des appliances virtuelles à l'aide de l'Interface de ligne de commande Azure dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à contrôler le routage dans des réseaux VNet à l'aide de l'Interface de ligne de commande Azure dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/06/2015"
   ms.author="telmos" />

#Contrôle du routage et utilisation des appliances virtuelles (classiques) à l'aide de l'Interface de ligne de commande Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article traite du modèle de déploiement classique. Vous pouvez également [contrôler le routage et utiliser des appliances virtuelles dans le modèle de déploiement Resource Manager](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu'elles sont présentées dans ce document, créez l'environnement décrit dans [Créer un VNet (classique) à l'aide de l'Interface de ligne de commande Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Création des itinéraires définis par l'utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécutez **`azure config mode`** pour basculer vers le mode classique.

		azure config mode asm

	Output:

		info:    New mode is asm

3. Exécutez la commande **`azure network route-table create`** pour créer une table de routage pour le sous-réseau frontal.

		azure network route-table create -n UDR-FrontEnd -l uswest

	Output:

		info:    Executing command network route-table create
		info:    Creating route table "UDR-FrontEnd"
		info:    Getting route table "UDR-FrontEnd"
		data:    Name                            : UDR-FrontEnd
		data:    Location                        : West US
		info:    network route-table create command OK

	Paramètres : - **-l (ou --location)**. Région Azure où le groupe de sécurité réseau sera créé. Pour notre scénario, *westus*. - **-n (ou --name)**. Nom du nouveau groupe de sécurité réseau. Pour notre scénario, *NSG-FrontEnd*.

4. Exécutez la commande **`azure network route-table route set`** pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau Backend (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4).

		azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

	Output:

		info:    Executing command network route-table route set
		info:    Getting route table "UDR-FrontEnd"
		info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    network route-table route set command OK

	Paramètres : - **-r (ou --route-table-name)**. Nom de la table de routage où l'itinéraire sera ajouté. Pour notre scénario, *UDR-FrontEnd*. - **-a (ou --address-prefix)**. Préfixe d'adresse pour le sous-réseau auquel les paquets sont destinés. Pour notre scénario, *192.168.2.0/24*. - **-t (ou --next-hop-type)**. Type d'objet vers lequel le trafic sera envoyé. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*. - **-p (ou --next-hop-ip-address**). Adresse IP de saut suivant. Pour notre scénario, *192.168.0.4*.

5. Exécutez la commande **`azure network vnet subnet route-table add`** pour associer la table de routage créée ci-dessus au sous-réseau **frontal**.

		azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

	Output:

		info:    Executing command network vnet subnet route-table add
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		data:    Route table name                : UDR-FrontEnd
		data:      Location                      : West US
		data:      Routes:
		info:    network vnet subnet route-table add command OK	

	Paramètres : - **-t (ou --vnet-name)**. Nom du réseau virtuel où le sous-réseau est situé. Pour notre scénario, *TestVNet*. - **-n (ou --subnet-name**. Nom du sous-réseau auquel la table de routage sera ajoutée. Pour notre scénario, *FrontEnd*.
 
## Création des itinéraires définis par l'utilisateur (UDR) pour le sous-réseau Backend
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau Backend selon le scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécutez la commande **`azure network route-table create`** pour créer une table de routage pour le sous-réseau Backend.

		azure network route-table create -n UDR-BackEnd -l uswest

4. Exécutez la commande **`azure network route-table route set`** pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4).

		azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Exécutez la commande **`azure network vnet subnet route-table add`** pour associer la table de routage créée ci-dessus au sous-réseau **Backend**.

		azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

<!---HONumber=Oct15_HO2-->