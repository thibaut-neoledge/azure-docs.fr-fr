<properties 
   pageTitle="Contrôle du routage et utilisation des appliances virtuelles à l'aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à contrôler le routage dans des réseaux VNet à l'aide de PowerShell dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Contrôle du routage et utilisation des appliances virtuelles (classiques) à l'aide de PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Cet article traite du modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes Azure PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu'elles sont présentées dans ce document, créez l'environnement décrit dans [Créer un VNet (classique) à l'aide de PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Création des itinéraires définis par l'utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécutez l'applet de commande **`New-AzureRouteTable`** pour créer une table de routage pour le sous-réseau frontal.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	Output:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Exécutez l'applet de commande **`Set-AzureRoute`** pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau Backend (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4).
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	Output:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Exécutez l'applet de commande **`Set-AzureSubnetRouteTable`** pour associer la table de routage créée ci-dessus au sous-réseau **frontal**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## Création des itinéraires définis par l'utilisateur (UDR) pour le sous-réseau Backend
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau Backend selon le scénario ci-dessus, suivez les étapes ci-dessous.

3. Exécutez l'applet de commande **`New-AzureRouteTable`** pour créer une table de routage pour le sous-réseau Backend.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. Exécutez l'applet de commande **`Set-AzureRoute`** pour créer un itinéraire dans la table de routage créée ci-dessus pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4).

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. Exécutez l'applet de commande **`Set-AzureSubnetRouteTable`** pour associer la table de routage créée ci-dessus au sous-réseau **Backend**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName BackEnd `
			-RouteTableName UDR-BackEnd

## Activer le transfert IP sur la machine virtuelle FW1
Pour activer le transfert IP sur la machine virtuelle FW1, suivez les étapes ci-dessous.

1. Exécutez l'applet de commande **`Get-AzureIPForwarding`** pour vérifier le statut du transfert IP

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Output:

		Disabled

2. Exécutez la commande **`Set-AzureIPForwarding`** pour activer le transfert IP pour la machine virtuelle *FW1*.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0720_2016-->