<properties 
   pageTitle="Connecter plusieurs sites locaux à un réseau virtuel"
   description="Cet article vous guidera pour la connexion de plusieurs sites locaux à un réseau virtuel."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/04/2015"
   ms.author="cherylmc" />


# Connecter plusieurs sites locaux à un réseau virtuel

Vous pouvez connecter plusieurs sites locaux à un même réseau virtuel. Cela est particulièrement intéressant pour la création de solutions de cloud hybrides. La création d’une connexion sur plusieurs sites à votre passerelle de réseau virtuel Azure est très similaire à la création d’autres connexions de site à site. En fait, vous pouvez utiliser une passerelle VPN Azure existante, dans la mesure où vous disposez d’une passerelle VPN basée sur un itinéraire (ou routage dynamique) configurée pour votre réseau virtuel.

Si votre passerelle est basée sur une stratégie (ou le routage statique), vous pouvez toujours modifier le type de passerelle sans avoir à régénérer le réseau virtuel pour prendre en charge plusieurs sites. Toutefois, vous devrez vérifier que votre passerelle VPN locale prend en charge les configurations de VPN basées sur un itinéraire. Vous pouvez alors ajouter les paramètres de configuration au fichier de configuration du réseau et créer plusieurs connexions VPN à partir de votre réseau virtuel pour les sites supplémentaires.

![VPN multisite](./media/vpn-gateway-multi-site/IC727363.png)

## Éléments à prendre en considération

**Vous ne pourrez pas utiliser le portail de gestion pour apporter des modifications à ce réseau virtuel.** Pour cette version, vous devez apporter des modifications au fichier de configuration réseau au lieu d’utiliser le portail de gestion. Si vous apportez des modifications dans le portail de gestion, elles remplaceront vos paramètres de référence multisite pour ce réseau virtuel. Vous aurez le temps de vous familiariser avec le fichier de configuration réseau d’ici la fin de la procédure multisite. Toutefois, si plusieurs personnes travaillent sur la configuration de votre réseau, vous devez vous assurer que tout le monde ait connaissance de cette limitation. Cela ne signifie pas que vous ne pouvez pas du tout utiliser le portail de gestion. Vous pouvez l’utiliser pour toutes les procédures, à l’exception des modifications de configuration de ce réseau virtuel spécifique.

## Avant de commencer

Avant de commencer la configuration, vérifiez que les conditions suivantes sont remplies :

- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

