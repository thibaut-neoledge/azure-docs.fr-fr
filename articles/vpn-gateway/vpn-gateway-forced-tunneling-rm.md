<properties pageTitle="Configurer le tunneling forcé pour les passerelles VPN à l’aide de Resource Manager" | Microsoft Azure" description="Si vous disposez d’un réseau virtuel avec passerelle réseau entre sites, vous pouvez rediriger ou "forcer" l’ensemble du trafic lié à votre emplacement local. Cet article concerne le modèle de déploiement du Gestionnaire de ressources. "services ="passerelle vpn"documentationCenter ="na"authors ="cherylmc"manager ="carolz"editor = » « balises = « Gestionnaire de Ressources azure » / > <tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# Configurer le tunneling forcé à l’aide de PowerShell et d’Azure Resource Manager

Cet article s’applique aux réseaux virtuels et passerelles VPN créés à l’aide du modèle de déploiement Azure Resource Manager. Si vous souhaitez configurer le tunneling forcé à l’aide du modèle de déploiement de gestion des services, consultez [Configurer le tunneling forcé](vpn-gateway-about-forced-tunneling.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## À propos du tunneling forcé

Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise. Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fera toujours à partir de l’infrastructure du réseau Azure directement vers Internet, sans vous laisser inspecter ou vérifier le trafic. L’accès Internet non autorisés est susceptible d’entraîner la divulgation d’informations ou tout autre type de violation de sécurité.

Le diagramme suivant illustre le fonctionnement du tunneling forcé.

![Tunneling forcé](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Dans l’exemple ci-dessus, le sous-réseau frontal n’utilise pas le tunneling forcé. Les charges de travail du sous-réseau frontal peuvent continuer à accepter et à répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux intermédiaire et principal utilisent le tunneling forcé. Toutes les connexions sortantes à partir de ces deux sous-réseaux vers Internet seront forcées ou redirigées vers un site local via l’un des tunnels VPN S2S. Cela vous permet de restreindre et d'inspecter les accès à Internet à partir de vos machines virtuelles ou des services cloud dans Azure, tout en continuant à activer votre architecture de service multiniveaux requise. Vous avez également la possibilité d’appliquer le tunneling forcé à tous les réseaux virtuels s’il n’existe aucune charge de travail sur Internet dans vos réseaux virtuels.

## Conditions requises et éléments à prendre en compte

Le tunneling forcé dans Azure est configuré via les itinéraires de réseau virtuel défini par l’utilisateur. La redirection du trafic vers un site local est exprimée comme un itinéraire par défaut vers la passerelle VPN Azure. Pour plus d’informations sur les itinéraires et les réseaux virtuels définis par l’utilisateur, consultez [Itinéraires définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).

- Chaque sous-réseau du réseau virtuel dispose d’une table de routage système intégrée. La table de routage système comporte les 3 groupes d’itinéraires suivants :

	- **Itinéraire de réseau virtuel local :** directement vers les machines virtuelles de destination dans le même réseau virtuel
	
	- **Sur les itinéraires locaux :** vers la passerelle VPN Azure
	
	- **Itinéraire par défaut :** directement vers Internet. Les paquets destinés à des adresses IP privées non couvertes par les deux itinéraires précédents seront supprimés.

-  Cette procédure utilise des itinéraires définis par l’utilisateur (UDR) pour une table de routage pour ajouter un itinéraire par défaut, puis associer la table de routage à vos sous-réseaux pour activer le tunneling forcé sur ces sous-réseaux.

- Le tunneling forcé doit être associé à un réseau virtuel équipé d’une passerelle VPN avec itinéraire. Vous devez définir un « site par défaut » parmi les sites locaux intersites connectés au réseau virtuel.

- Le tunneling forcé ExpressRoute n'est pas configuré via ce mécanisme, mais il est activé en publiant un itinéraire par défaut via les sessions d'homologation BGP ExpressRoute. Pour plus d’informations, consultez [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).

## Configurer un tunneling forcé ?

### Présentation de la configuration

La procédure suivante vous aide à créer un groupe de ressources et un réseau virtuel. Vous créerez ensuite une passerelle VPN et configurerez le tunneling forcé.

Dans l’exemple, le réseau virtuel multiniveau comporte 3 sous-réseaux : *frontal*, *intermédiaire* et *principal*, et 4 connexions entre locaux : *DefaultSiteHQ* et 3 *branches*. Les étapes de la procédure définissent *DefaultSiteHQ* en tant que connexion de site par défaut pour le tunneling forcé et configurent les sous-réseaux *intermédiaire* et *principal* de manière à utiliser le tunneling forcé.

	
### Avant tout chose

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

- Dernière version des applets de commande Azure PowerShell utilisant Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de [Web Platform Installer](http://aka.ms/webpi-azps/). Cette documentation a été écrite pour PowerShell 1.0 ou version ultérieure. Les applets de commande nécessaires pour cette configuration ne sont pas présents dans les versions antérieures. Pour plus d’informations sur PowerShell 1.0, consultez [Azure PowerShell 1.0 en version préliminaire](https://azure.microsoft.com/blog/azps-1-0-pre/)

- Si vous n’êtes pas familiarisé avec l’utilisation d’Azure Resource Manager et PowerShell, consultez [cet article](../articles/powershell-azure-resource-manager.md) pour plus d’informations.

### Configuration

1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.

		Login-AzureRmAccount 

2. Obtenez la liste de vos abonnements Azure.

		Get-AzureRmSubscription

2. Spécifiez votre nom d’abonnement Azure.

		Get-AzureRmSubscription -SubscriptionName "YourSubscriptionName" | Select-AzureRmSubscription
		
3. Créez un groupe de ressources

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Créez un réseau virtuel et spécifiez vos sous-réseaux.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Créez les passerelles de réseau local.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. Créez la table d’itinéraires et la règle de routage.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. Associez la table d’itinéraire vers le niveau intermédiaire et les sous-réseaux principaux.

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. Créez la passerelle avec un site par défaut. Cette opération prend un certain temps pour s’accomplir, parfois de 20 minutes ou plus, car vous créez et configurez la passerelle. Il se présente comme quelques applets de commande dans la console, mais beaucoup de choses se passent dans les coulisses. Notez que GatewayDefaultSite est le paramètre d’applet de commande qui permet à la configuration d’itinéraire forcée de fonctionner. C’est pour cette raison que vous ne voudrez pas ignorer cette dernière. Il est disponible uniquement dans PowerShell 1.0 ou ultérieur.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. Établir les connexions VPN de site à site

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_1125_2015-->