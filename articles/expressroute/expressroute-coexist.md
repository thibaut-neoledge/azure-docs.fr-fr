<properties
   pageTitle="Configurer des connexions ExpressRoute et VPN de site à site pouvant coexister"
   description="Ce didacticiel vous guide tout au long de la configuration d’une connexion ExpressRoute et d’une connexion VPN de site à site pouvant coexister."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />

<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/05/2015"
   ms.author="cherylmc"/>


# Configurer des connexions ExpressRoute et VPN de site à site coexistantes

Vous pouvez désormais connecter ExpressRoute et un réseau privé virtuel de site à site à un même réseau virtuel. Vous pouvez choisir entre deux scénarios et deux procédures de configuration.

## Scénarios

### Scénario 1

Dans ce scénario, vous disposez d’un réseau local. ExpressRoute est le lien actif, et votre VPN de site à site constitue un outil de secours. En cas d’échec de la connexion ExpressRoute, la connexion VPN de site à site devient active. Ce scénario est mieux approprié pour la haute disponibilité.

![Coexister](media/expressroute-coexist/scenario1.jpg)



### Scénario 2

Dans ce scénario, vous disposez de deux réseaux locaux. Vous pouvez connecter l’un d’eux à Azure via ExpressRoute, et l’autre via le VPN de site à site. Dans ce cas, les deux connexions sont actives simultanément.

![Coexister](media/expressroute-coexist/scenario2.jpg)


## Création et configuration

Vous pouvez choisir entre deux procédures différentes pour configurer vos connexions pour qu'elles coexistent. La procédure de configuration que vous sélectionnez varie selon que vous disposez déjà d’un réseau virtuel auquel vous connecter ou que vous voulez créer un réseau virtuel.

