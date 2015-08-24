<properties 
   pageTitle="À propos des connexions sécurisées entre locaux pour les réseaux virtuels| Microsoft Azure"
   description="Découvrez les différents types de connexions sécurisées entre locaux pour les réseaux virtuels, comprenant les connexions site à site, point à site et ExpressRoute."
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
   ms.date="08/07/2015"
   ms.author="cherylmc" />

# À propos des connexions sécurisées entre locaux pour les réseaux virtuels

Si vous souhaitez connecter vos sites locaux à un réseau virtuel en toute sécurité, vous disposez de trois options : [Site à site](#site-to-site-connections), [Point à site](#point-to-site-connections) et [ExpressRoute](#expressroute-connections).

L’option que vous choisirez peut dépendre de la réponse à différentes questions, notamment :


- Quel est le type de débit requis par votre solution ?
- Souhaitez-vous communiquer sur l’Internet public par le biais d’un VPN sécurisé ou sur une connexion privée ?
- Disposez-vous d’une adresse IP publique utilisable ?
- Envisagez-vous d’utiliser un périphérique VPN ? Si tel est le cas, est-il compatible ?
- Ne voulez-vous connecter qu’un petit nombre d’ordinateurs, ou souhaitez-vous mettre en place une connexion permanente pour votre site ?
- Quel est le type de passerelle VPN requis par la solution que vous souhaitez créer ?

Le tableau ci-après peut vous aider à déterminer la meilleure option de connectivité pour votre solution.

| - | **Point à site** | **Site à site** | **ExpressRoute - EXP** | **ExpressRoute - NSP** | |------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------| | **Services Azure pris en charge** | Cloud Services Virtual Machines | Cloud Services Virtual Machines | [Liste des services](../expressroute/expressroute-faqs.md#supported-azure-services) | [Liste des services](../expressroute/expressroute-faqs.md#supported-azure-services) | | **Bandes passantes classiques** | Débit agrégé généralement < 100 Mbits/s | Débit agrégé généralement < 100 Mbits/s | 200 Mbits/s, 500 Mbits/s, 1 Gbits/s et 10 Gbits/s | 10 Mbits/s, 50 Mbits/s, 100 Mbits/s, 500 Mbits/s, 1 Gbits/s | | **Protocoles pris en charge** | SSTP (Secure Sockets Tunneling Protocol) | [IPsec](http://go.microsoft.com/fwlink/p/?LinkId=618592) | Connexion directe sur réseaux locaux virtuels | Technologies de réseau privé virtuel de NSP (MPLS, VPLS, …) | | **Routage** | Statique | Statique – Nous prenons en charge les réseaux basés sur des stratégies (routage statique) et basés sur l’itinéraire (VPN à routage dynamique) | BGP | BGP | | **Résilience de connexion** | active-passive | active-passive | active-active | active-active | | **Cas d’utilisation classique** | Scénarios de prototypage, de développement, de test et de laboratoire pour les services cloud et les machines virtuelles | Scénarios de développement, de test et de laboratoire et charges de travail de production à petite échelle pour les services cloud et les machines virtuelles | Accès à l’ensemble des services Azure (liste validée), charges de travail d’entreprise et stratégiques, Backup, Big Data, Azure en tant que site de récupération d’urgence | Accès à l’ensemble des services Azure (liste validée), charges de travail d’entreprise et stratégiques, Backup, Big Data, Azure en tant que site de récupération d’urgence | | **Contrat de niveau de service** | [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/) | [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/) | [Contrat de niveau de service](https://azure.microsoft.com/support/legal/sla/) | [Contrat de niveau de service](https://azure.Microsoft.com/support/legal/sla/) | | **Tarification** | [Tarification](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Tarification](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Tarification](http://azure.microsoft.com/pricing/details/expressroute/) | [Tarification](http://azure.microsoft.com/pricing/details/expressroute/) | | **Documentation technique** | [Documentation de passerelle VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentation de passerelle VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentation d’ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | [Documentation d’ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | | **FAQ** | [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md) | [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md) | [FAQ sur ExpressRoute](../expressroute/expressroute-faqs.md) | [FAQ sur ExpressRoute](../expressroute/expressroute-faqs.md) |
                                                                                 



## Connexions site à site

Un réseau privé virtuel (VPN) site à site vous permet de créer une connexion sécurisée entre votre site local et votre réseau virtuel. Pour la création d’une connexion site à site, un périphérique VPN situé sur votre réseau local est configuré de façon à créer une connexion sécurisée avec la passerelle VPN Azure. Une fois la connexion créée, les ressources de votre réseau local et celles qui sont situées dans votre réseau virtuel peuvent communiquer directement et de façon sécurisée. Dans le cas des connexions site à site, vous n’avez pas besoin d’établir une connexion distincte pour chacun des ordinateurs clients de votre réseau local pour leur permettre d’accéder aux ressources du réseau virtuel.

**Utilisez une connexion VPN site à site dans les cas suivants :**

- Vous souhaitez créer une solution hybride.
- Vous voulez disposer d’une connexion entre votre emplacement local et votre réseau virtuel qui ne nécessite aucune configuration côté client.
- Vous souhaitez disposer d’une connexion permanente. 

**Configuration requise**

- Le périphérique VPN local doit disposer d’une adresse IP IPv4 d’accès à Internet. Il ne peut pas se trouver derrière un processus de traduction d’adresses réseau (NAT).
- Vous devez disposer d’un périphérique VPN compatible. Voir l’article [À propos des périphériques VPN](http://go.microsoft.com/fwlink/p/?LinkID=615099). 
- Le périphérique VPN que vous utilisez doit être compatible avec le type de passerelle requis pour votre solution. Consultez la rubrique [À propos des passerelles VPN](vpn-gateway-about-vpngateways.md).
- La référence SKU de passerelle aura également une incidence sur le débit agrégé. Pour plus d’informations, voir la section [SKU de passerelle](vpn-gateway-about-vpngateways.md#gateway-skus). 

Pour plus d’informations sur la configuration d’une connexion de passerelle VPN site à site, voir l’article [Créer un réseau virtuel avec une connexion VPN site à site](vpn-gateway-site-to-site-create.md).

Si vous voulez créer une connexion de passerelle VPN site à site en utilisant le service de routage et d’accès à distance (RRAS), voir l’article [Configurer un VPN de site à site à l’aide du service de routage et d’accès à distance (RRAS) de Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx).


## Connectivité de point à site

Un VPN point à site vous permet également de créer une connexion sécurisée à votre réseau virtuel. Dans une configuration point à site, la connexion est configurée individuellement sur chaque ordinateur client que vous souhaitez connecter au réseau virtuel. Les connexions point à site ne nécessitent aucun périphérique VPN. Ce type de connexion utilise un client VPN que vous installez sur chaque ordinateur client. Vous établissez le VPN en démarrant manuellement la connexion à partir de l’ordinateur client local.

Les configurations point à site et site à site peuvent coexister, mais contrairement aux connexions site à site, les connexions point à site ne sont pas configurables en même temps qu’une connexion ExpressRoute au même réseau virtuel.

**Utilisez une connexion point à site dans les cas suivants :**

- Vous ne souhaitez configurer qu’un petit nombre de clients pour la connexion à un réseau virtuel.

- Vous souhaitez établir une connexion à votre réseau virtuel à partir d’un emplacement distant, tel qu’un café ou une salle de conférence.

- Vous disposez d’une connexion site à site, mais certains de vos clients doivent se connecter à partir d’un emplacement distant.

- Vous n’avez pas accès à un périphérique VPN qui présente la configuration minimale requise pour une connexion site à site.

- Vous ne disposez pas d’une adresse IP IPv4 d’accès à Internet pour votre périphérique VPN.

Pour plus d’informations sur la configuration d’une connexion point à site, voir l’article [Configuration d’une connexion VPN de point à site à un réseau virtuel](vpn-gateway-point-to-site-create.md).

## Connexions ExpressRoute

Azure ExpressRoute vous permet de créer des connexions privées entre les centres de données Azure et une infrastructure locale ou dans un environnement de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l’Internet public et offrent de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet.

Dans certains cas, l’utilisation de connexions ExpressRoute pour le transfert de données entre l’infrastructure locale et Azure peut également générer des économies substantielles. Grâce à ExpressRoute, vous pouvez établir des connexions à Azure dans un emplacement ExpressRoute (installation d’un fournisseur Exchange) ou vous connecter directement à Azure à partir de votre réseau étendu existant (tel qu’un VPN MPLS) proposé par un fournisseur de services réseau.

Pour plus d’informations sur ExpressRoute, voir l’article [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md).


## Étapes suivantes

Pour plus d’informations, voir les articles [Forum Aux Questions ExpressRoute](../expressroute/expressroute-faqs.md) et [FAQ sur la passerelle VPN](vpn-gateway-vpn-faq.md).

<!---HONumber=August15_HO7-->