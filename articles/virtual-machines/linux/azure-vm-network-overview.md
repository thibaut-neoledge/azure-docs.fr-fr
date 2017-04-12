---
title: "Vue d’ensemble du réseau de machines virtuelles Azure et Linux | Microsoft Docs"
description: "Vue d’ensemble de la mise en réseau de machines virtuelles Azure et Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: b5420e35-0463-4456-9803-6142db150f2e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1ff4a0482d6dc6ec0eceaa89ca4b87ba1e2f89a1
ms.lasthandoff: 04/03/2017


---
# <a name="azure-and-linux-vm-network-overview"></a>Vue d’ensemble du réseau de machines virtuelles Azure et Linux
## <a name="virtual-networks"></a>Virtual Network
Un réseau virtuel Azure (VNet) est une représentation de votre propre réseau dans le cloud. Il s’agit d’un isolement logique du cloud Azure dédié à votre abonnement. Vous pouvez contrôler complètement les blocs d’adresses IP, les paramètres DNS, les stratégies de sécurité et les tables de routage de ce réseau. Vous pouvez également segmenter votre réseau virtuel en plusieurs sous-réseaux et lancer des machines virtuelles IaaS Azure et/ou des services cloud (instances de rôle PaaS). En outre, vous pouvez connecter le réseau virtuel à votre réseau local à l’aide des options de connectivité disponibles dans Azure. En bref, vous pouvez développer votre réseau sur Azure et maîtriser totalement vos blocs d’adresses IP avec les que procurent la mise à l’échelle d’entreprise d’Azure.

* [Présentation du réseau virtuel.](../../virtual-network/virtual-networks-overview.md)

Pour créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure, rendez-vous ici pour suivre la procédure.

* [Création d’un réseau virtuel à l’aide de l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md)

## <a name="network-security-groups"></a>Groupes de sécurité réseau
Un groupe de sécurité réseau (NSG) contient une liste des règles de liste de contrôle d’accès (ACL) qui autorise ou rejette les instances de machine virtuelle dans un réseau virtuel. Des groupes de sécurité réseau peuvent être associés à des sous-réseaux ou à des instances de machine virtuelle au sein de ce sous-réseau. Lorsqu’un groupe de sécurité réseau est associé à un sous-réseau, les règles ACL s’appliquent à toutes les instances de machine virtuelle présentes dans ce sous-réseau. En outre, le trafic vers un ordinateur virtuel individuel peut être limité par l’association d’un groupe de sécurité réseau directement à la machine virtuelle.

* [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
* [Comment créer des groupes de sécurité réseau dans l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

## <a name="user-defined-routes"></a>Itinéraires définis par l’utilisateur
Lorsque vous ajoutez des machines virtuelles à un réseau virtuel dans Microsoft Azure, notez que les machines sont en mesure d’interagir automatiquement entre elles sur le réseau. Il est inutile de spécifier une passerelle, bien que les machines virtuelles soient hébergées dans des sous-réseaux différents. Cela vaut également pour la communication des machines virtuelles vers l’Internet public, et même vers votre réseau local en cas de connexion hybride de Microsoft Azure vers votre propre centre de données.

* [Présentation des itinéraires définis par l’utilisateur et du transfert IP](../../virtual-network/virtual-networks-udr-overview.md)
* [Créer un itinéraire défini par l’utilisateur dans l’interface CLI Azure](../../virtual-network/virtual-network-create-udr-arm-cli.md)

## <a name="associating-a-fqdn-to-your-linux-vm"></a>Associer un nom de domaine complet à votre machine virtuelle Linux
Lorsque vous créez une machine virtuelle dans le Portail Azure à l’aide du modèle de déploiement Resource Manager, une ressource d’adresse IP publique est créée automatiquement pour la machine virtuelle. Vous utilisez cette adresse IP pour accéder à distance à la machine virtuelle. Bien que le portail ne crée pas de nom de domaine complet (FQDN) par défaut, vous pouvez en ajouter un une fois la machine virtuelle créée.

* [Créer un nom de domaine complet dans le Portail Azure](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="network-interfaces"></a>Interfaces réseau
Une carte d’interface réseau (NIC) est l’interconnexion entre une machine virtuelle et le réseau logiciel sous-jacent. Cet article explique ce qu’est une interface réseau et comment elle est utilisée dans le modèle de déploiement Azure Resource Manager.

* [Interfaces de réseau virtuel](../../virtual-network/virtual-network-network-interface.md)

## <a name="virtual-nics-and-dns-labeling"></a>Cartes réseau virtuelles et étiquetage DNS
Si vous avez besoin que votre serveur soit persistant, mais qu’il est traité comme du bétail et fréquemment détruit et déployé, il est conseillé d’utiliser l’étiquetage DNS sur votre carte réseau pour conserver le nom sur le réseau virtuel.  La procédure pas à pas suivante vous permettra de configurer une carte réseau nommée de manière permanente avec une adresse IP statique.

* [Création d’un environnement Linux complet à l’aide de l’interface de ligne de commande Azure](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network-gateways"></a>Passerelles de réseau virtuel
Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux, mais aussi entre plusieurs réseaux virtuels au sein d’Azure (connexion entre deux réseaux virtuels). Lorsque vous configurez une passerelle VPN, vous devez créer et configurer une passerelle de réseau virtuel et une connexion à la passerelle de réseau virtuel.

* [À propos de la passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

## <a name="internal-load-balancing"></a>Équilibrage de charge interne
Un équilibrage de charge Azure est de type Couche 4 (TCP, UDP). L’équilibrage de charge offre une disponibilité élevée en distribuant le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles dans un jeu d’équilibrage de la charge. Azure Load Balancer peut également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

* [Création d’un équilibreur de charge interne à l’aide de l’interface de ligne de commande Azure](../../load-balancer/load-balancer-get-started-internet-arm-cli.md)


