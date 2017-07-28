---
title: "Homologation de réseaux virtuels Azure | Microsoft Docs"
description: "En savoir plus sur l’homologation de réseaux virtuels dans Azure."
services: virtual-network
documentationcenter: na
author: NarayanAnnamalai
manager: jefco
editor: tysonn
ms.assetid: eb0ba07d-5fee-4db0-b1cb-a569b7060d2a
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: narayan
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 393557074db2ddbeb53ca20873a33d06874c4dc8
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

---
# <a name="virtual-network-peering"></a>Homologation de réseaux virtuels
L’homologation de réseaux virtuels vous permet de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau à toutes fins de connectivité. Ils sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans ces réseaux virtuels peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

Le trafic entre les machines virtuelles des réseaux virtuels homologués est acheminé via l’infrastructure Azure de façon assez semblable au trafic entre des machines virtuelles d’un même réseau virtuel. Voici quelques-uns des avantages de l’homologation de réseaux virtuels :

* Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
* Possibilité d’utiliser des ressources telles que des appliances de réseau virtuel et des passerelles VPN en tant que points de transit dans un réseau virtuel homologué.
* Possibilité d’homologuer deux réseaux virtuels créés via le modèle de déploiement Azure Resource Manager ou d’homologuer un réseau virtuel créé via Resource Manager pour un réseau virtuel créé via le modèle de déploiement classique. Lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour en savoir plus sur les différences entre les deux modèles de déploiement Azure.

## <a name="requirements-constraints"></a>Exigences et contraintes

