---
title: "Présentation de l’équilibrage de charge Azure | Microsoft Docs"
description: "Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne l’équilibrage de charge et l’exploiter dans le cloud."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: ecf1fc38d2b9fd54fe5b00db616224a0848179fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-load-balancer-overview"></a>Vue d’ensemble de l’équilibreur de charge Azure

L'équilibrage de charge Azure offre une haute disponibilité et des performances réseau élevées pour vos applications. Il s’agit d’un équilibreur de charge Layer-4 (TCP, UDP) qui distribue le trafic entrant parmi des instances saines de services définis dans un jeu à charge équilibrée.

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Azure Load Balancer peut être configuré pour :

* équilibrer la charge du trafic Internet entrant sur les machines virtuelles. Cette configuration est appelée [équilibrage de charge avec accès par Internet](load-balancer-internet-overview.md).
* équilibrer le trafic entre des machines virtuelles dans un réseau virtuel, entre des machines virtuelles dans les services cloud ou entre des ordinateurs locaux et des machines virtuelles dans un réseau virtuel entre différents locaux. Cette configuration est appelée [équilibrage de charge interne](load-balancer-internal-overview.md).
* transférer du trafic externe vers une instance spécifique de machine virtuelle.

Toutes les ressources du cloud ont besoin d'une adresse IP publique pour être accessibles depuis Internet. L’infrastructure de cloud dans Azure utilise des adresses IP non routables pour ses ressources. Azure utilise la traduction d’adresses réseau (NAT) avec des adresses IP publiques pour communiquer avec Internet.

## <a name="load-balancer-features"></a>Fonctionnalités d’équilibrage de charge

* Distribution basée sur le hachage

    L'Équilibrage de charge Azure utilise un algorithme de distribution basé sur le hachage. Par défaut, il utilise un hachage à 5 tuples composé de l’adresse IP source, du port source, de l’adresse IP de destination, du port de destination et du type de protocole pour mapper le trafic vers les serveurs disponibles. Il fournit l’adhérence uniquement *dans* une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance derrière le point de terminaison à charge équilibrée. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir de la même adresse IP source, le port source change. Cela peut pousser le trafic à accéder à un autre point de terminaison dans un autre centre de données.

    Pour en savoir plus, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md). Le graphique suivant montre la distribution basée sur le hachage :

    ![Distribution basée sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)

    Figure : distribution basée sur le hachage

