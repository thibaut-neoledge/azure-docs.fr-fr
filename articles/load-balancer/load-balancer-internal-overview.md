---
title: "Présentation de l’équilibrage de charge interne | Microsoft Docs"
description: "Vue d'ensemble de l'équilibrage de charge interne et de ses fonctionnalités. Procédure de fonctionnement de l'équilibrage de charge pour Azure et les scénarios de configuration possibles des points de terminaison internes"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ec07c77119c2da408da21fbdc7877d0b43d16556
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="internal-load-balancer-overview"></a>Présentation de l’équilibrage de charge interne

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Contrairement à l’équilibreur de charge avec accès par Internet, l’équilibreur de charge interne (ILB) dirige le trafic uniquement vers les ressources au sein du service cloud ou via le VPN pour accéder à l’infrastructure Azure. L’infrastructure limite l’accès aux adresses IP virtuelles à charge équilibrée d’un service cloud ou d’un réseau virtuel. Ainsi, elles ne seront jamais exposées directement à un point de terminaison Internet. Cela permet aux applications cœur de métier (LOB) internes de s’exécuter dans Azure et d’être accessibles depuis le cloud ou à partir de ressources locales.

## <a name="why-you-may-need-an-internal-load-balancer"></a>Pourquoi utiliser un équilibreur de charge interne

L’équilibrage de charge interne (ILB) d’Azure fournit un équilibrage de charge entre les machines virtuelles qui résident dans un service cloud ou un réseau virtuel avec une portée régionale. Pour plus d'informations sur l'utilisation et la configuration des réseaux virtuels avec une portée régionale, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) sur le blog Azure. Les réseaux virtuels existants qui ont été configurés pour un groupe d'affinités ne peuvent pas utiliser l'ILB.

L’ILB permet d’effectuer les types d'équilibrage de charge suivants :

* Dans un service cloud, des machines virtuelles à un ensemble de machines virtuelles qui résident dans le même service cloud (voir Figure 1).
* Dans un réseau virtuel, des machines virtuelles dans le réseau virtuel à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel (voir Figure 2).
* Dans un réseau virtuel entre différents locaux, des ordinateurs locaux à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel (voir Figure 3).
* Les applications multiniveau sur internet pour lesquelles les principaux niveaux ne sont pas sur internet mais nécessitent un équilibrage de charge pour le trafic depuis le niveau sur internet.
* Équilibrer la charge pour des applications métier (LOB) hébergées dans Azure, sans matériel ou logiciel d'équilibrage de charge supplémentaire. Y compris les serveurs locaux d'un ensemble d'ordinateurs dont la charge du trafic est équilibrée.

## <a name="internet-facing-multi-tier-applications"></a>Une application multiniveau sur internet

La couche web a des points de terminaison sur Internet pour les clients Internet et fait partie d'un jeu d'équilibrage de charge. L'équilibrage de charge répartit le trafic entrant à partir des clients web pour le port TCP 443 (HTTPS) pour les serveurs web.

Les serveurs de base de données sont situés derrière un point de terminaison d'ILB utilisé par les serveurs web pour le stockage. Cette base de données dispose d’un point de terminaison d’équilibrage de charge dont la charge du trafic est équilibrée entre les serveurs de base de données dans le jeu d'ILB.

L'image suivante présente l’application multiniveau sur Internet dans le même service cloud.

![Service cloud unique d'équilibrage de charge interne](./media/load-balancer-internal-overview/IC736321.png)

Illustration 1 - Application multi-niveau sur Internet

Une autre utilisation possible pour une application multiniveau se présente lorsqu’une ILB est déployée dans un service cloud différent de celui consommant le service pour l'ILB.

Les services cloud qui utilisent le même réseau virtuel auront accès au point de terminaison de l'ILB. L’image suivante présente des serveurs web frontaux qui se trouvent dans un autre service cloud que la base de données principale et utilisent le point de terminaison d’ILB dans le même réseau virtuel.

![Équilibrage de charge interne entre les services cloud](./media/load-balancer-internal-overview/IC744147.png)

Figure 2 : serveurs frontaux dans un service cloud différent

## <a name="intranet-line-of-business-applications"></a>Applications cœur de métier (LOB) Intranet

Le trafic des clients sur le réseau local obtient un équilibrage de charge sur l'ensemble des serveurs cœur de métier à l'aide de la connexion VPN au réseau Azure.

L’ordinateur client aura accès à une adresse IP du service VPN Azure à l’aide d’un VPN point à site. Il permet l’utilisation de l’application de cœur de métier (LOB) hébergée derrière le point de terminaison d’équilibrage de charge interne (ILB).

![Équilibrage de charge interne utilisant le VPN de point à site](./media/load-balancer-internal-overview/IC744148.png)

Figure 3 : applications de cœur de métier (LOB) hébergées derrière le point de terminaison d’équilibrage de charge (ILB)

Un autre scénario pour le système cœur de métier est d'avoir un VPN de site à site sur le réseau virtuel dans lequel le point de terminaison de l’ILB est configuré. Cela permet au trafic du réseau local d’être acheminé vers le point de terminaison d’équilibrage de charge interne (ILB).

![Équilibrage de charge interne utilisant le VPN de site à site](./media/load-balancer-internal-overview/IC744150.png)

Figure 4 : trafic du réseau local acheminé vers le point de terminaison d’équilibrage de charge interne (ILB)

## <a name="limitations"></a>Limitations

Les configurations d’équilibrage de charge internes ne prennent pas en charge SNAT. Dans le cadre de ce document, SNAT fait référence à la traduction d’adresses réseau sources en masquant des ports.  Cela s’applique aux scénarios où une machine virtuelle dans un pool d’équilibrage de charge doit atteindre l’adresse PI frontale de son équilibreur de charge interne respectif. Ce scénario n’est pas pris en charge pour l’équilibreur de charge interne. Des problèmes de connexion surviennent lorsque le flux est à charge équilibrée pour la machine virtuelle à l’origine du flux. Vous devez utiliser un équilibreur de charge de type proxy pour de tels scénarios.

## <a name="next-steps"></a>Étapes suivantes

[Prise en charge d'Azure Resource Manager pour l'équilibrage de charge Azure](load-balancer-arm.md)

[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
