<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et PowerShell | Microsoft Azure"
   description="Cet article vous guide dans le processus de création d'un réseau virtuel à l'aide du modèle Resource Manager et de connexion à votre réseau local à l'aide d'une connexion de passerelle VPN site à site. Il inclut des étapes supplémentaires permettant de modifier des préfixes d'adresses IP pour les sites locaux existants."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN site à site à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Cet article vous guidera dans la création d’un réseau virtuel et d’une connexion VPN site à site à votre réseau local à l’aide du modèle de déploiement Azure Resource Manager.

>[AZURE.IMPORTANT]Il est important de comprendre qu'Azure fonctionne actuellement avec deux modèles de déploiement : Resource Manager et classique. Avant de commencer votre configuration, assurez-vous que vous comprenez les modèles de déploiement et les outils. Pour plus d'informations sur les modèles de déploiement, consultez [Modèles de déploiement Azure](../azure-classic-rm.md).

Vous pouvez sélectionner l'article correspondant au modèle de déploiement et l'outil de déploiement à l'aide des onglets ci-dessus. Par exemple, si vous souhaitez créer une connexion de passerelle VPN site à site à l'aide du modèle de déploiement classique dans le portail Azure, au lieu d'utiliser le modèle Resource Manager, cliquez sur l’onglet **Portail Azure** (ci-dessus) pour ouvrir [Créer une connexion VPN de site à site dans le portail Azure](vpn-gateway-site-to-site-create.md).



