<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et PowerShell | Microsoft Azure"
   description="Créer une connexion VPN de site à site de votre réseau virtuel à votre emplacement local en utilisant Azure Resource Manager et PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Cette rubrique vous guidera dans la création d’un réseau virtuel Azure Resource Manager et d’une connexion VPN de site à site à votre réseau local.

Azure propose actuellement deux modèles de déploiement : le modèle de déploiement classique et le modèle de déploiement Azure Resource Manager. La configuration de site à site diffère selon le modèle qui a été utilisé pour déployer votre réseau virtuel. Ces instructions s’appliquent à Resource Manager. Si vous voulez créer une connexion de passerelle VPN de site à site en utilisant le modèle de déploiement classique, consultez [Créer une connexion VPN de site à site dans le portail de gestion](vpn-gateway-site-to-site-create.md).


## Avant de commencer

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un périphérique VPN compatible (et une personne qui est en mesure de le configurer). Consultez [À propos des périphériques VPN](vpn-gateway-vpn-devices.md).
- Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
- La version la plus récente des applets de commande Azure PowerShell. Vous pouvez télécharger et installer la dernière version à partir de la section Windows PowerShell de la [page de téléchargement](http://azure.microsoft.com/downloads/). 
- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).
	

## Connexion à votre abonnement 


Ouvrez votre console PowerShell et passez en mode Azure Resource Manager. Utilisez l’exemple suivant pour faciliter votre connexion :

		Add-AzureAccount

Utilisez Select-AzureSubscription pour vous connecter à l’abonnement que vous voulez utiliser.

		Select-AzureSubscription "yoursubscription"

Passez ensuite au mode ARM. Vous passez ainsi au mode qui vous permet d’utiliser les applets de commande ARM.

		Switch-AzureMode -Name AzureResourceManager


## Créer un réseau virtuel et un sous-réseau de passerelle

- Si vous avez disposez déjà d’un réseau virtuel avec un sous-réseau de passerelle, vous pouvez passer d’emblée à [Ajouter votre site local](#add-your-local-site). 
- Si vous disposez d’un réseau virtuel et que vous voulez ajouter un sous-réseau de passerelle à votre réseau virtuel, consultez [Ajouter un sous-réseau de passerelle à un réseau virtuel](#gatewaysubnet).

### Pour créer un réseau virtuel et un sous-réseau de passerelle

Utilisez l’exemple ci-dessous pour créer un réseau virtuel et un sous-réseau de passerelle. Remplacez les valeurs par les vôtres.

Créez d’abord un groupe de ressources :

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Ensuite, créez votre réseau virtuel L’exemple ci-dessous crée un réseau virtuel nommé *testvnet* et deux sous-réseaux, un nommé *GatewaySubnet* et l’autre *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous le nommez autrement, la configuration de votre connexion échouera.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel

Si vous disposez déjà d’un réseau virtuel existant et que vous voulez y ajouter un sous-réseau de passerelle, vous pouvez créer votre sous-réseau de passerelle en utilisant l’exemple ci-dessous. Assurez-vous de nommer le sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la configuration de votre VPN ne fonctionnera pas comme prévu.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Ajouter votre site local

Dans un réseau virtuel, le *site local* fait généralement référence à votre emplacement local. Vous donnez à ce site un nom qui sera utilisé par Azure pour le référencer.

Vous allez également spécifier le préfixe de l’espace d’adressage pour le site local. Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers le site local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous voulez associer au site local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local est modifié. Utilisez les exemples PowerShell ci-dessous pour spécifier votre site local.

	
- *GatewayIPAddress* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT. 
- *AddressPrefix* est votre espace d’adressage local.

Utilisez cet exemple pour ajouter votre site local :

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## Demander une adresse IP publique de la passerelle de réseau virtuel

Ensuite, vous allez demander qu’une adresse IP publique soit allouée à votre passerelle VPN du réseau virtuel Azure. Il ne s’agit pas de l’adresse IP affectée à votre périphérique VPN, mais de celle qui est affectée à la passerelle VPN Azure elle-même. Vous ne pouvez pas spécifier l’adresse IP que vous voulez utiliser, car elle est allouée dynamiquement à votre passerelle. Vous utilisez cette adresse IP lors de la configuration de votre périphérique VPN local pour la connexion à la passerelle.

Utilisez l’exemple PowerShell ci-dessous. La méthode d’allocation pour cette adresse doit être dynamique.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Créer la configuration de l’adressage IP de la passerelle

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Créer la passerelle

Dans cette étape, vous allez créer la passerelle de réseau virtuel. Utilisez les valeurs suivantes :

- Le type de passerelle est *Vpn*.
- Le type Vpn peut être Basé sur des routes\* (appelé passerelle dynamique dans certaines documentations) ou *Basé sur des stratégies* (appelé passerelle statique dans certaines documentations). Pour plus d’informations sur les types de passerelles VPN, consultez [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Configuration de votre périphérique VPN

À ce stade, vous aurez besoin de l’adresse IP publique de la passerelle de réseau virtuel pour configurer votre périphérique VPN local. Consultez le fabricant de votre périphérique pour des informations de configuration spécifiques. Vous pouvez aussi vous reporter à [Périphériques VPN](http://go.microsoft.com/fwlink/p/?linkid=615099)pour plus d’informations.

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant :

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Créer la connexion VPN

Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer les valeurs pas les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Après quelques minutes, la connexion doit être établie. À ce stade, les connexions VPN de site à site créées avec Resource Manager ne sont pas visibles dans le portail.


## Étapes suivantes

Ajoutez une machine virtuelle à votre réseau virtuel. [Créez une machine virtuelle](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=July15_HO5-->