---
title: "Vue d’ensemble du protocole BGP avec les passerelles VPN Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble du protocole BGP avec les passerelles VPN Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: f8c3985c-c128-4f34-835c-0e88742bf36e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/12/2017
ms.author: yushwang
translationtype: Human Translation
ms.sourcegitcommit: 5961c73c3f147ab6b3eca4e9d920003df1ca2c82
ms.openlocfilehash: 298eab8ad562cfb32edf8609ac29052dc403f406


---
# <a name="overview-of-bgp-with-azure-vpn-gateways"></a>Vue d’ensemble du protocole BGP avec les passerelles VPN Azure
Cet article fournit une vue d’ensemble de la prise en charge du protocole BGP (Border Gateway Protocol) avec les passerelles VPN Azure.

BGP est le protocole de routage standard couramment utilisé sur Internet pour l’échange d’informations d’accessibilité et de routage entre plusieurs réseaux. Dans le contexte des réseaux virtuels Azure, le protocole BGP permet aux passerelles VPN Azure et à vos périphériques VPN locaux (appelés voisins ou homologues BGP) d’échanger des « itinéraires » qui informent les deux passerelles de la disponibilité et de l’accessibilité de ces préfixes pour transiter par les passerelles ou routeurs impliqués. Le protocole BGP permet également le routage de transit entre plusieurs réseaux en propageant les itinéraires qu’une passerelle BGP obtient d’un homologue BGP vers tous les autres homologues BGP. 

## <a name="why-use-bgp"></a>Pourquoi utiliser le protocole BGP ?
Le protocole BGP est une fonctionnalité facultative que vous pouvez utiliser avec les passerelles VPN Azure basées sur des itinéraires. Vous devez vous assurer que vos périphériques VPN locaux prennent en charge le protocole BGP avant d’activer cette fonctionnalité. Vous pouvez continuer à utiliser des passerelles VPN Azure et vos périphériques VPN locaux sans le protocole BGP. Cela revient à utiliser des itinéraires statiques (sans le protocole BGP) *au lieu* d’utiliser un routage dynamique avec le protocole BGP entre vos réseaux et Azure.

Le protocole BGP offre plusieurs avantages et de nouvelles fonctionnalités :

### <a name="support-automatic-and-flexible-prefix-updates"></a>Prise en charge de mises à jour de préfixe automatiques et flexibles
Avec le protocole BGP, il vous suffit de déclarer un préfixe minimal pour un homologue BGP spécifique sur le tunnel VPN S2S IPsec. Il peut être aussi réduit que le préfixe d’un hôte (/32) de l’adresse IP de l’homologue BGP de votre périphérique VPN local. Vous pouvez décider des préfixes de réseaux locaux à publier sur Azure, qui pourront accéder à votre réseau virtuel Azure.

Vous pouvez également publier un préfixe plus étendu, qui peut inclure certains préfixes d’adresses de votre réseau virtuel, par exemple un large espace d’adressage IP privé (par exemple, 10.0.0.0/8). Notez cependant que les préfixes ne peuvent en aucun cas être identiques à ceux de votre réseau virtuel. Les itinéraires identiques aux préfixes de votre réseau virtuel seront rejetés.

### <a name="support-multiple-tunnels-between-a-vnet-and-an-on-premises-site-with-automatic-failover-based-on-bgp"></a>Prise en charge de plusieurs tunnels entre un réseau virtuel et un site local avec basculement automatique basé sur le protocole BGP
Vous pouvez établir plusieurs connexions entre votre réseau virtuel Azure et vos périphériques VPN locaux au même emplacement. Cette fonctionnalité fournit plusieurs tunnels (chemins) entre les deux réseaux dans une configuration actif-actif. Si l’un des tunnels est déconnecté, les itinéraires correspondants seront retirés par le biais du protocole BGP et le trafic sera automatiquement déplacé vers les tunnels restants.

Le schéma suivant offre un exemple simple de cette configuration à haute disponibilité :

![Plusieurs chemins actifs](./media/vpn-gateway-bgp-overview/multiple-active-tunnels.png)

### <a name="support-transit-routing-between-your-on-premises-networks-and-multiple-azure-vnets"></a>Prise en charge du routage de transit entre vos réseaux locaux et plusieurs réseaux virtuels Azure
Le protocole BGP permet à plusieurs passerelles d’obtenir des préfixes de différents réseaux et de les propager, que la connexion soit directe ou indirecte. Ceci peut permettre un routage de transit avec des passerelles VPN Azure entre vos sites locaux ou sur plusieurs réseaux virtuels Azure.

Le schéma suivant offre un exemple de topologie à tronçons multiples, avec plusieurs chemins permettant de faire transiter le trafic entre les deux réseaux locaux via les passerelles VPN Azure dans les réseaux Microsoft :

![Transit sur plusieurs tronçons](./media/vpn-gateway-bgp-overview/full-mesh-transit.png)

## <a name="bgp-faq"></a>Forum Aux Questions sur le protocole BGP
[!INCLUDE [vpn-gateway-bgp-faq-include](../../includes/vpn-gateway-bpg-faq-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Consultez la page [Getting started with BGP on Azure VPN gateways](vpn-gateway-bgp-resource-manager-ps.md) (Prise en main du protocole BGP sur les passerelles VPN Azure) pour savoir comment configurer le protocole BGP pour vos connexions entre sites locaux et entre réseaux virtuels.




<!--HONumber=Jan17_HO2-->


