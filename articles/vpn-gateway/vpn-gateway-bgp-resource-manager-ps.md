<properties
   pageTitle="Comment configurer BGP sur des passerelles VPN Azure à l’aide d’Azure Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article vous guide dans la configuration de BGP avec des passerelles VPN Azure à l’aide d’Azure Resource Manager et de PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# Comment configurer BGP sur des passerelles VPN Azure à l’aide d’Azure Resource Manager et de PowerShell

Cet article vous guide pas à pas dans l’activation de BGP sur une connexion VPN de site à site (S2S) et une connexion de réseau virtuel à réseau virtuel, à l’aide du modèle de déploiement de Resource Manager et de PowerShell.


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## À propos du protocole BGP

BGP est le protocole de routage standard couramment utilisé sur Internet pour échanger des informations de routage et d’accessibilité entre plusieurs réseaux. Il permet aux passerelles VPN Azure et à vos périphériques VPN locaux (appelés voisins ou homologues BGP) d’échanger des « itinéraires » informant les deux passerelles sur la disponibilité et l’accessibilité de ces préfixes à travers les passerelles ou routeurs impliqués. Le protocole BGP assure également le routage de transit entre plusieurs réseaux en propageant les itinéraires qu’une passerelle BGP obtient d’un homologue BGP à tous les autres homologues BGP.

Pour plus d’informations sur les avantages de BGP ainsi que les exigences techniques et considérations d’utilisation de BGP, consultez [Vue d’ensemble du protocole BGP avec des passerelles VPN Azure](./vpn-gateway-bgp-overview.md).

## Prise en main de BGP sur les passerelles VPN Azure

Cet article détaille les étapes permettant d’effectuer les tâches suivantes :