* Les réseaux virtuels homologués doivent se trouver dans la même région Azure. Vous pouvez vous connecter à des réseaux virtuels dans différentes régions Azure à l’aide d’une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
* Les réseaux virtuels homologués doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
* Il n’est pas possible d’ajouter ni de supprimer des espaces d’adressage dans un réseau virtuel une fois que celui-ci est homologué avec un autre réseau virtuel.
* L’homologation se fait entre deux réseaux virtuels. Il n’y a aucune relation transitive entre homologations. Par exemple, si virtualNetworkA est homologué avec virtualNetworkB, lequel est homologué avec virtualNetworkC, virtualNetworkA n’est *pas* homologué avec virtualNetworkC.
* Vous pouvez homologuer des réseaux virtuels qui existent dans deux abonnements différents sous réserve qu’un utilisateur privilégié (voir la section [specific permissions](create-peering-different-deployment-models-subscriptions.md#permissions) [Autorisations spécifiques]) de chacun de ces abonnements autorise l’homologation, et que les abonnements soient associés au même locataire Azure Active Directory. Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour vous connecter aux réseaux virtuels des abonnements associés à différents locataires Active Directory.
* Deux réseaux virtuels peuvent être homologués s’ils sont créés via le modèle de déploiement Resource Manager ou si l’un est créé via le modèle de déploiement Resource Manager et l’autre via le modèle de déploiement classique. Toutefois, deux réseaux virtuels créés via le modèle de déploiement classique ne peuvent pas être homologués l’un envers l’autre. Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour connecter deux réseaux virtuels créés via le modèle de déploiement classique.
* Si la communication entre les machines virtuelles de réseaux virtuels homologués ne présente pas de restrictions de bande passante supplémentaires, une bande passante réseau maximale qui dépend de la taille de la machine virtuelle s’applique toujours. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, lisez les articles [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sur les tailles de machine virtuelle.
* La résolution de noms DNS internes fournie par Azure pour les machines virtuelles ne fonctionne pas entre des réseaux virtuels homologués. Les machines virtuelles présentent des noms DNS internes pouvant uniquement être résolus au sein du réseau virtuel local. Cependant, vous pouvez configurer des machines virtuelles connectées à des réseaux virtuels homologués en tant que serveurs DNS pour un réseau virtuel. Pour plus de détails, lisez l’article [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

![Homologation de réseaux virtuels de base](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connectivité
Une fois deux réseaux virtuels homologués, les ressources de l’un peuvent se connecter directement à celles du réseau virtuel homologué. Ces deux réseaux bénéficient d’une connectivité de niveau IP totale.

La latence du réseau pour un aller-retour entre deux machines virtuelles de réseaux virtuels homologués est la même qu’au sein d’un réseau virtuel. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée au sein de l’homologation.

Le trafic entre les machines virtuelles dans des réseaux virtuels homologués est acheminé directement via l’infrastructure principale d’Azure et non par le biais d’une passerelle.

Les machines virtuelles connectées à un réseau virtuel peuvent accéder aux points de terminaison d’équilibrage de charge interne du réseau virtuel homologué. Des groupes de sécurité réseau peuvent être appliqués dans l’un ou l’autre des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou à des sous-réseaux si besoin est.

Lors de la configuration de l’homologation de réseaux virtuels, vous pouvez ouvrir ou fermer les règles du groupe de sécurité réseau entre les réseaux virtuels. Si vous ouvrez totalement la connectivité entre les réseaux virtuels homologués (ce qui est l’option par défaut), vous pouvez alors appliquer des groupes de sécurité réseau à des sous-réseaux ou des machines virtuelles spécifiques pour bloquer ou refuser certains accès. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article de présentation des [groupes de sécurité réseau](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Chaînage de services
Vous pouvez configurer des itinéraires définis par l’utilisateur qui pointent vers des machines virtuelles de réseaux virtuels homologués en tant qu’adresse IP du « tronçon suivant » pour activer le chaînage de services. Le chaînage de services vous permet de diriger le trafic d’un réseau virtuel vers une appliance virtuelle d’un réseau virtuel homologué via les itinéraires définis.

Vous pouvez également déployer efficacement des environnements de type hub-and-spoke où le nœud peut héberger des composants d’infrastructure tels qu’une appliance virtuelle réseau. Tous les réseaux virtuels spoke peuvent ensuite être homologués avec le réseau virtuel hub. Le trafic peut passer par des appliances virtuelles réseau exécutées sur le réseau virtuel hub. En résumé, l’homologation de réseaux virtuels permet de définir l’adresse IP du tronçon suivant dans l’itinéraire défini par l’utilisateur sur l’adresse IP d’une machine virtuelle du réseau virtuel homologué. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article de présentation des [itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md).

## <a name="gateways-and-on-premises-connectivity"></a>Passerelles et connectivité locale
Chaque réseau virtuel, qu’il soit homologué ou non avec un autre réseau virtuel, peut posséder sa propre passerelle et l’utiliser pour se connecter à un réseau local. Vous pouvez également configurer des [connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à l’aide de passerelles, même si les réseaux virtuels sont homologués.

Lorsque les deux options d’interconnexion de réseaux virtuels sont configurées, le trafic entre les réseaux virtuels transite via la configuration d’homologation (c’est-à-dire via le réseau principal Azure).

Lorsque des réseaux virtuels sont homologués, vous pouvez également configurer la passerelle du réseau virtuel homologué en tant que point de transit vers un réseau local. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne peut posséder qu’une seule passerelle. Il peut s’agir d’une passerelle locale ou distante (dans le réseau virtuel homologué), comme l’illustre l’image suivante :

![Transit entre des réseaux virtuels homologués](./media/virtual-networks-peering-overview/figure02.png)

Le transit de la passerelle n’est pas pris en charge dans la relation d’homologation entre des réseaux virtuels créés via des modèles de déploiement différents. Les deux réseaux virtuels dans la relation d’homologation doivent avoir été créés via Resource Manager pour qu’un transit de la passerelle fonctionne.

Lorsque des réseaux virtuels qui partagent une même connexion Azure ExpressRoute sont homologués, le trafic entre eux transite via la relation d’homologation (c’est-à-dire via le réseau principal Azure). Vous pouvez toujours utiliser des passerelles locales dans chaque réseau virtuel pour vous connecter au circuit local. Vous pouvez également utiliser une passerelle partagée et configurer le transit pour la connectivité locale.

## <a name="provisioning"></a>Approvisionnement
L’homologation de réseaux virtuels est une opération nécessitant des privilèges. Il s’agit d’une fonction distincte sous l’espace de noms VirtualNetworks. Un utilisateur peut se voir accorder des droits spécifiques pour autoriser l’homologation. Un utilisateur qui dispose d’un accès en lecture-écriture au réseau virtuel hérite automatiquement de ces droits.

Un utilisateur administrateur ou disposant des privilèges d’homologation peut lancer une opération d’homologation sur un autre réseau virtuel. S’il existe une demande d’homologation correspondante de l’autre côté et si les autres exigences sont remplies, l’homologation est établie.

## <a name="limits"></a>limites
Le nombre d’homologations autorisées pour un même réseau virtuel est limité. Pour plus d’informations, consultez la section [Limites de mise en réseau d’Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Tarification
Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une homologation de réseau virtuel. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Étapes suivantes

* Suivez un didacticiel d’homologation de réseaux virtuels. Une homologation est générée entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel pour l’un des scénarios suivants :
 
    |Modèle de déploiement Azure  | Abonnement  |
    |---------|---------|
    |Les deux modèles Resource Manager |[Identique](virtual-network-create-peering.md)|
    | |[Différent](create-peering-different-subscriptions.md)|
    |Un modèle Resource Manager, un modèle classique     |[Identique](create-peering-different-deployment-models.md)|
    | |[Différent](create-peering-different-deployment-models-subscriptions.md)|

* Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering). 
* En savoir plus sur tous les [paramètres d’homologation de réseaux virtuels et sur la façon de les modifier](virtual-network-manage-peering.md).

