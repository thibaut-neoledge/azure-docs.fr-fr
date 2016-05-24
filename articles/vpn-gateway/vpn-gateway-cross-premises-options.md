<properties 
   pageTitle="À propos des connexions sécurisées entre locaux pour les réseaux virtuels| Microsoft Azure"
   description="Découvrez les types de connexions sécurisées entre différents locaux pour les réseaux virtuels, comprenant les connexions site à site, point à site et ExpressRoute."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# À propos des connexions sécurisées entre locaux pour les réseaux virtuels

Cet article présente les différentes méthodes de connexion de votre site local à un réseau virtuel Azure. Il s’applique aux modèles de déploiement classique et Resource Manager. Si vous recherchez des topologies de connexion aux passerelles VPN, consultez l’article [Topologies de connexion à la passerelle VPN Azure](vpn-gateway-topology.md).

Trois options de connexion sont disponibles : site à site, point à site et ExpressRoute. L’option que vous choisirez peut dépendre de la réponse à différentes questions, notamment :


- Quel est le type de débit requis par votre solution ?
- Souhaitez-vous communiquer sur l’Internet public par le biais d’un VPN sécurisé ou sur une connexion privée ?
- Disposez-vous d’une adresse IP publique utilisable ?
- Envisagez-vous d’utiliser un périphérique VPN ? Si tel est le cas, est-il compatible ?
- Ne voulez-vous connecter qu’un petit nombre d’ordinateurs, ou souhaitez-vous mettre en place une connexion permanente pour votre site ?
- Quel est le type de passerelle VPN requis par la solution que vous souhaitez créer ?

Le tableau ci-après peut vous aider à déterminer la meilleure option de connectivité pour votre solution.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## Connexions site à site

Un réseau privé virtuel (VPN) site à site vous permet de créer une connexion sécurisée entre votre site local et votre réseau virtuel. Pour la création d’une connexion site à site, un périphérique VPN situé sur votre réseau local est configuré de façon à créer une connexion sécurisée avec la passerelle VPN Azure. Une fois la connexion créée, les ressources de votre réseau local et celles qui sont situées dans votre réseau virtuel peuvent communiquer directement et de façon sécurisée. Dans le cas des connexions site à site, vous n’avez pas besoin d’établir une connexion distincte pour chacun des ordinateurs clients de votre réseau local pour leur permettre d’accéder aux ressources du réseau virtuel.

**Utilisez une connexion site à site dans les cas suivants :**

- Vous souhaitez créer une solution hybride.
- Vous voulez disposer d’une connexion entre votre emplacement local et votre réseau virtuel qui ne nécessite aucune configuration côté client.
- Vous souhaitez disposer d’une connexion permanente. 

**Configuration requise**

- Le périphérique VPN local doit disposer d’une adresse IP IPv4 d’accès à Internet. Il ne peut pas se trouver derrière un processus de traduction d’adresses réseau (NAT).
- Vous devez disposer d’un périphérique VPN compatible. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). 
- Le périphérique VPN que vous utilisez doit être compatible avec le type de passerelle requis pour votre solution. Voir [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).
- La référence SKU de passerelle aura également une incidence sur le débit agrégé. Pour plus d’informations, voir la section [SKU de passerelle](vpn-gateway-about-vpngateways.md#gwsku). 

**Méthodes et modèles de déploiement disponibles pour les connexions S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## Connexions de point à site

Un réseau privé virtuel point à site vous permet également de créer une connexion sécurisée à votre réseau virtuel. Dans une configuration point à site, la connexion est configurée individuellement sur chaque ordinateur client que vous souhaitez connecter au réseau virtuel. Les connexions point à site ne nécessitent aucun périphérique VPN. Ce type de connexion utilise un client VPN que vous installez sur chaque ordinateur client. Vous établissez le VPN en démarrant manuellement la connexion à partir de l’ordinateur client local.

Les configurations point à site et site à site peuvent coexister, mais contrairement aux connexions site à site, les connexions point à site ne sont pas configurables en même temps qu’une connexion ExpressRoute au même réseau virtuel.

**Utilisez une connexion point à site dans les cas suivants :**

- Vous ne souhaitez configurer qu’un petit nombre de clients pour la connexion à un réseau virtuel.

- Vous souhaitez établir une connexion à votre réseau virtuel à partir d’un emplacement distant, tel qu’un café ou une salle de conférence.

- Vous disposez d’une connexion site à site, mais certains de vos clients doivent se connecter à partir d’un emplacement distant.

- Vous n’avez pas accès à un périphérique VPN qui présente la configuration minimale requise pour une connexion site à site.

- Vous ne disposez pas d’une adresse IP IPv4 d’accès à Internet pour votre périphérique VPN.

**Méthodes et modèles de déploiement disponibles pour les connexions P2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Connexions ExpressRoute

Azure ExpressRoute vous permet de créer des connexions privées entre les centres de données Azure et une infrastructure locale ou dans un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l’Internet public et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet.

Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre l’infrastructure locale et Azure peut également générer des économies substantielles. Grâce à ExpressRoute, vous pouvez établir des connexions à Azure dans un emplacement ExpressRoute (installation d’un fournisseur Exchange) ou vous connecter directement à Azure à partir de votre réseau étendu existant (tel qu’un VPN MPLS) proposé par un fournisseur de services réseau.

Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).


## Étapes suivantes

- Pour plus d’informations sur la passerelle VPN, voir [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md), la [FAQ](vpn-gateway-vpn-faq.md) de la passerelle VPN, et [Planification et conception de la passerelle VPN](vpn-gateway-plan-design.md).

- Pour plus d’informations sur ExpressRoute, consultez [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md), la [FAQ](../expressroute/expressroute-faqs.md), et les [Workflows ExpressRoute](../expressroute/expressroute-workflows.md).

<!---HONumber=AcomDC_0518_2016-->