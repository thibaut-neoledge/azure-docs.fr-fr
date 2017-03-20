---
title: "Présentation du tronçon suivant dans Azure Network Watcher | Microsoft Docs"
description: "Cette page fournit une vue d’ensemble de la fonctionnalité de tronçon suivant de Network Watcher"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 864185e62fb6c3cef4116824b36ee7e5d3447662
ms.lasthandoff: 03/04/2017

---

# <a name="introduction-to-next-hop-in-azure-network-watcher"></a>Présentation du tronçon suivant dans Azure Network Watcher

Le trafic d’une machine virtuelle est envoyé vers une destination en fonction des itinéraires effectifs associés à une carte réseau. Le tronçon suivant obtient le type de tronçon suivant et l’adresse IP d’un paquet à partir d’une machine virtuelle et d’une carte réseau spécifiques. Cela permet de déterminer si le paquet est dirigé vers la destination ou si le trafic est dirigé vers un trou noir. Une mauvaise configuration des itinéraires par l’utilisateur, où le trafic est dirigé vers un emplacement local ou une appliance virtuelle, peut provoquer des problèmes de connectivité. Le tronçon suivant renvoie également la table d’itinéraires associée au tronçon suivant. Lorsque vous interrogez un tronçon suivant pour savoir si l’itinéraire est défini comme un itinéraire défini par l’utilisateur, celui-ci est renvoyé. Sinon, le tronçon suivant renvoie « Itinéraire du système ».

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

![vue d’ensemble du tronçon suivant][1]

Voici une liste des types de tronçons suivants qui peuvent être renvoyés lors de l’interrogation du tronçon suivant.

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VnetLocal
* HyperNetGateway
* VnetPeering
* Aucun

### <a name="next-steps"></a>Étapes suivantes

Découvrez comment utiliser le tronçon suivant pour repérer les problèmes de connectivité réseau en consultant [Find out what the next hop type is using the Next Hop capability in Azure Network Watcher using the portal](network-watcher-check-next-hop-portal.md) (Déterminer quel est le type de tronçon suivant utilisé par la fonctionnalité de tronçon suivant dans Azure Network Watcher à l’aide du portail).

<!--Image references-->
[1]: ./media/network-watcher-next-hop-overview/figure1.png














