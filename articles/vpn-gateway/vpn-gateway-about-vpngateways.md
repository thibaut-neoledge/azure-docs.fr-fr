<properties 
   pageTitle="À propos de la passerelle VPN | Microsoft Azure"
   description="En savoir plus sur les connexions par passerelle VPN pour réseaux virtuels Azure."
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
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# À propos de la passerelle VPN


Les passerelles VPN sont un ensemble de paramètres conçus pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements locaux. Les passerelles VPN sont utilisées pour les connexions de site à site et de point à site ainsi que pour les connexions ExpressRoute. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure (connexion de réseau virtuel à réseau virtuel).

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel. Pour créer une connexion, vous ajoutez la passerelle de réseau virtuel à un réseau virtuel et configurez d’autres paramètres de passerelle VPN. Dans certains cas, la connexion que vous créez est une connexion VPN. Dans d’autres cas, votre configuration ne nécessite pas de VPN. La collection de paramètres est appelée « Passerelle VPN », qu’un réseau privé virtuel soit requis pour votre connexion ou non.

Lorsque vous configurez votre passerelle VPN, les instructions que vous utilisez varient selon le modèle de déploiement que vous avez utilisé pour créer votre réseau virtuel. Par exemple, si vous avez créé votre réseau virtuel à l’aide du modèle de déploiement classique, vous utilisez les recommandations et les instructions pour le modèle de déploiement classique afin de créer et configurer les paramètres de votre passerelle VPN. Pour plus d’informations, voir [Présentation du déploiement Resource Manager et du déploiement classique](../resource-manager-deployment-model.md).

Les sections suivantes contiennent des tableaux répertoriant les informations suivantes pour la configuration :

- Modèle de déploiement disponible
- Outils de configuration disponibles
- Liens vous redirigeant directement vers un article, si disponibles


Utilisez les graphiques et les descriptions pour sélectionner la topologie de configuration répondant à vos besoins. Le graphique présente les principales topologies de base, mais il est possible de créer des configurations plus complexes à l’aide des diagrammes. Chaque configuration repose sur les paramètres de passerelle VPN que vous sélectionnez.

Une passerelle VPN étant une collection de paramètres, vous pouvez configurer certains paramètres à l’aide d’un outil et basculer vers un autre par la suite. Vous ne pouvez actuellement pas configurer tous les paramètres de passerelle VPN depuis le portail Azure. Les instructions fournies dans les articles pour chaque configuration indiquent si un outil spécifique est requis. Si vous utilisez le modèle de déploiement classique, il peut être utile de travailler dans le portail classique ou d’utiliser PowerShell pour l’instant. Pour plus d’informations sur les différents paramètres disponibles, consultez [À propos des paramètres de la passerelle VPN](vpn-gateway-about-vpn-gateway-settings.md).


## Connexions site à site et multi-sites

### De site à site

Une connexion site à site (S2S) est une connexion via un tunnel VPN IPsec/IKE (S2S ou IKEv1). Ce type de connexion requiert un périphérique VPN local auquel est affectée une IP publique, et qui ne se situe pas derrière un NAT. Les connexions S2S peuvent être utilisées pour les configurations hybrides et entre différents locaux.

![Connexion S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site à site")


### Multi-sites

Vous pouvez créer et configurer une connexion VPN entre votre réseau virtuel et plusieurs réseaux locaux. Lorsque vous travaillez avec plusieurs connexions, vous devez utiliser un type de VPN basé sur l’itinéraire (passerelle dynamique pour réseaux virtuels classiques). Un réseau virtuel ne pouvant disposer que d’une seule passerelle de réseau virtuel, toutes les connexions passant par la passerelle partagent la bande passante disponible. Ce type de configuration est souvent appelé connexion « multi-sites ».
 

![Connexion multi-sites](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-sites")

### Outils et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Connexion entre deux réseaux virtuels

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN Azure pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels.

Les réseaux virtuels que vous connectez peuvent être situés :

- dans la même région ou dans des régions différentes
- dans le même abonnement ou dans des abonnements différents
- dans le même modèle de déploiement ou dans des modèles de déploiement différents



![Connexion de réseau virtuel à réseau virtuel](./media/vpn-gateway-about-vpngateways/demov2v.png "Connexion entre deux réseaux virtuels")



### Connexions entre modèles de déploiement

Azure propose actuellement deux modèles de déploiement : le modèle classique et le modèle Resource Manager. Si vous utilisez Azure depuis un certain temps, vous avez probablement des machines virtuelles et des rôles d’instance Azure exécutés dans un réseau virtuel classique. Il est possible que vos nouvelles machines virtuelles et instances de rôle s’exécutent dans un réseau virtuel créé dans Resource Manager. Vous pouvez créer une connexion entre les réseaux virtuels pour permettre aux ressources dans un réseau virtuel de communiquer directement avec les ressources d’un autre réseau virtuel.

### Homologation de réseaux virtuels

Vous pouvez utiliser l’homologation de réseau virtuel pour créer votre connexion dans la mesure où votre configuration de réseau virtuel répond à certaines exigences. L’homologation de réseau virtuel n’utilise pas de passerelle de réseau virtuel. [L’homologation de réseau virtuel](../virtual-network/virtual-network-peering-overview.md) est actuellement proposée dans sa version préliminaire.


### Outils et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## De point à site

Une configuration de point à site (P2S) vous permet de créer individuellement une connexion sécurisée à un réseau virtuel à partir d’un ordinateur client. Le P2S est une connexion VPN sur SSTP (Secure Socket Tunneling Protocol). Les connexions de ce type ne nécessitent pas de périphérique VPN ou d’adresse IP publique pour fonctionner. Vous établissez la connexion VPN en la démarrant depuis l’ordinateur client. Ceci est une solution utile lorsque vous souhaitez vous connecter à votre réseau virtuel à partir d’un site distant, comme depuis votre domicile ou une conférence ou lorsque seulement quelques clients doivent se connecter à un réseau virtuel.


![Connexion de point à site](./media/vpn-gateway-about-vpngateways/demop2s.png "point à site")

### Outils et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]


## ExpressRoute

[AZURE.INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).


## Coexistence de connexions ExpressRoute et de site à site

ExpressRoute est une connexion directe et dédiée aux services Microsoft, notamment à Azure, à partir de votre WAN, qui ne passe pas par l’Internet public. Le trafic VPN de site à site transite via l’Internet public tout en étant chiffré. La possibilité de configurer des connexions VPN de site à site et ExpressRoute pour le même réseau virtuel présente plusieurs avantages. Vous pouvez configurer un VPN de site à site comme un chemin d’accès de basculement sécurisé pour ExpressRoute, ou utiliser des VPN de site à site pour vous connecter à des sites qui ne font pas partie de votre réseau, mais qui sont connectés via ExpressRoute.


![Connexion de coexistence](./media/vpn-gateway-about-vpngateways/demoer.png "Expressroute-site à site")


### Outils et modèles de déploiement

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Étapes suivantes

Pour plus d’informations sur les passerelles VPN, consultez [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md)

Connectez votre emplacement local à un réseau virtuel. Consultez [Créer une connexion VPN de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).





 

<!---HONumber=AcomDC_0824_2016-->