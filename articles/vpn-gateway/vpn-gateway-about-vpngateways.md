<properties 
   pageTitle="À propos des passerelles VPN pour la connectivité de réseau virtuel entre sites locaux | Microsoft Azure"
   description="En savoir plus sur les passerelles VPN qui peuvent être utilisées pour les connexions site à site entre différents locaux pour les configurations hybrides, ainsi que pour les connexions entre deux réseaux virtuels et les connexions de point-à-site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# À propos des passerelles VPN

Les passerelles VPN, aussi appelées passerelles de réseau virtuel Azure, sont conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements sur site. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure (connexion de réseau virtuel à réseau virtuel). Les sections ci-dessous abordent les éléments qui se rapportent à une passerelle VPN.

Les instructions que vous utilisez pour créer votre passerelle VPN varient selon le modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre réseau virtuel à l’aide du modèle de déploiement classique, vous utiliserez les recommandations et les instructions pour le modèle de déploiement classique afin de créer et configurer votre passerelle VPN. Il est impossible de créer une passerelle VPN Resource Manager pour un réseau virtuel à base de modèle de déploiement classique.

Pour plus d’informations sur les modèles de déploiement, voir [Présentation du déploiement Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).


## <a name="gwsub"></a>Sous-réseau de passerelle

Vous devez créer un sous-réseau de passerelle pour votre réseau virtuel afin de configurer une passerelle VPN. Tous les sous-réseaux de passerelle doivent être nommés GatewaySubnet afin de fonctionner correctement.

La taille minimale du sous-réseau de passerelle dépend entièrement de la configuration que vous souhaitez créer. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29 pour certaines configurations, nous vous recommandons de créer un sous-réseau de passerelle de /28 ou plus (/28, /27, /26, etc.).

La création d’une passerelle de plus grande taille évite les problèmes liés aux limitations de la taille des passerelles. Par exemple, si vous avez créé une passerelle dont la taille du sous-réseau de passerelle est de /29 et que vous souhaitez configurer des configurations site à site/ExpressRoute coexistantes, vous devrez supprimer la passerelle, supprimer le sous-réseau de passerelle, créer un sous-réseau de passerelle dont la taille est /28 ou supérieure, puis recréer votre passerelle.

En créant un sous-réseau de passerelle d’une taille supérieure dès le départ, vous pouvez gagner du temps ultérieurement lors de l’ajout de nouvelles fonctionnalités de configuration à votre environnement réseau.

L’exemple ci-dessous montre un sous-réseau de passerelle nommé GatewaySubnet. Vous pouvez voir que la notation CIDR spécifie une taille /27, ce qui permet d’avoir un nombre suffisamment élevé d’adresses IP pour la plupart des configurations actuelles.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>Types de passerelle

Le type de passerelle spécifie comment la passerelle se connecte ; il s’agit d’un paramètre de configuration requis pour le modèle de déploiement Resource Manager. Ne confondez pas le type de passerelle avec le type de réseau privé virtuel, qui spécifie le type de routage pour votre réseau privé virtuel. Les valeurs disponibles GatewayType sont : *Vpn* et *ExpressRoute*.


Cet exemple pour le modèle de déploiement Resource Manager spécifie la valeur de -GatewayType comme étant *Vpn*. Lorsque vous créez une passerelle, vous devez vous assurer que le type de passerelle convient pour votre configuration.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>SKU de passerelle

Lorsque vous créez une passerelle VPN, vous devez spécifier la passerelle de référence (SKU) que vous souhaitez utiliser. Il existe 3 SKU de passerelle VPN :

- De base
- Standard
- HighPerformance

L’exemple ci-dessous spécifie la valeur GatewaySku comme étant *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### Débit agrégé estimé par type de SKU et de passerelle


Le tableau ci-dessous indique les types de passerelle et le débit total estimé. La tarification varie en fonction des différents SKU de passerelle. Pour plus d'informations sur la tarification, consultez la [tarification de passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>Types de réseau privé virtuel

Chaque configuration nécessite un type de réseau privé virtuel spécifique afin de fonctionner. Si vous combinez deux configurations, telles que la création d’une connexion de site à site et une connexion de point-à-site au même réseau virtuel, vous devez utiliser un type de réseau privé virtuel qui satisfait les exigences des deux types de connexion. Pour les connexions de point-à-site et de site à site coexistantes, vous devez utiliser un type de VPN basé sur un itinéraire lorsque vous travaillez avec le modèle de déploiement Azure Resource Manager ; si vous travaillez avec le mode de déploiement classique, utilisez une passerelle dynamique.

Lorsque vous créez votre configuration, vous devez sélectionner le type de VPN requis pour votre connexion.

Il existe deux types de VPN :

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Cet exemple pour le modèle de déploiement Resource Manager spécifie la valeur de -VpnType comme étant *RouteBased*. Lorsque vous créez une passerelle, vous devez vous assurer que -VpnType convient pour votre configuration.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## <a name="lng"></a>Passerelles de réseau local

La passerelle de réseau local fait généralement référence à votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local a été appelée Site local. Vous devez donner un nom à la passerelle de réseau local (l’adresse IP publique du périphérique VPN local) et spécifier les préfixes d’adresse qui se trouvent en local. Azure examinera les préfixes d’adresse de destination pour le trafic réseau, consultera la configuration que vous avez spécifiée pour votre passerelle de réseau local, puis routera les paquets en conséquence. Vous pouvez modifier ces préfixes d’adresse en fonction des besoins.



### Modifier les préfixes d’adresse - Resource Manager

Lorsque vous modifiez les préfixes d’adresse, la procédure diffère selon que vous avez déjà créé votre passerelle VPN ou non. Consultez la section [Modifier les préfixes d’adresse d’une passerelle de réseau local](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

Dans l’exemple ci-dessous, vous pouvez voir qu’une passerelle de réseau local nommée MyOnPremiseWest est en cours de spécification. Celle-ci contiendra deux préfixes d’adresses IP.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Modifier les préfixes d’adresse - déploiement classique

Si vous devez modifier vos sites locaux lorsque vous utilisez le modèle de déploiement classique, vous pouvez pour l’instant utiliser la page de configuration Réseaux locaux dans le portail classique, ou modifier directement le fichier de configuration réseau (NETCFG. XML).


## Périphériques VPN

Assurez-vous que le périphérique VPN que vous prévoyez d’utiliser prend en charge le type de VPN requis pour votre configuration. Pour plus d’informations sur les périphériques VPN compatibles, consultez l’article [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md).

## Conditions requises de la passerelle


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## Étapes suivantes

Pour plus d’informations avant de poursuivre avec la planification et la conception de votre configuration, consultez la [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md)





 

<!---HONumber=AcomDC_0323_2016-->