## Avant tout chose

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Voir l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).
- Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
- La version la plus récente des applets de commande Azure PowerShell. Vous pouvez télécharger et installer la dernière version à partir de la section Windows PowerShell de la [page de téléchargement](http://azure.microsoft.com/downloads/). 
- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

>[AZURE.IMPORTANT]Si vous n'êtes pas familiarisé avec la configuration de votre périphérique VPN ou avec les plages d'adresses IP situées sur la configuration de votre réseau local, vous devez vous coordonner avec une personne qui peut fournir les informations.

## Connexion à votre abonnement 


Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

		Add-AzureAccount

Si vous avez plusieurs abonnements, utilisez *Select-AzureSubscription* pour vous connecter à l’abonnement que vous voulez utiliser.

		Select-AzureSubscription "yoursubscription"

Basculez ensuite sur le mode Azure Resource Manager. Vous n’aurez bientôt plus à basculer sur ce mode, et les applets de commande pour les deux modes seront automatiquement disponibles.

		Switch-AzureMode -Name AzureResourceManager


## Créer un réseau virtuel et un sous-réseau de passerelle

- Si vous disposez déjà d’un réseau virtuel avec un sous-réseau de passerelle, vous pouvez passer d’emblée à [Ajouter votre site local](#add-your-local-site). 
- Si vous disposez déjà d’un réseau virtuel et que vous voulez ajouter un sous-réseau de passerelle à votre réseau virtuel, consultez [Ajouter un sous-réseau de passerelle à un réseau virtuel](#gatewaysubnet).

### Pour créer un réseau virtuel et un sous-réseau de passerelle

Utilisez l’exemple ci-dessous pour créer un réseau virtuel et un sous-réseau de passerelle. Remplacez les valeurs par les vôtres.

Créez d’abord un groupe de ressources :

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Ensuite, créez votre réseau virtuel Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.

L’exemple ci-dessous crée un réseau virtuel nommé *testvnet* et deux sous-réseaux, un nommé *GatewaySubnet* et l’autre *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous le nommez autrement, la configuration de votre connexion échouera.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel (facultatif)

Cette étape est requise uniquement si vous avez besoin d’ajouter un sous-réseau de passerelle à un réseau virtuel.

Si vous disposez déjà d’un réseau virtuel existant et que vous voulez y ajouter un sous-réseau de passerelle, vous pouvez créer votre sous-réseau de passerelle en utilisant l’exemple ci-dessous. Assurez-vous de nommer le sous-réseau de passerelle « GatewaySubnet ». Si vous choisissez un autre nom, vous créerez un sous-réseau, mais Azure ne l’identifiera pas comme un sous-réseau de passerelle.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

À présent, définissez la configuration.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Ajouter votre site local

Dans un réseau virtuel, le *site local* fait généralement référence à votre emplacement local. Vous donnez à ce site un nom qui sera utilisé par Azure pour le référencer.

Vous allez également spécifier le préfixe de l’espace d’adressage pour le site local. Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers le site local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous voulez associer au site local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local est modifié.

Lorsque vous utilisez les exemples PowerShell, notez les points suivants :
	
- *GatewayIPAddress* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT. 
- *AddressPrefix* est votre espace d’adressage local.

Pour ajouter un site local avec un préfixe d'adresse unique :

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Pour ajouter un site local avec plusieurs préfixes d'adresses :

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')


### Pour modifier des préfixes d'adresses IP de votre site local

Parfois, les préfixes de votre site local changent. Les étapes à suivre pour modifier vos préfixes d'adresses IP varient selon que vous avez créé une connexion à la passerelle VPN. Consultez [Modifier des préfixes d'adresses IP de votre site local](#modify-ip-address-prefixes-for-a-local-site).



## Demander une adresse IP publique pour la passerelle

Ensuite, vous allez demander qu’une adresse IP publique soit allouée à votre passerelle VPN du réseau virtuel Azure. Il ne s’agit pas de l’adresse IP affectée à votre périphérique VPN, mais de celle qui est affectée à la passerelle VPN Azure elle-même. Vous ne pouvez pas spécifier l’adresse IP que vous voulez utiliser, car elle est allouée dynamiquement à votre passerelle. Vous utilisez cette adresse IP lors de la configuration de votre périphérique VPN local pour la connexion à la passerelle.

Utilisez l’exemple PowerShell ci-dessous. La méthode d’allocation pour cette adresse doit être dynamique.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Créer la configuration de l’adressage IP de la passerelle

La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## Créer la passerelle

Dans cette étape, vous allez créer la passerelle de réseau virtuel. Notez que la création d'une passerelle prend un certain temps.

Utilisez les valeurs suivantes :

- Le type de passerelle est *Vpn*.
- Le type Vpn peut être Basé sur des routes* (appelé passerelle dynamique dans certaines documentations) ou *Basé sur des stratégies* (appelé passerelle statique dans certaines documentations). Pour plus d'informations sur les types de passerelles VPN, consultez [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Configuration de votre périphérique VPN

À ce stade, vous aurez besoin de l’adresse IP publique de la passerelle de réseau virtuel pour configurer votre périphérique VPN local. Consultez le fabricant de votre périphérique pour des informations de configuration spécifiques. Vous pouvez aussi vous reporter à [Périphériques VPN](http://go.microsoft.com/fwlink/p/?linkid=615099) pour plus d'informations.

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant :

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Créer la connexion VPN

Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer les valeurs pas les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Après un bref délai, la connexion sera établie.

## Vérifier une connexion VPN

À ce stade, les connexions VPN de site à site créées avec Resource Manager ne sont pas visibles dans la version préliminaire du portail. Vous pouvez néanmoins vérifier que votre connexion a réussi à l'aide de la commande *get-azurevirtualnetworkgatewayconnection –Debug*. Nous proposerons prochainement à cet effet une applet de commande, avec la possibilité d'afficher votre connexion dans la version préliminaire du portail.

Vous pouvez utiliser l'exemple d'applet de commande suivant, en le configurant selon vos propres valeurs. Lorsque vous y êtes invité, sélectionnez *A* pour exécuter tout.

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Une fois l'applet de commande exécutée, faites défiler pour afficher les valeurs. Dans l'exemple ci-dessous, l'état de la connexion indique *Connecté*, et vous pouvez voir les octets d'entrée et de sortie.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## Modifier des préfixes d'adresses IP de votre site local

Si vous devez modifier les préfixes de votre site local, utilisez les instructions ci-dessous. Deux jeux d'instructions sont fournis et varient selon que vous avez déjà créé votre connexion à la passerelle VPN.

### Ajouter ou supprimer des préfixes sans une connexion à la passerelle VPN


- **Pour ajouter** d’autres préfixes d'adresses à un site local que vous avez créé, mais qui ne dispose pas encore d’une connexion à la passerelle VPN, utilisez l'exemple ci-dessous.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')



- **Pour supprimer** un préfixe d'adresse d'un site local qui ne dispose pas d’une connexion VPN, utilisez l'exemple ci-dessous. Abandonnez les préfixes dont vous n'avez plus besoin. Dans cet exemple, le préfixe 20.0.0.0/24 (de l'exemple précédent) n'est plus nécessaire. Nous allons donc modifier le site local et exclure ce préfixe.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Ajouter ou supprimer des préfixes avec une connexion à la passerelle VPN

Si vous avez créé votre connexion VPN et que vous souhaitez ajouter ou supprimer des préfixes d'adresses IP contenues dans votre site local, vous devez effectuer les étapes suivantes dans l'ordre. Cela interrompra votre connexion VPN car vous devrez supprimer et recréer la passerelle. Toutefois, étant donné que vous avez demandé une adresse IP pour la connexion, vous n’aurez pas besoin de reconfigurer votre routeur VPN local, sauf si vous décidez de modifier les valeurs que vous avez déjà utilisées.

 
1. Supprimez la connexion à la passerelle. 
2. Modifiez les préfixes de votre site local. 
3. Créez une connexion de passerelle. 

Vous pouvez utiliser l'exemple suivant comme référence.


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



## Étapes suivantes

Ajoutez une machine virtuelle à votre réseau virtuel. [Créez une machine virtuelle](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=Sept15_HO3-->