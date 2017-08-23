---
title: "Réseau virtuel Azure | Microsoft Docs"
description: "Découvrez les concepts et les fonctionnalités du réseau virtuel Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.contentlocale: fr-fr
ms.lasthandoff: 07/26/2017

---
# <a name="azure-virtual-network"></a>Réseau virtuel Azure

Le service Réseau virtuel Azure vous permet de connecter en toute sécurité des ressources Azure entre elles en utilisant des réseaux virtuels. Un réseau virtuel est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez également connecter des réseaux virtuels à votre réseau local. L’illustration suivante montre certaines des fonctionnalités du service Réseau virtuel Azure :

![Diagramme du réseau](./media/virtual-networks-overview/virtual-network-overview.png)

Pour en savoir plus sur les fonctionnalités suivantes du réseau virtuel Azure, cliquez sur la fonctionnalité :
- **[Isolement :](#isolation)**  les réseaux virtuels sont isolés les uns des autres. Vous pouvez créer des réseaux virtuels distincts pour le développement, le test et la production, qui utilisent les mêmes blocs d’adresses CIDR. À l’inverse, vous pouvez créer plusieurs réseaux virtuels qui utilisent différents blocs d’adresses CIDR et connecter les réseaux entre eux. Vous pouvez segmenter un réseau virtuel en plusieurs sous-réseaux. Azure fournit une résolution de noms interne pour les machines virtuelles et les instances de rôle Services cloud connectées à un réseau virtuel. Vous pouvez également configurer un réseau virtuel afin d’utiliser vos propres serveurs DNS au lieu d’utiliser la résolution de noms interne Azure.
- **[Connectivité Internet :](#internet)** tous les machines virtuelles Azure et instances de rôle Services cloud connectées à un réseau virtuel ont accès à Internet, par défaut. Vous pouvez également activer l’accès entrant à des ressources spécifiques, en fonction de vos besoins.
- **[Connectivité des ressources Azure :](#within-vnet)** les ressources Azure telles que les Services cloud et les machines virtuelles peuvent être connectées au même réseau virtuel. Les ressources peuvent se connecter entre elles à l’aide d’adresses IP privées, même si elles se trouvent dans des sous-réseaux différents. Azure fournit un routage par défaut entre les sous-réseaux, les réseaux virtuels et les réseaux locaux, sans que vous ayez à configurer et gérer ces itinéraires.
- **[Connectivité de réseau virtuel :](#connect-vnets)** les réseaux virtuels peuvent être connectés entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec n’importe quelle ressource sur n’importe quel autre réseau virtuel.
- **[Connectivité locale :](#connect-on-premises)** les réseaux virtuels peuvent être connectés à des réseaux locaux via des connexions réseau privées entre votre réseau et Azure, ou via une connexion VPN de site à site sur Internet.
- **[Filtrage du trafic :](#filtering)** le trafic réseau entrant et sortant des machines virtuelles et des instances de rôle Services cloud peut être filtré par adresse IP source et de port, adresse IP de destination et port, et protocole.
- **[Routage :](#routing)** vous pouvez également remplacer le routage Azure par défaut en configurant votre propre routage ou en utilisant des itinéraires BGP via une passerelle réseau.

## <a name = "isolation"></a>Isolement et segmentation du réseau

Vous pouvez implémenter plusieurs réseaux virtuels au sein de chaque [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure et de chaque [région](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure. Chaque réseau virtuel est isolé des autres réseaux virtuels. Pour chaque réseau virtuel, vous pouvez :
- Spécifier un espace d’adressage IP privé personnalisé à l’aide d’adresses (RFC 1918) publiques et privées. Azure attribue aux ressources connectées au réseau virtuel une adresse IP privée à partir de l’espace d’adressage que vous attribuez.
- Segmenter le réseau virtuel en un ou plusieurs sous-réseaux et allouer une partie de l’espace d’adressage du réseau virtuel à chaque sous-réseau.
- Utiliser la résolution de noms fournie par Azure ou spécifier votre propre serveur DNS pour une utilisation par les ressources connectées à un réseau virtuel. Pour en savoir plus sur la résolution de noms dans des réseaux virtuels, lisez l’article [Résolution de noms pour les machines virtuelles et services cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Se connecter à Internet
Toutes les ressources connectées à un réseau virtuel disposent par défaut d’une connectivité sortante vers Internet. L’adresse IP privée de la ressource est traduite via SNAT (source network address translated) en une adresse IP publique par l’infrastructure Azure. Pour en savoir plus sur la connectivité Internet sortante, lisez l’article [Présentation des connexions sortantes dans Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Vous pouvez modifier la connectivité par défaut en implémentant un routage et un filtrage de trafic personnalisés.

Pour pouvoir communiquer avec les ressources Azure à partir ou depuis Internet sans SNAT, une ressource doit être affectée à une adresse IP publique. Pour en savoir plus sur les adresses IP publiques, consultez l’article [Adresses IP publiques](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Connecter des ressources Azure
Vous pouvez connecter plusieurs ressources Azure à un réseau virtuel, par exemple des machines virtuelles, des services cloud, des environnements App Service et des groupes de machines virtuelles identiques. Les machines virtuelles se connectent à un sous-réseau d’un réseau virtuel via une interface réseau (NIC). Pour en savoir plus sur les interfaces réseau, lisez l’article [Interfaces réseau](virtual-network-network-interface.md).

## <a name="connect-vnets"></a>Connecter des réseaux virtuels

Vous pouvez connecter des réseaux virtuels entre eux, permettant aux ressources connectées à un réseau virtuel de communiquer avec eux via des réseaux virtuels. Vous pouvez utiliser une des deux options suivantes pour connecter des réseaux virtuels entre eux :
- **Homologation :** permet à des ressources connectées à différents réseaux virtuels Azure d’un même emplacement Azure de communiquer entre elles. La bande passante et la latence entre les réseaux virtuels est la même que si les ressources étaient connectées au même réseau virtuel. Pour en savoir plus sur l’homologation, lisez l’article [Homologation de réseau virtuel](virtual-network-peering-overview.md).
- **Connexion de réseau virtuel à réseau virtuel :** permet à des ressources connectées à un autre réseau virtuel Azure d’un même emplacement Azure ou de différents emplacements. Contrairement à l’homologation, la bande passante est limitée entre les réseaux virtuels car le trafic doit passer par une passerelle VPN Azure. Pour en savoir plus sur la connexion de réseaux virtuels avec une connexion de réseau virtuel à réseau virtuel, lisez l’article [Configurer une connexion de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="connect-on-premises"></a>Se connecter à un réseau local

Vous pouvez connecter votre réseau local à un réseau virtuel à l’aide de n’importe quelle combinaison des options suivantes :
- **Réseau privé virtuel (VPN) de point à site :** connexion établie entre un PC unique connecté à votre réseau et le réseau virtuel. Ce type de connexion est utile si vous n’êtes pas familiarisé avec Azure, ou pour les développeurs car elle nécessite peu voire pas de modifications de votre réseau existant. La connexion utilise le protocole SSTP pour fournir une communication chiffrée via Internet entre l’ordinateur et le réseau virtuel. La latence d’un réseau VPN de point à site est imprévisible, car le trafic transite par Internet.
- **VPN de site à site :** connexion établie entre votre appareil VPN et une passerelle VPN Azure. Ce type de connexion permet à n’importe quelle ressource locale de votre choix à accéder à un réseau virtuel. La connexion s’effectue via un réseau VPN IPSec/IKE qui fournit une communication chiffrée via Internet entre votre appareil local et la passerelle VPN Azure. La latence d’une connexion de site à site est imprévisible car le trafic transite par Internet.
- **Azure ExpressRoute :** connexion établie entre votre réseau et Azure via un partenaire ExpressRoute. Cette connexion est privée. Le trafic ne transite pas par Internet. La latence d’une connexion ExpressRoute est prévisible, car le trafic ne transite pas par Internet.

Pour en savoir plus sur toutes les options de connexion précédentes, lisez l’article [Diagrammes des topologies de connexion](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrer le trafic réseau
Vous pouvez filtrer le trafic réseau entre les sous-réseaux à l’aide d’une des deux options suivantes :
- **Groupes de sécurité réseau (NSG) :** chaque groupe de sécurité réseau peut contenir plusieurs règles de sécurité entrantes et sortantes qui vous permettent de filtrer le trafic par source et adresse IP de destination, port et protocole. Vous pouvez appliquer un groupe de sécurité réseau à chaque carte réseau d’une machine virtuelle. Vous pouvez également appliquer un groupe de sécurité réseau au sous-réseau auquel une carte réseau ou toute autre ressource Azure est connectée. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md).
- **Appliances de réseau virtuel (NVA) :** une appliance de réseau virtuel est une machine virtuelle exécutant un logiciel qui exécute une fonction de réseau, par exemple un pare-feu. Affichez la liste des appliances de réseau virtuel disponibles dans [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Il existe également des appliances de réseau virtuel qui fournissent une optimisation du réseau étendu et d’autres fonctions de trafic réseau. Les appliances de réseau virtuel sont généralement utilisées avec des itinéraires définis par l’utilisateur ou des itinéraires BGP. Vous pouvez également utiliser une appliance de réseau virtuel pour filtrer le trafic entre des réseaux virtuels.

## <a name="routing"></a>Router le trafic réseau

Azure crée des tables de routage qui permettent aux ressources connectées à un sous-réseau d’un réseau virtuel de communiquer entre elles, par défaut. Vous pouvez implémenter une ou les deux options suivantes pour remplacer les itinéraires par défaut créés par Azure :
- **Itinéraires définis par l’utilisateur :** vous pouvez créer des tables de routage personnalisées avec des itinéraires qui contrôlent où le trafic est acheminé pour chaque sous-réseau. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).
- **Itinéraires BGP :** si vous connectez votre réseau virtuel à votre réseau local via une connexion ExpressRoute ou la passerelle VPN Azure, vous pouvez propager les itinéraires BGP à vos réseaux virtuels.

## <a name="pricing"></a>Tarification

Aucun frais n’est facturé pour les réseaux virtuels, les sous-réseaux, les tables de routage et les groupes de sécurité réseau. L’utilisation de la bande passante Internet sortante, les IP adresses publiques, l’homologation du réseau virtuel, les passerelles VPN et ExpressRoute possèdent leurs propres aux structures de tarification. Consultez les pages sur la tarification du [réseau virtuel](https://azure.microsoft.com/pricing/details/virtual-network), de la [passerelle VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) et [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) pour plus d’informations.

## <a name="faq"></a>Forum Aux Questions

Pour consulter les questions fréquentes concernant le réseau virtuel, consultez l’article [FAQ sur les réseaux virtuels](virtual-networks-faq.md).


## <a name="next-steps"></a>Étapes suivantes

- Créez votre premier réseau virtuel et connectez-y plusieurs machines virtuelles en effectuant les étapes décrites dans l’article [Créer votre premier réseau virtuel](virtual-network-get-started-vnet-subnet.md).
- Créez une connexion de point à site vers un réseau virtuel en effectuant les étapes décrites dans l’article [Configurer une connexion de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- En savoir plus sur les autres [fonctionnalités réseau](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) clés d’Azure.

