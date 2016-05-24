<properties
   pageTitle="Créer un réseau virtuel avec une connexion VPN de site à site en utilisant Azure Resource Manager et le portail Azure | Microsoft Azure"
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
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Créer un réseau virtuel avec une connexion VPN de site à site à l’aide du Portail Azure et d’Azure Resource Manager

> [AZURE.SELECTOR]
- [Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portail Azure Classic](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Cet article vous guide lors de la création d’un réseau virtuel et d’une connexion VPN de site à site à votre réseau local, à l’aide du modèle de déploiement Azure Resource Manager et du portail Azure. Dans les étapes suivantes, vous créez un réseau virtuel et ajoutez un sous-réseau de passerelle, une passerelle, un site local et une connexion. En outre, vous devez également configurer votre périphérique VPN.



**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Diagramme de connexions

![De site à site](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site2site.png)

**Modèles de déploiement et outils pour les connexions de site à site**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site-table](../../includes/vpn-gateway-table-site-to-site-include.md)]

Si vous souhaitez établir une connexion entre des réseaux virtuels, mais si vous ne créez pas une connexion à un emplacement local, consultez [configurer une connexion de réseau virtuel à réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md). Si vous cherchez un autre type de configuration de connexion, consultez l’article [Topologies de connexion à la passerelle VPN Azure](vpn-gateway-topology.md).

## Avant de commencer

Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

- Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous n’êtes pas familiarisé avec la configuration de votre périphérique VPN ou avec les plages d’adresses IP situées dans la configuration de votre réseau local, vous devez vous associer à une personne qui peut vous fournir ces informations.

- Une adresse IP publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
	
- Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](http://azure.microsoft.com/pricing/free-trial/).

### <a name="values"></a>Exemples de valeurs de configuration pour cet exercice


Lorsque vous suivez ces étapes dans le cadre d’un exercice, vous pouvez utiliser les exemples de valeurs de configuration suivantes :

- Nom du réseau virtuel : TestVNet1
- Espace d’adressage :10.11.0.0/16 et 10.12.0.0/16
- Sous-réseaux : 
	- FrontEnd : 10.11.0.0/24
	- BackEnd : 10.12.0.0/24
	- GatewaySubnet : 10.12.255.0/27
- Groupe de ressources : TestRG1
- Emplacement : Est des États-Unis
- Serveur DNS : 8.8.8.8
- Nom de passerelle : VNet1GW
- Adresse IP publique : VNet1GWIP
- Type de VPN : Route-based
- Type de connexion : Site-to-site (IPsec)
- Type de passerelle : VPN
- Nom de passerelle de réseau local : Site2
- Nom de connexion : VNet1toSite2



## 1\. Créez un réseau virtuel 

Si vous avez déjà créé un réseau virtuel, vérifiez que les paramètres sont compatibles avec votre conception de passerelle VPN. Faites particulièrement attention à tous les sous-réseaux qui peuvent se chevaucher avec d’autres réseaux. Si vos sous-réseaux se chevauchent, votre connexion ne fonctionnera pas correctement. Si vous avez vérifié la configuration appropriée de votre réseau virtuel, vous pouvez commencer à suivre les étapes de la section [Spécifier un serveur DNS](#dns).

### Pour créer un réseau virtuel

[AZURE.INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## 2\. Ajouter des sous-réseaux et des espaces d’adressage supplémentaires

Vous pouvez ajouter des sous-réseaux et des espaces d’adressage supplémentaires pour votre réseau virtuel une fois qu’il a été créé.

[AZURE.INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="dns"></a>3. Spécifier un serveur DNS

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous spécifiez votre serveur DNS.

### Pour spécifier un serveur DNS

[AZURE.INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## 4\. Créer un sous-réseau de passerelle

Avant de connecter votre réseau virtuel à une passerelle, vous devez tout d’abord créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Le sous-réseau de passerelle que vous créez doit être nommé *GatewaySubnet*, sinon il ne fonctionnera pas correctement.

Le préfixe de sous-réseau de passerelle pour certaines configurations requiert un sous-réseau de /28 ou plus pour prendre en charge le nombre d’adresses IP requises dans le pool. Cela signifie que le préfixe de sous-réseau de passerelle doit être /28, /27, /26, etc. Vous souhaiterez peut-être créer un sous-réseau plus grand afin de prendre en charge les éventuels ajouts de configuration futurs.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre sous-réseau de passerelle.

### Pour créer un sous-réseau de passerelle

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## 5\. Créer une passerelle de réseau virtuel

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre passerelle.

### Pour créer une passerelle de réseau virtuel

[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## 6\. Créer une passerelle de réseau local

La *passerelle de réseau local* fait généralement référence à votre emplacement local. Vous donnez à cette passerelle de réseau local un nom qui sera utilisé par Azure pour la référencer.

Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre site local.

### Pour créer une passerelle de réseau local

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## 7\. Configuration de votre périphérique VPN

[AZURE.INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## 8\. Créer une connexion VPN de site à site.

Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer ces valeurs par les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN.

Avant de commencer cette section, vérifiez que la création de votre passerelle de réseau virtuel et des passerelles de réseau local est terminée. Si vous créez cette configuration dans le cadre d’un exercice, reportez-vous à ces [valeurs](#values) lorsque vous créez votre connexion.

### Pour créer la connexion VPN


[AZURE.INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-rm-portal-include.md)]

## 9\. Vérifier la connexion VPN

Vous pouvez vérifier votre connexion VPN dans le portail ou à l’aide de PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## Étapes suivantes

- Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Voir le [parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/virtual-machines) sur les machines virtuelles pour plus d’informations.

- Pour plus d’informations sur le protocole BGP, consultez la [vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et la page [Comment configurer BGP sur des passerelles VPN](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0518_2016-->