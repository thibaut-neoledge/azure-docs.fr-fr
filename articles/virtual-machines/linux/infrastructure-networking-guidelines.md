---
title: "Instructions pour les infrastructures réseau Azure - Linux | Microsoft Docs"
description: "Découvrez-en plus sur les principales instructions de conception et d’implémentation pour le déploiement d’un réseau virtuel dans des services d’infrastructure Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7ebd5da-3c62-45e8-ad90-6c73a37ebeb1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/17/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 5fb8de8e5cbff3e2fb24a347b99f883dfa227296
ms.lasthandoff: 04/03/2017


---
# <a name="azure-networking-infrastructure-guidelines-for-linux-vms"></a>Instructions pour les infrastructures réseau Azure pour machines virtuelles Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Cet article se concentre sur la compréhension des étapes de planification nécessaires pour un réseau virtuel dans Azure et la connectivité entre les environnements locaux existants.

## <a name="implementation-guidelines-for-virtual-networks"></a>Instructions d’implémentation pour les réseaux virtuels
Décisions :

* Quel type de réseau virtuel devez-vous pour héberger votre charge de travail ou votre infrastructure informatique (cloud ou intersite) ?
* Pour les réseaux virtuels intersite, de quelle taille d’espace adressage avez-vous besoin pour héberger les sous-réseaux et les machines virtuelles maintenant et pour une extension future raisonnable ?
* Allez-vous créer des réseaux virtuels centralisés ou plutôt des réseaux virtuels pour chaque groupe de ressources ?

Tâches :

* Définissez l’espace d’adressage du réseau virtuel à créer.
* Définir l’ensemble de sous-réseaux et l’espace d’adressage pour chacun.
* Pour les réseaux virtuels intersite, définir l’ensemble des espaces d’adressage de réseau local pour les emplacements locaux auxquels les machines virtuelles doivent accéder dans le réseau virtuel.
* Travaillez avec l’équipe réseau locale pour assurer que le routage approprié est configuré lors de la création de réseaux virtuels intersite.
* Créer le réseau virtuel à l’aide de votre convention d’affectation de noms.

## <a name="virtual-networks"></a>Réseaux virtuels
Des réseaux virtuels sont nécessaires pour prendre en charge les communications entre les machines virtuelles. Vous pouvez définir des sous-réseaux, des adresses IP personnalisées, des paramètres DNS, des filtrages de sécurité et l’équilibrage de charge, tout comme avec les réseaux physiques. Avec une [passerelle VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) ou un [Circuit Express Route](../../expressroute/expressroute-introduction.md), vous pouvez connecter des réseaux virtuels Azure à vos réseaux locaux. Vous pouvez en savoir plus sur [les réseaux virtuels et leurs composants](../../virtual-network/virtual-networks-overview.md).

Avec les groupes de ressources, vous disposez de plus de flexibilité dans la façon dont vous concevez vos composants de réseau virtuel. Les machines virtuelles peuvent se connecter à des réseaux virtuels en dehors de leur propre groupe de ressources. Une approche de conception courante consisterait à créer des groupes de ressources centralisés contenant votre infrastructure réseau de base, qui peut être gérée par une équipe commune. Les machines virtuelles et leurs applications sont déployées sur des groupes de ressources distincts. Cette approche permet aux propriétaires d’applications d’accéder au groupe de ressources qui contient leurs machines virtuelles sans ouvrir l’accès à la configuration des ressources réseau virtuelles à plus grande échelle.

## <a name="site-connectivity"></a>Connectivité de site
### <a name="cloud-only-virtual-networks"></a>Réseaux virtuels cloud uniquement
Si les utilisateurs et les ordinateurs locaux ne nécessitent pas de connectivité continue aux machines virtuelles dans un réseau virtuel Azure, votre conception de réseau virtuel sera simple :

![Diagramme d’un réseau virtuel de base uniquement dans le cloud](./media/infrastructure-networking-guidelines/vnet01.png)

Cette approche s’applique généralement à des charges de travail Internet, telles qu’un serveur web Internet. Vous pouvez gérer ces machines virtuelles par SSH ou connexion VPN point à site.

Étant donné qu’ils ne se connectent pas à votre réseau local, les réseaux virtuels Azure uniquement peuvent utiliser une partie de l’espace d’adressage IP privé. L’espace d’adressage peut être l’espace privé utilisé en local.

### <a name="cross-premises-virtual-networks"></a>Réseaux virtuels intersite
Si les utilisateurs et les ordinateurs locaux nécessitent une connectivité continue aux machines virtuelles dans un réseau virtuel Azure, créez un réseau virtuel entre différents locaux. Connectez le réseau virtuel à votre réseau local avec une connexion ExpressRoute ou VPN de site à site.

![Diagramme de réseau virtuel entre sites locaux](./media/infrastructure-networking-guidelines/vnet02.png)

Dans cette configuration, le réseau virtuel Azure est essentiellement une extension cloud de votre réseau local.

Étant donné qu’ils se connectent à votre réseau local, les réseaux virtuels intersite doivent utiliser une partie de l’espace d’adressage utilisé par votre organisation, qui est unique. De la même façon que différents emplacements de l’entreprise se voient affecter un sous-réseau IP spécifique, Azure devient un autre emplacement lorsque vous étendez votre réseau.