* Réacheminement de port

    L'équilibrage de charge Azure vous permet de contrôler la gestion des communications entrantes. Ces communications comprennent le trafic initié par des hôtes Internet, des machines virtuelles dans d'autres services cloud ou des réseaux virtuels. Ce contrôle est représenté par un point de terminaison (également appelé point de terminaison d'entrée).

    Un point de terminaison d’entrée écoute sur un port public et transmet le trafic vers un port interne. Vous pouvez mapper les mêmes ports pour un point de terminaison interne ou externe, ou utiliser un port différent. Par exemple : vous pouvez avoir une écoute configurée du serveur web sur le port 81 alors que le mappage de point de terminaison public est le port 80. La création d’un point de terminaison public déclenche la création d’une instance d’équilibrage de charge.

    En cas de création avec le Portail Azure, le portail crée automatiquement des points de terminaison sur la machine virtuelle pour le trafic RDP (Remote Desktop Protocol) et de la session Windows PowerShell distante. Vous pouvez utiliser ces points de terminaison pour administrer à distance la machine virtuelle sur Internet.

* Reconfiguration automatique

    L'équilibrage de charge Azure se reconfigure instantanément lors de la mise à l'échelle d'instances vers le haut ou vers le bas. Cette reconfiguration se produit, par exemple, lorsque l’on augmente le nombre d’instances de rôles Web/de travail dans un service cloud ou lorsque l’on ajoute des machines virtuelles supplémentaires dans le même jeu à charge équilibrée.

* Surveillance des services

    Azure Load Balancer peut tester l’intégrité des différentes instances de serveur. Lorsqu’une sonde ne répond pas, l’équilibrage de charge n’envoie plus de nouvelles connexions aux instances défaillantes. Les connexions existantes ne sont pas concernées.

    Trois types de sondes sont pris en charge :

    + **Sonde d’agent invité (sur machines virtuelles de plateforme en tant que service uniquement)** : l’équilibrage de charge utilise l’agent invité dans la machine virtuelle. L’agent invité écoute et répond HTTP 200 OK uniquement lorsque l’instance est prête (c’est-à-dire qu’elle ne se trouve pas dans un état de type occupé, recyclage ou arrêt). Si l’agent ne répond pas HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas et arrête d’envoyer du trafic vers cette instance. L’équilibreur de charge continue d’effectuer un test ping sur l’instance. Si l’agent invité répond avec un HTTP 200, l’équilibrage de charge envoie à nouveau du trafic vers cette instance. Lorsque vous utilisez un rôle web, le code de votre site web s'exécute généralement dans w3wp.exe, qui n'est pas surveillé par l'agent Azure Fabric ou l'agent invité. Cela signifie que les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne seront pas signalés à l’agent invité et l’équilibrage de charge ne saura pas qu’il doit placer cette instance hors service.
    + **Sonde personnalisée HTTP :** cette sonde remplace la sonde (agent invité) par défaut. Vous pouvez l'utiliser pour créer votre propre logique personnalisée pour déterminer l'état de l'instance de rôle. L’équilibrage de charge sonde régulièrement votre point de terminaison (toutes les 15 secondes, par défaut). L’instance est considérée comme en service si elle répond avec le code de statut HTTP 200 ou un paquet TCP ACK dans le délai imparti (31 secondes par défaut). C’est utile pour implémenter votre propre logique afin de supprimer des instances de l’équilibreur de charge. Vous pouvez, par exemple, configurer l'instance pour qu'elle renvoie un état autre que 200 si l'instance a une utilisation supérieure à 90 % de l'UC. Pour les rôles Web utilisant w3wp.exe, vous obtenez également une analyse automatique de votre site web, dans la mesure où les erreurs dans votre code de site web renvoient un état autre que 200 à la sonde.
    + **Sonde personnalisée TCP :** cette sonde s’appuie sur l’établissement réussi d’une session TCP sur un port de sonde défini.

    Pour plus d’informations, consultez le [schéma LoadBalancerProbe](https://msdn.microsoft.com/library/azure/jj151530.aspx).

* Source NAT

    Tout le trafic sortant vers Internet à partir de votre service d’origine passe par SNAT (Source NAT) avec la même adresse IP virtuelle que le trafic entrant. SNAT offre des avantages importants :

    + il permet une mise à niveau et une récupération d'urgence simples des services, étant donné que l'adresse IP virtuelle peut être dynamiquement mappée à une autre instance du service ;
    + Il facilite la gestion des listes de contrôle d’accès (ACL). Les ACL exprimées sous forme d’adresses IP virtuelles ne changent pas lorsque les services montent en puissance, descendent en puissance ou sont redéployés.

    La configuration de l’équilibrage de charge prend en charge le NAT « plein cône » pour UDP. Le NAT« plein cône » est un type de NAT où le port autorise les connexions entrantes à partir de n’importe quel hôte externe (en réponse à une requête sortante).

    Pour chaque nouvelle connexion sortante initiée par une machine virtuelle, un port sortant est également alloué par l’équilibrage de charge. L’hôte externe voit le trafic avec un port alloué à une adresse IP virtuelle. Pour les scénarios exigeant un grand nombre de connexions sortantes, il est recommandé d’utiliser des adresses [IP publiques de niveau d’instance](../virtual-network/virtual-networks-instance-level-public-ip.md) afin que les machines virtuelles disposent d’une adresse IP sortante dédiée pour SNAT. Cela réduit le risque d’épuisement du port.

    Consultez l’article [connexions sortantes](load-balancer-outbound-connections.md) pour plus d’informations sur ce sujet.

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>Prise en charge de plusieurs adresses IP à équilibrage de charge pour les machines virtuelles
Vous pouvez affecter plusieurs adresses IP publiques à charge équilibrée à un jeu de machines virtuelles. Cela vous permet d'héberger plusieurs sites Web SSL et/ou plusieurs écouteurs de groupe de disponibilité SQL Server Always On sur le même jeu de machines virtuelles. Pour en savoir plus, consultez [Plusieurs adresses IP virtuelles par service cloud](load-balancer-multivip.md).

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>Limites

Les pools back-end d’équilibreur de charge peuvent héberger toutes les références SKU de machines virtuelles, sauf le niveau De base.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l’[équilibrage de charge accessible sur Internet](load-balancer-internet-overview.md)

- En savoir plus sur l’[équilibrage de charge interne](load-balancer-internal-overview.md)

- Créer un [équilibrage de charge accessible sur Internet](load-balancer-get-started-internet-portal.md)

- Découvrir les autres [fonctionnalités de réseau](../networking/networking-overview.md) clés d’Azure