- Matériel VPN compatible pour chaque emplacement local. Consultez [À propos des périphériques VPN pour la connectivité au réseau virtuel](http://go.microsoft.com/fwlink/p/?linkid=615099) pour vérifier si le périphérique que vous souhaitez utiliser est bien compatible.

- Une adresse IP IPv4 publique exposée en externe pour chaque périphérique VPN. L’adresse IP ne peut pas se trouver derrière un NAT. Cela est obligatoire.

-   La version la plus récente des applets de commande Azure PowerShell. Vous pouvez télécharger et installer la dernière version à partir de la section Windows PowerShell de la page [Téléchargement](http://azure.microsoft.com/downloads/).

- Un expert en matière de configuration du matériel VPN. Vous ne pourrez pas utiliser les scripts VPN générés automatiquement à partir du portail de gestion pour configurer vos périphériques VPN. Cela signifie que vous devez avoir une bonne maîtrise de la façon de configurer votre périphérique VPN ou collaborer avec quelqu’un disposant des connaissances nécessaires.

- Les plages d’adresses IP que vous souhaitez utiliser pour votre réseau virtuel (si vous n’en avez pas déjà créé).

- Les plages d’adresses IP pour chacun des sites du réseau local auxquels vous vous connecterez. Vous devez vous assurer que les plages d’adresses IP pour chacun des sites du réseau local auxquels vous souhaitez vous connecter ne se chevauchent ne pas. Dans le cas contraire, le portail de gestion ou l’API REST rejettera la configuration en cours de téléchargement. Par exemple, si vous avez deux sites de réseau local contenant la plage d’adresse IP 10.2.3.0/24 et que vous disposez d’un package avec une adresse de destination 10.2.3.3, Azure ne saura pas à quel site envoyer le package, car les plages d’adresses se chevauchent. Pour éviter les problèmes de routage, Azure ne vous permet pas de télécharger un fichier de configuration ayant des plages qui se chevauchent.

## Créer votre réseau virtuel et votre passerelle

1. **Créez un VPN de site à site avec une passerelle de routage dynamique.** Si vous en avez déjà un, très bien ! Vous pouvez [Exporter les paramètres de configuration de réseau virtuel](#export). Puis, procédez ainsi :

	**Si vous disposez déjà d’un réseau virtuel de site à site, mais qu’il est doté d’une passerelle de routage statique :****1.** Modifiez le type de passerelle en routage dynamique. Un VPN multisite requiert une passerelle de routage dynamique. Pour modifier le type de passerelle, vous devez d’abord supprimer la passerelle existante, puis en créer une nouvelle. Pour obtenir des instructions, consultez[Modifier un type de routage de passerelle VPN](vpn-gateway-configure-vpn-gateway-mp.md/#how-to-change-your-vpn-gateway-type). **2.** Configurez votre nouvelle passerelle et créez votre tunnel VPN. Pour obtenir des instructions, consultez [Configurer une passerelle VPN dans le portail de gestion](vpn-gateway-configure-vpn-gateway-mp.md).
	
	**Si vous n’avez pas de réseau virtuel de site à site :****1.** Créez votre réseau virtuel de site à site en suivant cette procédure :[Créer un réseau virtuel avec une connexion VPN de site à site dans le portail de gestion](vpn-gateway-site-to-site-create.md). **2.** Configurez une passerelle de routage dynamique en suivant cette procédure :[Configurer une passerelle VPN dans le portail de gestion](vpn-gateway-configure-vpn-gateway-mp.md). Veillez à sélectionner le **routage dynamique** pour le type de passerelle.



1. **<a name="export"></a>Exporter les paramètres de configuration de réseau virtuel.** Pour exporter le fichier de configuration de votre réseau, consultez [Pour exporter vos paramètres réseau](../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Le fichier que vous exportez permet de configurer vos nouveaux paramètres multisites.

1. **Ouvrir le fichier de configuration réseau.** Ouvrez le fichier de configuration réseau que vous avez téléchargé à l’étape précédente. Utilisez un éditeur xml qui vous convient. Le fichier doit se présenter ainsi :

		<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
		  <VirtualNetworkConfiguration>
		    <LocalNetworkSites>
		      <LocalNetworkSite name="Site1">
		        <AddressSpace>
		          <AddressPrefix>10.0.0.0/16</AddressPrefix>
		          <AddressPrefix>10.1.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
		      </LocalNetworkSite>
		      <LocalNetworkSite name="Site2">
		        <AddressSpace>
		          <AddressPrefix>10.2.0.0/16</AddressPrefix>
		          <AddressPrefix>10.3.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
		      </LocalNetworkSite>
		    </LocalNetworkSites>
		    <VirtualNetworkSites>
		      <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
		        <AddressSpace>
		          <AddressPrefix>10.20.0.0/16</AddressPrefix>
		          <AddressPrefix>10.21.0.0/16</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="FE">
		            <AddressPrefix>10.20.0.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="BE">
		            <AddressPrefix>10.20.1.0/24</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.20.2.0/29</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="Site1">
		              <Connection type="IPsec" />

		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>
		    </VirtualNetworkSites>
		  </VirtualNetworkConfiguration>
		</NetworkConfiguration>

1. Ajoutez plusieurs références de sites dans le fichier de configuration réseau. Lorsque vous ajoutez ou supprimez les informations de référence de site, des modifications de configuration sont apportées à ConnectionsToLocalNetwork/LocalNetworkSiteRef. À l’ajout d’une nouvelle référence de site local, Azure crée un nouveau tunnel. Dans l’exemple ci-dessous, la configuration du réseau correspond à une connexion de site unique.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" />
</LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

	Pour ajouter des références à des sites supplémentaires (créer une configuration multisite), ajoutez simplement des lignes « LocalNetworkSiteRef » supplémentaires, comme illustré dans l’exemple ci-dessous :

        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" />
</LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" />
</LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

1. **Enregistrez le fichier de configuration réseau et importez-le.** Pour importer le fichier de configuration réseau, consultez [Pour importer vos paramètres réseau](../virtual-network/../virtual-network/virtual-networks-using-network-configuration-file.md#export-and-import-virtual-network-settings-using-the-management-portal). Lorsque vous importez ce fichier avec les modifications, les nouveaux tunnels sont ajoutés. Les tunnels utilisent la passerelle dynamique que vous avez créée précédemment.



1. **Téléchargez les clés prépartagées pour les tunnels VPN.** Une fois les nouveaux tunnels ajoutés, utilisez l’applet de commande PowerShell Get-AzureVNetGatewayKey pour obtenir les clés prépartagées IPsec/IKE pour chaque tunnel.

	Par exemple :

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"

		PS C:\> Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"

	Si vous préférez, vous pouvez également utiliser l’API REST *Obtenir la clé partagée de la passerelle de réseau virtuel* pour obtenir les clés prépartagées.

## Vérifiez vos connexions

**Vérifiez l’état de tunnel multisite.** Après avoir téléchargé les clés de chaque tunnel, vérifiez les connexions. Utilisez *Get-AzureVnetConnection* pour obtenir une liste des tunnels de réseau virtuel, comme indiqué dans l’exemple ci-dessous. VNet1 est le nom du réseau virtuel.

		PS C:\Users\yushwang\Azure> Get-AzureVnetConnection -VNetName VNET1
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 661530
		IngressBytesTransferred   : 519207
		LastConnectionEstablished : 5/2/2014 2:51:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site1
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded
		
		ConnectivityState         : Connected
		EgressBytesTransferred    : 789398
		IngressBytesTransferred   : 143908
		LastConnectionEstablished : 5/2/2014 3:20:40 PM
		LastEventID               : 23401
		LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
		LastEventTimeStamp        : 5/2/2014 2:51:40 PM
		LocalNetworkSiteName      : Site2
		OperationDescription      : Get-AzureVNetConnection
		OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
		OperationStatus           : Succeeded

## Étapes suivantes

Pour en savoir plus sur les passerelles VPN, consultez [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=August15_HO6-->