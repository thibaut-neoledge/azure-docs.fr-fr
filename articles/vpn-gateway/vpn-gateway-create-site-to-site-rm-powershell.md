<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans le processus de création d’un réseau virtuel à l’aide du modèle Resource Manager et de connexion à votre réseau local à l’aide d’une connexion de passerelle VPN S2S."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/16/2016"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et PowerShell

> [AZURE.SELECTOR]
- [Portail Azure Classic](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Cet article vous guide lors de la création d’un réseau virtuel et d’une connexion VPN site à site à votre réseau local, à l’aide du modèle de déploiement **Azure Resource Manager**. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

![Diagramme de site à site](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site à site")

**Modèles de déploiement et outils pour les connexions de site à site**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Avant de commencer

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN ou avec les plages d’adresses IP situées dans la configuration de votre réseau local, vous devez vous associer à une personne qui peut vous fournir ces informations.

- Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
	
- Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
	
- Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


## 1\. Connexion à votre abonnement 

Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

	Login-AzureRmAccount

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription 

Spécifiez l’abonnement que vous souhaitez utiliser.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. Créer un réseau virtuel et un sous-réseau de passerelle

Les exemples ci-dessous illustrent un sous-réseau de passerelle de /28. Bien qu’il soit possible de créer un sous-réseau de passerelle de /29, nous vous déconseillons de le faire. Nous vous recommandons de créer un sous-réseau de passerelle /27 ou de taille supérieure (/26, /25, etc.) afin de prendre en charge des fonctionnalités supplémentaires. Si vous disposez déjà d’un réseau virtuel avec un sous-réseau de passerelle /29 ou de taille supérieure, vous pouvez passer d’emblée à l’[Étape 3 : Ajouter votre passerelle de réseau local](#localnet).

### Pour créer un réseau virtuel et un sous-réseau de passerelle

Utilisez l’exemple ci-dessous pour créer un réseau virtuel et un sous-réseau de passerelle. Remplacez les valeurs par les vôtres.

Créez d’abord un groupe de ressources :
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

Ensuite, créez votre réseau virtuel Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.

L’exemple ci-dessous crée un réseau virtuel nommé *testvnet* et deux sous-réseaux, l’un nommé *GatewaySubnet* et l’autre *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous le nommez autrement, la configuration de votre connexion échouera.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel que vous avez déjà créé

Cette étape est requise uniquement si vous avez besoin d’ajouter un sous-réseau de passerelle à un réseau virtuel créé au préalable.

Vous pouvez créer votre sous-réseau de passerelle à l’aide de l’exemple ci-dessous. Assurez-vous de nommer le sous-réseau de passerelle « GatewaySubnet ». Si vous choisissez un autre nom, vous créerez un sous-réseau, mais Azure ne le traitera pas comme un sous-réseau de passerelle.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

À présent, définissez la configuration.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>Ajouter votre passerelle de réseau local

Dans un réseau virtuel, la passerelle de réseau local fait généralement référence à votre emplacement local. Vous donnerez à cet emplacement un nom auquel Azure pourra se référer, puis vous spécifierez le préfixe de l’espace d’adressage de la passerelle de réseau local.

Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers votre emplacement local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous voulez associer à votre passerelle de réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local est modifié.

Lorsque vous utilisez les exemples PowerShell, notez les points suivants :
	
- *GatewayIPAddress* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT. 
- *AddressPrefix* est votre espace d’adressage local.

Pour ajouter une passerelle de réseau local avec un préfixe d’adresse unique :

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Pour ajouter une passerelle de réseau local avec des préfixes d’adresse multiples :

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Pour modifier des préfixes d’adresses IP de votre passerelle de réseau local

Parfois, les préfixes de votre passerelle de réseau local changent. Les étapes à suivre pour modifier vos préfixes d'adresses IP varient selon que vous avez créé une connexion à la passerelle VPN. Consultez la section [Modifier les préfixes d’adresse IP d’une passerelle de réseau local](#modify) de cet article.


## 4\. Demander une adresse IP publique de la passerelle VPN

Ensuite, vous allez demander qu’une adresse IP publique soit allouée à votre passerelle VPN du réseau virtuel Azure. Il ne s’agit pas de l’adresse IP affectée à votre périphérique VPN, mais de celle qui est affectée à la passerelle VPN Azure elle-même. Vous ne pouvez pas spécifier l’adresse IP que vous voulez utiliser, car elle est allouée dynamiquement à votre passerelle. Vous utilisez cette adresse IP lors de la configuration de votre périphérique VPN local pour la connexion à la passerelle.

Utilisez l’exemple PowerShell ci-dessous. La méthode d’allocation pour cette adresse doit être dynamique.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] Actuellement, la passerelle VPN Azure du modèle de déploiement de Resource Manager ne prend en charge que les adresses IP publiques à l’aide de la méthode d’allocation dynamique. Néanmoins, cela ne signifie pas que l’adresse IP est modifiée. L’adresse IP de la passerelle VPN Azure change uniquement lorsque la passerelle est supprimée, puis recréée. L’adresse IP publique de la passerelle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN Azure.

## 5\. Créer la configuration de l’adressage IP de la passerelle

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Créer la passerelle de réseau virtuel

Dans cette étape, vous allez créer la passerelle de réseau virtuel. Notez que la création d’une passerelle peut durer un certain temps. Souvent 20 minutes, voire plus.

Utilisez les valeurs suivantes :

- Le paramètre **- GatewayType** pour une configuration de site à site est **Vpn**. Le type de passerelle dépend toujours de la configuration que vous implémentez. Par exemple, d’autres configurations de passerelle peuvent nécessiter GatewayType ExpressRoute. 

- Le paramètre **-VpnType** peut avoir pour valeur **RouteBased** (appelé passerelle dynamique dans certaines documentations) ou **PolicyBased** (appelé passerelle statique dans certaines documentations). Pour plus d’informations sur les types de passerelles VPN, consultez [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md#vpntype).
- La valeur de **-GatewaySku** peut être **Basic**, **Standard**, ou **HighPerformance**. 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7\. Configuration de votre périphérique VPN

À ce stade, vous aurez besoin de l’adresse IP publique de la passerelle de réseau virtuel pour configurer votre périphérique VPN local. Consultez le fabricant de votre périphérique pour des informations de configuration spécifiques. Vous pouvez aussi vous reporter à [Périphériques VPN](vpn-gateway-about-vpn-devices.md) pour plus d’informations.

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant :

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Créer la connexion VPN

Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer ces valeurs par les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN. Notez que le `-ConnectionType` de site à site est **IPsec**.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Après un bref délai, la connexion sera établie.

## 9\. Vérifier une connexion VPN

Il existe différentes façons de vérifier votre connexion VPN. Ci-dessous, vous verrez comment effectuer des vérifications de base à l’aide du portail Azure et de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Pour modifier des préfixes d’adresses IP d’une passerelle de réseau local

Si vous devez modifier les préfixes de votre passerelle de réseau local, suivez les instructions ci-dessous. Deux ensembles d’instructions vous sont fournis : Les instructions que vous choisissez d’appliquer varient selon que vous avez déjà créé votre connexion à la passerelle VPN.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0330_2016-->