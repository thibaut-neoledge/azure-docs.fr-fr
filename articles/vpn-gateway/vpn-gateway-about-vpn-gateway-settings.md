<properties 
   pageTitle="À propos des paramètres de la passerelle VPN | Microsoft Azure"
   description="Découvrir les paramètres de passerelle VPN pour réseau virtuel Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="cherylmc" />

# À propos des paramètres de la passerelle VPN

Les passerelles VPN sont un ensemble de ressources conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements locaux. Vous pouvez également utiliser la passerelle VPN pour acheminer le trafic entre des réseaux virtuels dans Azure. Les sections de cet article présentent les ressources et les paramètres relatifs aux passerelles VPN.

Il est parfois utile de visualiser les configurations disponibles à l’aide de diagrammes de connexion. Vous trouverez des diagrammes montrant comment déployer chaque configuration dans l’article [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).


## <a name="gwsku"></a>SKU de passerelle

Lorsque vous créez une passerelle VPN, vous devez spécifier la référence (SKU) de passerelle que vous voulez utiliser. Les SKU de passerelle s’appliquent aux types de passerelle ExpressRoute et VPN. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

Il existe 3 SKU de passerelle VPN :

- De base
- Standard
- HighPerformance

L’exemple PowerShell suivant spécifie la `-GatewaySku` en tant que *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>Débit agrégé estimé par type de SKU et de passerelle


Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>Types de passerelle

Le type de passerelle spécifie la manière dont la passerelle se connecte. Il s’agit d’un paramètre de configuration obligatoire pour le modèle de déploiement Resource Manager. Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel de chaque type. Les valeurs disponibles pour `-GatewayType` sont :

- Vpn
- ExpressRoute


Cet exemple PowerShell pour le modèle de déploiement Resource Manager spécifie la valeur de -GatewayType comme étant *Vpn*. Lorsque vous créez une passerelle, vous devez vous assurer que le type de passerelle convient pour votre configuration.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>Types de connexion

Chaque configuration nécessite un type de connexion spécifique. Les valeurs de PowerShell pour Resource Manager disponibles pour `-ConnectionType` sont :

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Dans l’exemple PowerShell suivant, nous créons une connexion S2S qui nécessite le type de connexion « IPsec ».

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Types de VPN

Chaque configuration nécessite un type de réseau privé virtuel spécifique afin de fonctionner. Lorsque vous créez votre configuration, vous sélectionnez le type de VPN requis pour votre connexion.

Par exemple, si vous souhaitez vous connecter à un réseau virtuel à l’aide de connexions de site à site (S2S) et de point à site (P2S), vous devez utiliser un type de VPN qui répond aux exigences de connexion pour les deux configurations. Les connexions P2S requièrent un type de VPN basé sur un routage (passerelle de routage dynamique), tandis que les connexions S2S peuvent parfois prendre en charge un type de VPN basé sur une stratégie. Cela signifie que, si vous disposez déjà d’une connexion S2S qui utilise un type de VPN basé sur une stratégie (routage statique), vous devez recréer la passerelle à l’aide du type de VPN basé sur un routage (routage dynamique) pour prendre en charge P2S.

Il existe deux types de VPN :

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



Cet exemple PowerShell pour le modèle de déploiement Resource Manager spécifie la valeur `-VpnType` comme étant *RouteBased*. Lorsque vous créez une passerelle, vous devez vous assurer que -VpnType convient pour votre configuration.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Conditions requises de la passerelle

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Sous-réseau de passerelle

Vous devez créer un sous-réseau de passerelle pour votre réseau virtuel afin de configurer une passerelle VPN. Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé *SousRéseau\_Passerelle*. Ce nom permet à Azure de savoir que ce sous-réseau doit être utilisé pour la passerelle.<BR>Si vous utilisez le portail Classic, le sous-réseau de passerelle est automatiquement nommé *Passerelle* dans l’interface du portail. Cela se rapporte uniquement à l’affichage du sous-réseau de passerelle dans le portail classique. Dans ce cas, le sous-réseau est créé avec la valeur *SousRéseau\_Passerelle*, et peut ainsi être affiché dans le portail Azure ainsi que dans PowerShell.

La taille minimale de votre sous-réseau de passerelle dépend entièrement de la configuration que vous voulez créer. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau de passerelle de taille /28 ou supérieure (/28, /27, /26, etc.).

La création d’une passerelle de plus grande taille évite les problèmes liés aux limitations de la taille des passerelles. Par exemple, vous avez peut-être créé une passerelle de réseau virtuel avec une taille de sous-réseau de passerelle /29 pour une connexion S2S. Vous souhaitez à présent définir une configuration coexistante S2S/ExpressRoute. Cette configuration requiert une taille minimale de sous-réseau de passerelle /28. Pour créer votre configuration, vous devez modifier le sous-réseau de passerelle pour prendre en charge la configuration minimale requise pour la connexion, à savoir /28.

L’exemple PowerShell suivant montre un sous-réseau de passerelle nommé SousRéseau\_Passerelle. Vous pouvez voir que la notation CIDR spécifie une taille /27, ce qui permet d’avoir un nombre suffisamment élevé d’adresses IP pour la plupart des configurations actuelles.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Assurez-vous qu’aucun groupe de sécurité réseau (NSG) n’est appliqué au sous-réseau de passerelle, car cela risque de d’entraîner l’échec des connexions.


## <a name="lng"></a>Passerelles de réseau local

La passerelle de réseau local fait généralement référence à votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local a été appelée Site local. Vous donnez un nom à la passerelle de réseau local (l’adresse IP publique du périphérique VPN local) et spécifiez les préfixes d’adresse qui se situent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour votre passerelle de réseau local, et route les paquets en conséquence.

Parfois, vous devez modifier les paramètres de passerelle de réseau local. C’est le cas, par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou lorsque l’adresse IP du périphérique VPN change. Pour un réseau virtuel classique, vous pouvez modifier ces paramètres via la page Réseaux locaux du portail Classic. Pour Resource Manager, voir [Modification des paramètres de passerelle de réseau local à l’aide de PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST et PowerShell

Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et de PowerShell, voir les pages suivantes :

|**Classique** | **Gestionnaire de ressources**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Étapes suivantes

Pour plus d’informations sur les configurations de connexion disponible, consultez la rubrique [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).







 

<!---HONumber=AcomDC_0831_2016-->