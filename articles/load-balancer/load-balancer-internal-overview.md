
<properties
   pageTitle="Vue d’ensemble de l’équilibrage de charge Azure | Microsoft Azure"
   description="Vue d'ensemble de l'équilibrage de charge interne et de ses fonctionnalités. Procédure de fonctionnement de l'équilibrage de charge pour Azure et les scénarios de configuration possibles des points de terminaison internes"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/25/2016"
   ms.author="sewhee" />


# Vue d’ensemble de l’équilibrage de charge interne

Contrairement à l’équilibreur de charge avec accès par Internet, l’équilibreur de charge interne (ILB) utilise uniquement les ressources au sein du service cloud ou le VPN pour accéder à l’infrastructure Azure. L’infrastructure limite l’accès aux adresses IP virtuelles à charge équilibrée d’un service cloud ou d’un réseau virtuel. Ainsi, elles ne seront jamais exposées directement à un point de terminaison Internet. Cela permet aux applications cœur de métier internes de s’exécuter dans Azure et d’être accessibles dans le cloud ou à partir de ressources locales.

## Scénarios pour l’équilibreur de charge interne

L’équilibrage de charge interne (ILB) d’Azure fournit un équilibrage de charge entre les machines virtuelles qui résident dans un service cloud ou un réseau virtuel avec une portée régionale. Pour plus d'informations sur l'utilisation et la configuration des réseaux virtuels avec une portée régionale, consultez [Réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) sur le blog Azure. Les réseaux virtuels existants qui ont été configurés pour un groupe d'affinités ne peuvent pas utiliser l'ILB.

L’ILB donne accès aux scénarios suivants :

- Dans un service cloud, des machines virtuelles à un ensemble de machines virtuelles qui résident dans le même service cloud (voir Figure 1).
- Dans un réseau virtuel, des machines virtuelles dans le réseau virtuel à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel (voir Figure 2).
- Dans un réseau virtuel entre différents locaux, des ordinateurs locaux à un ensemble de machines virtuelles qui résident dans le même service cloud du réseau virtuel (voir Figure 3).
- Les applications multiniveau sur internet pour lesquelles les principaux niveaux ne sont pas sur internet mais nécessitent un équilibrage de charge pour le trafic depuis le niveau sur internet.
- Équilibrer la charge pour des applications métier hébergées dans Azure, sans matériel ou logiciel d’équilibrage de charge supplémentaire. Y compris les serveurs locaux d'un ensemble d'ordinateurs dont la charge du trafic est équilibrée.

## Une application multiniveau sur internet


La couche web a des points de terminaison sur Internet pour les clients Internet et fait partie d'un jeu d'équilibrage de charge. L'équilibrage de charge répartit le trafic entrant à partir des clients web pour le port TCP 443 (HTTPS) pour les serveurs web.

Les serveurs de base de données sont situés derrière un point de terminaison d'ILB utilisé par les serveurs web pour le stockage. Cette base de données dispose d’un point de terminaison d’équilibrage de charge dont la charge du trafic est équilibrée entre les serveurs de base de données dans le jeu d'ILB.

L'image ci-dessous décrit l’application multiniveau sur Internet dans le même service cloud.

La figure 1

![Service cloud unique d'équilibrage de charge interne](./media/load-balancer-internal-overview/IC736321.png)

Un autre scénario possible pour une application multiniveau se présente lorsqu’une ILB est déployée dans un service cloud différent de celui consommant le service pour l'ILB.

Les services cloud qui utilisent le même réseau virtuel auront accès au point de terminaison de l'ILB.

Vous pouvez voir dans l'image ci-dessous que des serveurs web frontaux se trouvent dans un autre service cloud que la base de données principale et exploitent le point de terminaison d’ILB dans le même réseau virtuel.

Figure 2

![Équilibrage de charge interne entre les services cloud](./media/load-balancer-internal-overview/IC744147.png)

## Applications cœur de métier Intranet

Le trafic des clients sur le réseau local obtient un équilibrage de charge sur l'ensemble des serveurs cœur de métier à l'aide de la connexion VPN au réseau Azure.

L'ordinateur client aura accès à une adresse IP du service VPN Azure à l'aide du VPN de point à site. Cela permettra d'utiliser l'application cœur de métier hébergée derrière le point de terminaison de l'ILB.

Figure 3

![Équilibrage de charge interne utilisant le VPN de point à site](./media/load-balancer-internal-overview/IC744148.png)

Un autre scénario pour le système cœur de métier est d'avoir un VPN de site à site sur le réseau virtuel dans lequel le point de terminaison de l’ILB est configuré. Cela permet au trafic du réseau local d’être acheminé vers le point de terminaison de l'ILB.

Figure 4

![Équilibrage de charge interne utilisant le VPN de site à site](./media/load-balancer-internal-overview/IC744150.png)


## Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-ps.md)

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0831_2016-->