- **Créer un réseau virtuel et des connexions qui coexistent  :** 
	
	si vous ne disposez pas déjà d’un réseau virtuel, cette procédure vous guide dans la création d’un réseau virtuel et dans l’établissement de nouvelles connexions ExpressRoute et VPN de site à site. Pour procéder à la configuration, suivez les étapes décrites dans la rubrique [Créer un réseau virtuel et des connexions](#create-a-new-virtual-network-and-connections-that-coexist).

- **Configurer votre réseau virtuel existant pour les connexions coexistantes :**
	
	vous disposez peut-être déjà d’un réseau virtuel avec une connexion VPN de site à site existante ou une connexion ExpressRoute. La procédure [Configurer des connexions qui coexistent pour votre réseau virtuel existant](#configure-connections-that-coexist-for-your-existing-virtual-network) vous guide dans la suppression de la passerelle, puis la création de connexions ExpressRoute et VPN de site à site. Notez que vous devez effectuer les étapes dans un ordre très spécifique lorsque vous créez les connexions. N’utilisez pas les instructions contenues dans d’autres articles pour créer des connexions et des passerelles.

	Lors de cette procédure, si vous créez des connexions pouvant coexister, vous devez supprimer votre passerelle, puis configurer de nouvelles passerelles. En d’autres termes, vous subissez un temps d’arrêt pour les connexions entre différents locaux lorsque vous supprimez et recréez la passerelle et les connexions, mais vous ne devez pas migrer les ordinateurs virtuels ou les services vers un nouveau réseau virtuel. Les ordinateurs virtuels et les services sont toujours en mesure de communiquer via l’équilibrage de charge lorsque vous configurez votre passerelle s’ils sont configurés pour ce faire.


## Remarques et limitations

- Vous ne pouvez pas créer d’itinéraire (via Azure) entre votre réseau local connecté via le réseau privé virtuel de site à site et votre réseau local connecté via ExpressRoute.
- Vous ne pouvez pas activer de connexions VPN point à site avec le même réseau virtuel connecté à ExpressRoute. Des connexions VPN points à site et ExpressRoute ne peuvent pas coexister pour un même réseau virtuel.
- La passerelle ExpressRoute et la passerelle VPN de site à site doivent être une référence (SKU) de passerelle Standard ou HighPerformance. Pour plus d’informations sur les références de passerelle, consultez la rubrique [Références (SKU) de passerelle](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Si votre réseau local est connecté à ExpressRoute et à un réseau privé virtuel de site à site (scénario 1), vous devez avoir configuré un itinéraire statique sur votre réseau local pour acheminer la connexion VPN de site à site vers l’Internet public. 
- Vous devez commencer par créer la passerelle ExpressRoute avant d’ajouter la passerelle VPN de site à site.
- Les deux procédures partent du principe que vous disposez déjà d’un circuit ExpressRoute configuré. Si ce n’est pas le cas, consultez les articles suivants : 

	- [Configuration d’une connexion ExpressRoute via un fournisseur de services réseau](expressroute-configuring-nsps.md) 
	- [Configuration d’une connexion ExpressRoute via un fournisseur Exchange](expressroute-configuring-exps.md)


## Créer un réseau virtuel et des connexions qui coexistent

Cette procédure vous guide dans la création d'un réseau virtuel et dans l’établissement de nouvelles connexions de site à site et ExpressRoute qui coexisteront.

1. Vérifiez que vous disposez de la dernière version des applets de commande PowerShell. Vous pouvez télécharger et installer les dernières applets de commande PowerShell à partir de la section PowerShell de la [page de téléchargement](http://azure.microsoft.com/downloads/).
2. Créez un schéma pour votre réseau virtuel. Pour plus d’informations sur l’utilisation du fichier de configuration réseau, consultez la rubrique [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](../virtual-network/virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur le schéma de configuration, consultez la page [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Lorsque vous créez votre schéma, veillez à utiliser les valeurs suivantes :

	- Le sous-réseau de la passerelle du réseau virtuel doit être défini sur /27 (ou un préfixe plus court).
	- La connexion à la passerelle est du type « dédié ».

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />

		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>



3. Après avoir créé et configuré votre fichier de schéma xml, chargez-le vers le serveur. Cette opération crée votre réseau virtuel.

	Utilisez l’applet de commande suivante pour charger votre fichier vers le serveur en remplaçant la valeur par la vôtre.

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Créez une passerelle ExpressRoute. Veillez à spécifier la valeur *Standard* ou *HighPerformance* pour le paramètre GatewaySKU, et la valeur *DynamicRouting* pour le paramètre GatewayType. 

	Utilisez l’exemple ci-dessous en utilisant vos propres valeurs.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Liez la passerelle ExpressRoute au circuit ExpressRoute. Une fois cette étape terminée, la connexion entre votre réseau local et Azure est établie via ExpressRoute.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. Créez ensuite la passerelle VPN de site à site. Vous devez spécifier la valeur *Standard* ou *HighPerformance* pour le paramètre GatewaySKU, et la valeur *DynamicRouting* pour le paramètre GatewayType.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

	Pour récupérer les paramètres de la passerelle de réseau virtuel, y compris l’ID de passerelle et l’adresse IP publique, utilisez l’applet de commande `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway
		
		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Créez une entité de passerelle VPN de site local. Cette commande ne configure pas votre passerelle VPN locale. Elle vous permet d’indiquer les paramètres de la passerelle locale, par exemple l’adresse IP publique et l’espace d’adressage local afin que la passerelle VPN Azure puisse s’y connecter.

	**Important :** Le site local du réseau privé virtuel de site à site n’est pas défini dans le fichier netcfg. Vous devez utiliser cette applet de commande pour spécifier les paramètres du site local. Vous ne pouvez pas le définir à l’aide du portail de gestion ou du fichier netcfg.

	Utilisez l’exemple suivant en remplaçant les valeurs par les vôtres :

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	Pour récupérer les paramètres de la passerelle de réseau virtuel, y compris l’ID de passerelle et l’adresse IP publique, utilisez l’applet de commande `Get-AzureVirtualNetworkGateway`. Prenons l'exemple suivant :

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. Configurez votre périphérique VPN local à connecter à la nouvelle passerelle. Utilisez les informations que vous avez récupérées à l’étape 6 lors de la configuration de votre périphérique VPN. Pour plus d’informations sur la configuration du périphérique VPN, consultez la rubrique [Configuration de périphérique VPN](http://go.microsoft.com/fwlink/p/?linkid=615099).
	

9. Liez la passerelle VPN de site à site dans Azure à la passerelle locale.

	Dans cet exemple, connectedEntityId est l’ID de la passerelle locale que vous pouvez trouver en exécutant `Get-AzureLocalNetworkGateway`. Vous pouvez trouver virtualNetworkGatewayId à l’aide de l’applet de commande `Get-AzureVirtualNetworkGateway`. Après cette étape, la connexion entre votre réseau local et Azure est établie via la connexion VPN de site à site.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## Configurer des connexions qui coexistent pour votre réseau virtuel existant

Si vous disposez d’un réseau virtuel connecté via ExpressRoute ou une connexion VPN de site à site, vous devez d’abord supprimer la passerelle existante pour permettre aux deux connexions de se connecter au réseau virtuel existant. Cela signifie que votre site local perd la connexion à votre réseau virtuel via la passerelle lorsque vous effectuez cette configuration.

**Avant de commencer la configuration :** Vérifiez que vous disposez de suffisamment d’adresses IP restantes dans votre réseau virtuel pour que vous puissiez augmenter la taille du sous-réseau de passerelle.


1. Téléchargez la toute dernière version des applets de commande PowerShell. Vous pouvez télécharger et installer les dernières applets de commande PowerShell à partir de la section PowerShell de la [page de téléchargement](http://azure.microsoft.com/downloads/).
 
2. Supprimez la passerelle VPN de site à site existante. Utilisez l’applet de commande suivante en remplaçant les valeurs par les vôtres.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Exportez le schéma du réseau virtuel. Utilisez l’applet de commande PowerShell suivante en remplaçant les valeurs par les vôtres.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. Modifiez le schéma du fichier de configuration réseau pour que le sous-réseau de passerelle soit défini sur /27 (ou un préfixe plus court). Reportez-vous à l’exemple ci-dessous. Pour plus d’informations sur l’utilisation du fichier de configuration réseau, consultez la rubrique [Configuration d’un réseau virtuel à l’aide d’un fichier de configuration réseau](../virtual-network/virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur le schéma de configuration, consultez la page [Schéma de configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. Si votre passerelle précédente était une passerelle VPN de site à site, vous devez également modifier le type de connexion et le définir sur **Dédié**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />

		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. À ce stade, vous disposez d’un réseau virtuel sans passerelles. Vous pouvez passer à l’**étape 3** de l’article [Créer un réseau virtuel et des connexions](#create-a-new-virtual-network-and-connections-that-coexist) pour créer des passerelles et finaliser vos connexions.



## Étapes suivantes
	
En savoir plus sur ExpressRoute. Consultez la rubrique [Présentation d’ExpressRoute](expressroute-introduction.md).

En savoir plus sur les passerelles VPN. Consultez la rubrique [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=August15_HO6-->