Pour autoriser les paquets à circuler de votre réseau virtuel intersite vers votre réseau local, vous devez configurer le jeu de préfixes d’adresses locales pertinents dans le cadre de la définition du réseau local pour le réseau virtuel. En fonction de l’espace d’adressage du réseau virtuel et de l’ensemble des emplacements locaux concernés, le réseau local peut comprendre de nombreux préfixes d’adresse.

Vous pouvez convertir un réseau virtuel cloud en un réseau virtuel intersite, mais il nécessite probablement une renumérotation de l’IP de votre espace d’adressage de réseau virtuel et des ressources Azure. Par conséquent, déterminez avec soin si un réseau virtuel doit se connecter à votre réseau local lorsque vous affectez un sous-réseau d’IP.

## <a name="subnets"></a>Sous-réseaux
Les sous-réseaux vous permettent d’organiser des ressources apparentées, soit logiquement (par exemple, un sous-réseau pour les machines virtuelles associées à la même application), soit physiquement (par exemple, un sous-réseau par groupe de ressources), ou d’employer des techniques d’isolation de sous-réseaux pour renforcer la sécurité. Vous pouvez également employer des techniques d’isolation de sous-réseaux pour renforcer la sécurité.

Pour les réseaux virtuels entre différents locaux, vous devez concevoir des sous-réseaux possédant les mêmes conventions que les ressources locales. **Azure utilise toujours les trois premières adresses IP de l’espace d’adressage pour chaque sous-réseau**. Pour déterminer le nombre d’adresses nécessaires pour le sous-réseau, commencez par compter le nombre de machines virtuelles dont vous avez besoin actuellement. Estimez la croissance future, puis utilisez le tableau suivant pour déterminer la taille du sous-réseau.

| Nombre de machines virtuelles nécessaires | Nombre de bits hôte nécessaires | Taille du sous-réseau |
| --- | --- | --- |
| 1 à 3 |3 |/29 |
| 4 à 11 |4 |/28 |
| 12 à 27 |5 |/27 |
| 28 à 59 |6 |/26 |
| 60 à 123 |7 |/25 |

> [!NOTE]
> Pour des sous-réseaux locaux normaux, le nombre maximal d’adresses d’hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 2. Pour un sous-réseau Azure, le nombre maximal d’adresses d’hôte pour un sous-réseau avec n bits hôte est 2<sup>n</sup> – 5 (2 plus 3 pour les adresses qu’Azure utilise sur chaque sous-réseau).
> 
> 

Si vous choisissez une taille de sous-réseau trop petite, vous devez renuméroter les IP et redéployer les machines virtuelles dans le sous-réseau.

## <a name="network-security-groups"></a>Groupes de sécurité réseau
Vous pouvez appliquer des règles de filtrage pour le trafic qui transite via vos réseaux virtuels à l’aide de groupes de sécurité réseau. Vous pouvez créer des règles de filtrage granulaires pour sécuriser votre environnement de réseau virtuel, le trafic entrant et sortant du trafic, les plages d’IP de source et de destination, les ports autorisés, etc. Les groupes de sécurité réseau peuvent être appliqués à des sous-réseaux au sein d’un réseau virtuel, ou directement à une interface réseau virtuelle spécifiée. Il est recommandé d’avoir un certain niveau de filtrage du trafic sur vos groupes de sécurité réseau pour vos réseaux virtuels. Vous pouvez en savoir plus sur [Groupes de sécurité réseau](../../virtual-network/virtual-networks-nsg.md).

## <a name="additional-network-components"></a>Composants réseau supplémentaires
Comme avec une infrastructure de réseau local physique, un réseau virtuel Windows Azure peut contenir plus que des sous-réseaux et des adresses IP. Lorsque vous concevez votre infrastructure d’application, vous souhaiterez incorporer certains de ces composants supplémentaires :

* [Des passerelles VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) - connectez des réseaux virtuels Azure à d’autres réseaux virtuels Azure, ou connectez des réseaux locaux via une connexion VPN de site à site. Implémentez des connexions Express Route dédiées et sécurisées. Vous pouvez également fournir aux utilisateurs un accès direct grâce à des connexions VPN de point à site.
* [Un équilibreur de charge](../../load-balancer/load-balancer-overview.md) - fournit l’équilibrage de la charge du trafic pour le trafic externe et interne de la façon dont vous le souhaitez.
* [Une Application Gateway](../../application-gateway/application-gateway-introduction.md) - équilibrage de charge HTTP de la couche Application, en fournissant des avantages supplémentaires pour les applications web par rapport au déploiement de l’équilibreur de charge Azure.
* [Traffic Manager](../../traffic-manager/traffic-manager-overview.md) - distribution du trafic basée sur DNS aux utilisateurs finaux directs vers le point de terminaison de l’application disponible le plus proche, ce qui vous permet d’héberger votre application hors des centres de données Azure dans différentes régions.

## <a name="next-steps"></a>Étapes suivantes
[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]