- [Partie 1 - Activer BGP sur votre passerelle VPN Azure](#enablebgp)

- [Partie 2 - Établir une connexion intersite avec BGP](#crossprembgp)

- [Partie 3 - Établir une connexion de réseau virtuel à réseau virtuel avec BGP](#v2vbgp)

Chaque partie des instructions constitue un bloc de base pour activer BGP dans votre connectivité réseau. Si vous terminez ces trois parties, vous générez la topologie comme sur le diagramme suivant :

![Topologie BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Vous pouvez les combiner pour créer un réseau de transit plus complexe, à plusieurs tronçons, qui répond à vos besoins.

## <a name ="enablebgp"></a>Partie 1 – Configurer BGP sur la passerelle VPN Azure

Les étapes suivantes configurent les paramètres BGP de la passerelle VPN Azure comme indiqué dans le diagramme suivant :

![Passerelle BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### Avant de commencer

- Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
	
- Vous aurez besoin d’installer les applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

### Étape 1 – Créer et configurer le réseau virtuel VNet1 

#### 1\. Déclarer vos variables

Dans cet exercice, nous allons commencer par déclarer les variables. L’exemple suivant déclare les variables avec les valeurs de cet exercice. Veillez à les remplacer par vos valeurs lors de la configuration dans un contexte de production. Vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez-les dans la console PowerShell.

	$Sub1          = "Replace_With_Your_Subcription_Name"
	$RG1           = "TestBGPRG1"
	$Location1     = "East US"
	$VNetName1     = "TestVNet1"
	$FESubName1    = "FrontEnd"
	$BESubName1    = "Backend"
	$GWSubName1    = "GatewaySubnet"
	$VNetPrefix11  = "10.11.0.0/16"
	$VNetPrefix12  = "10.12.0.0/16"
	$FESubPrefix1  = "10.11.0.0/24"
	$BESubPrefix1  = "10.12.0.0/24"
	$GWSubPrefix1  = "10.12.255.0/27"
	$VNet1ASN      = 65010
	$DNS1          = "8.8.8.8"
	$GWName1       = "VNet1GW"
	$GWIPName1     = "VNet1GWIP"
	$GWIPconfName1 = "gwipconf1"
	$Connection12  = "VNet1toVNet2"
	$Connection15  = "VNet1toSite5"

#### 2\. Se connecter à votre abonnement et créer un groupe de ressources

Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

	Login-AzureRmAccount
	Select-AzureRmSubscription -SubscriptionName $Sub1
	New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### 3\. Créer TestVNet1

L’exemple ci-dessous crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux nommés GatewaySubnet, FrontEnd et Backend. Lorsque vous remplacez les valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue.

	$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
	$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
	$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

	New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### Étape 2 – Créer la passerelle VPN de TestVNet1 avec les paramètres BGP

#### 1\. Créer les configurations IP et de sous-réseau

Demandez l’allocation d’une adresse IP publique à la passerelle que vous allez créer pour votre réseau virtuel. Vous allez également définir les configurations requises (IP et sous-réseau).

	$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
	
	$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
	$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
	$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### 2\. Créer la passerelle VPN avec le numéro AS

Créez la passerelle de réseau virtuel pour TestVNet1. Notez que BGP requiert une passerelle VPN basée sur l’itinéraire ainsi que le paramètre d’ajout, - Asn, pour définir l’ASN (numéro AS) de TestVNet1. La création d’une passerelle peut prendre un certain temps (30 minutes ou plus).

	New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### 3\. Obtenir l’adresse IP de l’homologue BGP Azure

Une fois la passerelle créée, vous devez obtenir l’adresse IP de l’homologue BGP sur la passerelle VPN Azure. Cette adresse est nécessaire pour configurer la passerelle VPN Azure comme un homologue BGP pour vos périphériques VPN locaux.

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
	$vnet1gw.BgpSettingsText

La dernière commande affiche les configurations BGP correspondantes sur la passerelle VPN Azure. Par exemple :

	$vnet1gw.BgpSettingsText
	{
		"Asn": 65010,
		"BgpPeeringAddress": "10.12.255.30",
		"PeerWeight": 0
	}

Une fois la passerelle créée, vous pouvez l’utiliser pour établir une connexion intersite ou de réseau virtuel à réseau virtuel avec BGP. Les sections suivantes détaillent les étapes à effectuer pour terminer l’exercice.

## <a name ="crossprembbgp"></a>Partie 2 - Établir une connexion intersite avec BGP

Pour établir une connexion intersite, vous devez créer une passerelle de réseau local pour représenter votre périphérique VPN local, et une connexion entre la passerelle VPN Azure et la passerelle du réseau local. La différence entre les instructions de cet article réside dans les propriétés supplémentaires requises pour spécifier les paramètres de configuration de BGP.

![BGP pour une connexion intersite](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Avant de poursuivre, vérifiez que vous avez terminé la [Partie 1](#enablebgp) de cet exercice.

### Étape 1 - Créer et configurer la passerelle de réseau local

#### 1\. Déclarer vos variables

Cet exercice continue à générer la configuration représentée dans le diagramme. Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

	$RG5           = "TestBGPRG5"
	$Location5     = "East US 2"
	$LNGName5      = "Site5"
	$LNGPrefix50   = "10.52.255.254/32"
	$LNGIP5        = "Your_VPN_Device_IP"
	$LNGASN5       = 65050
	$BGPPeerIP5    = "10.52.255.254"

Quelques points à noter concernant les paramètres de la passerelle de réseau local :

- La passerelle de réseau local peut se trouver dans les mêmes emplacement et groupe de ressources que la passerelle VPN ou dans un emplacement et un groupe de ressources différents. Cet exemple les montre dans différents groupes de ressources situés dans différents emplacements.

- Le préfixe minimum à déclarer pour la passerelle de réseau local est l’adresse IP hôte de votre homologue BGP sur votre périphérique VPN. Dans ce cas, c’est un préfixe /32 de « 10.52.255.254/32 ».

- À titre de rappel, vous devez utiliser différents ASN BGP entre vos réseaux locaux et le réseau virtuel Azure. S’ils sont identiques, vous devez modifier l’ASN de votre réseau si votre périphérique VPN local utilise déjà cet ASN pour se connecter à d’autres voisins BGP.
	
Avant de continuer, assurez-vous que vous êtes toujours connecté à l’abonnement 1.

#### 2\. Créer la passerelle de réseau local pour le site 5

Veillez à créer le groupe de ressources (si ce n’est déjà fait) avant la passerelle de réseau local. Remarquez les deux paramètres supplémentaires pour la passerelle de réseau local : Asn et BgpPeerAddress.

	New-AzureRmResourceGroup -Name $RG5 -Location $Location5

	New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### Étape 2 - Connecter la passerelle de réseau virtuel et la passerelle de réseau local

#### 1\. Obtenir les deux passerelles

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
		$lng5gw  = Get-AzureRmVirtualNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### 2\. Créer la connexion entre TestVNet1 et Site5

Dans cette étape, vous allez créer la connexion entre TestVNet1 et Site5. Vous devez spécifier « -EnableBGP True » pour activer BGP sur cette connexion. Comme nous l’avons vu, il est possible d’avoir des connexions BGP et non BGP sur la même passerelle VPN Azure. À moins que BGP ne soit activé dans la propriété de connexion, Azure n’active pas BGP sur cette connexion même si les paramètres BGP sont déjà configurés sur les deux passerelles.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True


L’exemple ci-dessous répertorie les paramètres que vous devez saisir dans la section de configuration de BGP sur votre périphérique VPN local pour cet exercice :

	- Site5 ASN            : 65050
	- Site5 BGP IP         : 10.52.255.254
	- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
	- Azure VNet ASN       : 65010
	- Azure VNet BGP IP    : 10.12.255.30
	- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
	- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

La connexion doit s’établir après quelques minutes, et la session d’homologation BGP débute une fois la connexion IPsec établie.
 
## <a name ="v2vbgp"></a>Partie 3 - Établir une connexion de réseau virtuel à réseau virtuel avec le protocole BGP

Cette section ajoute une connexion de réseau virtuel à réseau virtuel avec le protocole BGP, comme illustré dans le diagramme ci-dessous.

![BGP pour une connexion de réseau virtuel à réseau virtuel](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Les instructions ci-dessous sont la suite des étapes précédentes répertoriées plus haut. Vous devez terminer la [Partie 1](#enablebgp) pour créer et configurer TestVNet1 et la passerelle VPN avec le protocole BGP.

### Étape 1 - Créer TestVNet2 et la passerelle VPN

Il est important de s’assurer que l’espace d’adresse IP du nouveau réseau virtuel, TestVNet2, n’empiète sur aucune de vos plages de réseau virtuel.

Dans cet exemple, les réseaux virtuels appartiennent au même abonnement. Vous pouvez configurer des connexions de réseau virtuel à réseau virtuel entre les différents abonnements. Pour en savoir plus, consultez [Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell](./vpn-gateway-vnet-vnet-rm-ps.md). Veillez à ajouter l’argument « -EnableBgp True » lors de la création des connexions pour activer BGP.

#### 1\. Déclarer vos variables

Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

	$RG2           = "TestBGPRG2"
	$Location2     = "West US"
	$VNetName2     = "TestVNet2"
	$FESubName2    = "FrontEnd"
	$BESubName2    = "Backend"
	$GWSubName2    = "GatewaySubnet"
	$VNetPrefix21  = "10.21.0.0/16"
	$VNetPrefix22  = "10.22.0.0/16"
	$FESubPrefix2  = "10.21.0.0/24"
	$BESubPrefix2  = "10.22.0.0/24"
	$GWSubPrefix2  = "10.22.255.0/27"
	$VNet2ASN      = 65020
	$DNS2          = "8.8.8.8"
	$GWName2       = "VNet2GW"
	$GWIPName2     = "VNet2GWIP"
	$GWIPconfName2 = "gwipconf2"
	$Connection21  = "VNet2toVNet1"
	$Connection12  = "VNet1toVNet2"

#### 2\. Créer TestVNet2 dans le nouveau groupe de ressources

	New-AzureRmResourceGroup -Name $RG2 -Location $Location2
	
	$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
	$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
	$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

	New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### 3\. Créer la passerelle VPN de TestVNet2 avec les paramètres BGP

Demandez l’allocation d’une adresse IP publique à la passerelle que vous allez créer pour votre réseau virtuel. Vous allez également définir les configurations requises (IP et sous-réseau).

	$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

	$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
	$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
	$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Créez la passerelle VPN avec le numéro AS. Notez que vous devez substituer la valeur par défaut de l’ASN sur vos passerelles VPN Azure. Les ASN des réseaux virtuels connectés doivent être différents pour activer BGP et le routage de transit.

	New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### Étape 2 - Connecter les passerelles TestVNet1 et TestVNet2

Dans cet exemple, les deux passerelles sont dans le même abonnement. Vous pouvez effectuer cette opération dans la même session PowerShell.

#### 1\. Accéder aux deux passerelles

Veillez à ouvrir une session et à vous connecter à Abonnement 1.

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
	$vnet2gw = Get-AzureRmLocalNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
	
#### 2\. Créer les deux connexions

Dans cette étape, vous allez créer la connexion de TestVNet1 à TestVNet2 et la connexion de TestVNet2 à TestVNet1.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp True

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp True

>[AZURE.IMPORTANT] Veillez à activer BGP pour les deux connexions.

Une fois ces étapes terminées, la connexion s’établit en quelques minutes, et la session d’homologation BGP est ouverte dès la connexion de réseau virtuel à réseau virtuel établie.

Si vous avez effectué les trois parties de cet exercice, vous avez obtenu une topologie de réseau similaire à celle ci-dessous :

![BGP pour une connexion de réseau virtuel à réseau virtuel](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0504_2016-->