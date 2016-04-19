<properties
   pageTitle="Configurer la coexistence de connexions VPN Expressroute et site à site pour le modèle de déploiement Resource Manager | Microsoft Azure"
   description="Cet article vous guide tout au long de la configuration d’une connexion ExpressRoute et d’une connexion VPN de site à site pouvant coexister pour le modèle de déploiement Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="charwen"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/06/2016"
   ms.author="charleywen"/>

# Configurer la coexistence de connexions de site à site et ExpressRoute pour le modèle de déploiement Resource Manager

> [AZURE.SELECTOR]
- [PowerShell - Resource Manager](expressroute-howto-coexist-resource-manager.md)
- [PowerShell - Classique](expressroute-howto-coexist-classic.md)

La possibilité de configurer des connexions VPN de site à site et ExpressRoute présente plusieurs avantages. Vous pouvez configurer un VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne sont pas connectés via ExpressRoute. Dans cet article, nous décrirons les étapes de configuration des deux scénarios. Cet article s'applique au modèle de déploiement Resource Manager. Cette configuration n'est pas disponible dans le portail Azure.


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

>[AZURE.IMPORTANT] Les circuits ExpressRoute doivent être préconfigurés avant que vous suiviez les instructions ci-dessous. Assurez-vous que vous avez suivi les guides [Créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [Configurer le routage](expressroute-howto-routing-arm.md) avant de suivre les étapes ci-dessous.

## Limites et limitations

- **Le routage de transit n’est pas pris en charge :** vous ne pouvez pas créer d’itinéraire (via Azure) entre votre réseau local connecté via le VPN de site à site et votre réseau local connecté via ExpressRoute.
- **Le tunneling forcé ne peut pas être activé sur la passerelle VPN de site à site :** vous pouvez uniquement utiliser ExpressRoute pour « forcer » l’ensemble du trafic Internet vers votre réseau local. 
- **Passerelles standard ou hautes performances uniquement :** vous devez utiliser une passerelle standard ou hautes performances pour la passerelle ExpressRoute comme pour la passerelle VPN de site à site. Consultez [SKU de passerelle](../vpn-gateway/vpn-gateway-about-vpngateways.md) pour plus d’informations sur les références de passerelle.
- **Uniquement la passerelle VPN basée sur l’itinéraire :** vous devez utiliser une passerelle VPN basée sur un itinéraire. Consultez la page [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) pour plus d’informations sur la passerelle VPN basée sur l’itinéraire.
- **Condition requise d’itinéraire statique :** si votre réseau local est connecté à la fois à ExpressRoute et à un VPN de site à site, vous devez avoir configuré un itinéraire statique sur votre réseau local pour acheminer la connexion VPN de site à site vers l’Internet public.
- **La passerelle ExpressRoute doit d’abord être configurée :** vous devez commencer par créer la passerelle ExpressRoute avant d’ajouter la passerelle VPN de site à site.


## Modèles de configuration

### Configurer un réseau VPN de site à site comme un chemin d’accès de basculement pour ExpressRoute

Vous pouvez configurer une connexion VPN de site à site en tant que sauvegarde pour ExpressRoute. Cela s’applique uniquement aux réseaux virtuels liés au chemin d’homologation privé Azure. Il n’existe aucune solution de basculement basée sur des réseaux VPN pour les services accessibles via les homologations Azure public et Microsoft. Le circuit ExpressRoute est toujours le lien principal. Les données circulent via le chemin d’accès du réseau VPN de site à site uniquement si le circuit ExpressRoute échoue.

![Coexister](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### Configurer un réseau VPN de site à site pour se connecter à des sites non connectés via ExpressRoute

Vous pouvez configurer votre réseau là où certains sites se connectent directement à Azure via des réseaux VPN de site à site ou via ExpressRoute.

![Coexister](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

>[AZURE.NOTE] Vous ne pouvez pas configurer un réseau virtuel comme un routeur de transit.

## Sélection des étapes à suivre

Vous pouvez choisir entre deux procédures différentes pour configurer vos connexions afin qu’elles coexistent. La procédure de configuration que vous sélectionnez varie selon que vous disposez déjà d’un réseau virtuel auquel vous connecter ou que vous voulez créer un réseau virtuel.


- Je n’ai pas de réseau virtuel et dois en créer un
	
	Si vous ne disposez pas déjà d’un réseau virtuel, cette procédure vous guide dans la création d’un réseau virtuel en utilisant le modèle de déploiement Resource Manager et dans l’établissement de nouvelles connexions ExpressRoute et VPN de site à site. Pour procéder à la configuration, suivez les étapes décrites dans la section [Créer un réseau virtuel et des connexions qui coexistent](#new).

- J’ai déjà un réseau virtuel répondant au modèle de déploiement Resource Manager.

	Vous disposez peut-être déjà d’un réseau virtuel avec une connexion VPN de site à site existante ou une connexion ExpressRoute. La section [Configurer des connexions qui coexistent pour un réseau virtuel existant](#add) vous guide tout au long des étapes de suppression de la passerelle et de création de connexions ExpressRoute et VPN de site à site. Notez que vous devez effectuer les étapes dans un ordre très spécifique lorsque vous créez les connexions. N’utilisez pas les instructions contenues dans d’autres articles pour créer des connexions et des passerelles.

	Lors de cette procédure, si vous créez des connexions pouvant coexister, vous devez supprimer votre passerelle, puis configurer de nouvelles passerelles. En d’autres termes, vous subissez un temps d’arrêt pour les connexions entre différents locaux lorsque vous supprimez et recréez la passerelle et les connexions, mais vous ne devez pas migrer les ordinateurs virtuels ou les services vers un nouveau réseau virtuel. Les machines virtuelles et les services sont toujours en mesure de communiquer via l’équilibreur de charge lorsque vous configurez votre passerelle s’ils sont configurés pour ce faire.


## <a name="new"></a>Créer un réseau virtuel et des connexions qui coexistent

Cette procédure vous guide dans la création d’un réseau virtuel et dans l’établissement de nouvelles connexions de site à site et ExpressRoute appelées à coexister.
	
1. Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Les applets de commande que vous utiliserez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions.

2. Connectez-vous à votre compte et configurez l'environnement.
	
		login-AzureRmAccount
		Select-AzureRmSubscription -SubscriptionName 'yoursubscription'
		$location = "Central US"
		$resgrp = New-AzureRmResourceGroup -Name "ErVpnCoex" -Location $location

3. Créez un réseau virtuel et un sous-réseau de passerelle. Pour plus d’informations sur la configuration d'un réseau virtuel, consultez la rubrique [Configuration du réseau virtuel Azure](../virtual-network/virtual-networks-create-vnet-arm-ps.md).

	>[AZURE.IMPORTANT] Le sous-réseau de la passerelle doit être défini sur /27 ou un préfixe plus court (comme /26 ou /25).
	
	Créez un nouveau réseau virtuel.

		$vnet = New-AzureRmVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16" 

	Ajoutez des sous-réseaux.

		Add-AzureRmVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Enregistrez la configuration du réseau virtuel.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

4. <a name="gw"></a>Créez une passerelle ExpressRoute. Pour plus d'informations sur la configuration de la passerelle ExpressRoute, consultez la rubrique [Configuration de la passerelle ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). La valeur de GatewaySKU doit être *Standard* ou *HighPerformance*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		$gw = New-AzureRmVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard 

5. Liez la passerelle ExpressRoute au circuit ExpressRoute. Une fois cette étape terminée, la connexion entre votre réseau local et Azure est établie via ExpressRoute. Pour plus d'informations sur l'opération de liaison, voir l'article [Liaison de réseaux virtuels à ExpressRoute](expressroute-howto-linkvnet-arm.md).

		$ckt = Get-AzureRmExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
		New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute

6. Créez ensuite la passerelle VPN de site à site. Pour plus d'informations sur la configuration de la passerelle VPN, consultez la rubrique [Configuration d'une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md). La valeur de GatewaySKU doit être *Standard* ou *HighPerformance*. La valeur VpnType doit être *RouteBased*.

		$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$gwIP = New-AzureRmPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
		$gwConfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
		New-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "Standard"

7. Créez une entité de passerelle VPN de site local. Cette commande ne configure pas votre passerelle VPN locale. Elle vous permet d’indiquer les paramètres de la passerelle locale, par exemple l’adresse IP publique et l’espace d’adressage local afin que la passerelle VPN Azure puisse s’y connecter.
	>[AZURE.NOTE] Si votre réseau local possède plusieurs itinéraires, vous pouvez tous les transmettre sous la forme d’un tableau. $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")

		$localVpn = New-AzureRmLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix '10.100.0.0/16'

8. Configurez votre périphérique VPN local à connecter à la nouvelle passerelle VPN Azure. Pour plus d’informations sur la configuration du périphérique VPN, consultez la rubrique [Configuration de périphérique VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

9. Liez la passerelle VPN de site à site dans Azure à la passerelle locale.

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		New-AzureRmVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>


## <a name="add"></a>Configurer des connexions qui coexistent pour un réseau virtuel existant

Si vous disposez d’un réseau virtuel connecté via ExpressRoute ou une connexion VPN de site à site, vous devez d’abord supprimer la passerelle existante pour permettre aux deux connexions de se connecter au réseau virtuel existant. Cela signifie que votre site local perd la connexion à votre réseau virtuel via la passerelle lorsque vous effectuez cette configuration.

**Avant de commencer la configuration :** Vérifiez que vous disposez de suffisamment d’adresses IP restantes dans votre réseau virtuel pour que vous puissiez augmenter la taille du sous-réseau de passerelle. Notez que vous devrez supprimer la passerelle et la recréer même si vous disposez de suffisamment d’adresses IP. En effet, la passerelle doit être recréée afin de prendre en charge les connexions qui coexistent.

1. Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Les applets de commande que vous utiliserez pour cette configuration peuvent être légèrement différentes de celles que vous connaissez. Utilisez les applets de commande spécifiées dans ces instructions. 

2. Supprimez la passerelle VPN ExpressRoute ou de site à site existante.

		Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>

3. Supprimez le sous-réseau de la passerelle.
		
		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> 
		Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet

4. Ajoutez un sous-réseau de passerelle défini sur /27 ou plus.

		$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
		Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"

	Enregistrez la configuration du réseau virtuel.

		$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

5. À ce stade, vous disposez d’un réseau virtuel sans passerelles. Pour créer de nouvelles passerelles et finaliser vos connexions, vous pouvez passer à l’[Étape 4 : Créer une passerelle ExpressRoute](#gw), dans les étapes qui précèdent.

## Pour ajouter une configuration point à site à la passerelle VPN
Vous pouvez suivre les étapes ci-dessous pour ajouter une configuration point à site à votre passerelle VPN dans une configuration de coexistence.

1. Ajoutez le pool d’adresses des clients VPN. 

		$azureVpn = Get-AzureRmVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
		Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"

2. Téléchargez le certificat racine VPN dans Azure pour votre passerelle VPN. Dans cet exemple, nous supposons que le certificat racine est stocké dans l'ordinateur local où sont exécutés les applets de commande PowerShell suivantes.

		$p2sCertFullName = "RootErVpnCoexP2S.cer"
		$p2sCertMatchName = "RootErVpnCoexP2S"
		$p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName}
		if ($p2sCertToUpload.count -eq 1){
		    write-host "cert found"
		} else {
		    write-host "cert not found"
		    exit
		} 
		$p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData)
		Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData

Pour plus d'informations sur le réseau VPN point à site, consultez la rubrique [Configuration d'une connexion point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0413_2016-->