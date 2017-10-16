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
ms.date: 09/25/2017
ms.author: narayan;anavin
ms.openlocfilehash: 082cd8a6cf50f76c89fe5995047396c734f83034
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="virtual-network-peering"></a>Homologation de réseaux virtuels

Un [réseau virtuel Azure](virtual-networks-overview.md) constitue votre espace réseau privé dans Azure, ce qui vous permet de connecter en toute sécurité des ressources Azure entre elles.

L’homologation de réseaux virtuels vous permet de connecter des réseaux virtuels en toute transparence. Une fois homologués, les réseaux virtuels apparaissent comme un seul réseau à toutes fins de connectivité. Les machines virtuelles qui se trouvent dans des réseaux virtuels homologués peuvent communiquer entre elles directement.
Le trafic entre les machines virtuelles des réseaux virtuels homologués est acheminé via l’infrastructure principale de Microsoft de façon assez semblable au trafic entre des machines virtuelles d’un même réseau virtuel via des adresses IP *privées* seulement.

>[!IMPORTANT]
> Vous pouvez homologuer des réseaux virtuels dans différentes régions Azure. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Vous pouvez [inscrire votre abonnement pour la version préliminaire](virtual-network-create-peering.md). L’homologation de réseaux virtuels dans les mêmes régions est généralement disponible.
>

Voici quelques-uns des avantages de l’homologation de réseaux virtuels :

* Le trafic qui traverse les homologations de réseaux virtuels est entièrement privé. Il traverse le réseau principal de Microsoft, sans impliquer aucune passerelle ni connexion Internet publiques.
* Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
* Capacité à utiliser des ressources d’un réseau virtuel dans un autre réseau virtuel, une fois homologués.
* L’homologation de réseaux virtuels vous aide à transférer des données dans des abonnements Azure, des modèles de déploiement et dans les régions Azure (version préliminaire).
* Possibilité d’homologuer deux réseaux virtuels créés via le modèle de déploiement Azure Resource Manager ou d’homologuer un réseau virtuel créé via Resource Manager pour un réseau virtuel créé via le modèle de déploiement classique. Lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour en savoir plus sur les différences entre les deux modèles de déploiement Azure.

## <a name="requirements-constraints"></a>Exigences et contraintes

* L’appairage de réseaux virtuels au sein d’une même région est généralement possible. L’appairage de réseaux virtuels appartenant à des régions différentes est actuellement en préversion dans les régions États-Unis Centre-Ouest, Centre du Canada et Ouest des États-Unis 2. Vous pouvez [inscrire votre abonnement pour la préversion](virtual-network-create-peering.md).
    > [!WARNING]
    > Les appairages de réseaux virtuels créés dans ce scénario peuvent ne pas avoir le même niveau de disponibilité et de fiabilité qu’avec les scénarios utilisant la version publique. Les appairages de réseaux virtuels peuvent avoir des fonctionnalités limitées et peuvent ne pas être disponibles dans toutes les régions Azure. Pour les notifications les plus récentes sur la disponibilité et l’état de cette fonctionnalité, consultez la page relative aux [mises à jour du réseau virtuel Azure](https://azure.microsoft.com/updates/?product=virtual-network).

* Les réseaux virtuels homologués doivent avoir des espaces d’adressage IP qui ne se chevauchent pas.
* Il n’est pas possible d’ajouter ni de supprimer des espaces d’adressage dans un réseau virtuel une fois que celui-ci est homologué avec un autre réseau virtuel.
* L’homologation se fait entre deux réseaux virtuels. Il n’y a aucune relation transitive entre homologations. Par exemple, si virtualNetworkA est homologué avec virtualNetworkB, lequel est homologué avec virtualNetworkC, virtualNetworkA n’est *pas* homologué avec virtualNetworkC.
* Vous pouvez homologuer des réseaux virtuels qui existent dans deux abonnements différents sous réserve qu’un utilisateur privilégié (voir la section [specific permissions](create-peering-different-deployment-models-subscriptions.md#permissions) [Autorisations spécifiques]) de chacun de ces abonnements autorise l’homologation, et que les abonnements soient associés au même locataire Azure Active Directory. Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour vous connecter aux réseaux virtuels des abonnements associés à différents locataires Active Directory.
* Deux réseaux virtuels peuvent être homologués s’ils sont créés via le modèle de déploiement Resource Manager ou si l’un est créé via le modèle de déploiement Resource Manager et l’autre via le modèle de déploiement classique. Toutefois, les réseaux virtuels créés via le modèle de déploiement classique ne peuvent pas être homologués l’un envers l’autre. Vous pouvez utiliser une [passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) pour connecter des réseaux virtuels créés via le modèle de déploiement classique.
* Si la communication entre les machines virtuelles de réseaux virtuels homologués ne présente pas de restrictions de bande passante supplémentaires, une bande passante réseau maximale qui dépend de la taille de la machine virtuelle s’applique toujours. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, lisez les articles [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sur les tailles de machine virtuelle.

     ![Homologation de réseaux virtuels de base](./media/virtual-networks-peering-overview/figure03.png)

## <a name="connectivity"></a>Connectivité

Une fois les réseaux virtuels homologués, les ressources de l’un peuvent se connecter directement à celles du réseau virtuel homologué.

La latence du réseau entre deux machines virtuelles de réseaux virtuels homologués dans la même région est la même qu’au sein d’un seul réseau virtuel. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée au sein de l’homologation.

Le trafic entre les machines virtuelles dans des réseaux virtuels homologués est acheminé directement via l’infrastructure principale de Microsoft ou Internet public.

Les machines virtuelles d’un réseau virtuel peuvent accéder à l’équilibreur de charge interne du réseau virtuel homologué dans la même région. La prise en charge d’équilibreur de charge interne ne s’étend pas aux réseaux virtuels homologués à l’échelle mondiale dans la version préliminaire. La version de disponibilité générale de l’homologation de réseaux virtuels mondiale prendra en charge l’équilibreur de charge interne.

Des groupes de sécurité réseau peuvent être appliqués dans l’un ou l’autre des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou à des sous-réseaux si besoin est.
Lors de la configuration de l’homologation de réseaux virtuels, vous pouvez ouvrir ou fermer les règles du groupe de sécurité réseau entre les réseaux virtuels. Si vous ouvrez totalement la connectivité entre les réseaux virtuels homologués (ce qui est l’option par défaut), vous pouvez alors appliquer des groupes de sécurité réseau à des sous-réseaux ou des machines virtuelles spécifiques pour bloquer ou refuser certains accès. Pour en savoir plus sur les groupes de sécurité réseau, consultez l’article de présentation des [groupes de sécurité réseau](virtual-networks-nsg.md).

## <a name="service-chaining"></a>Chaînage de services

Vous pouvez configurer des itinéraires définis par l’utilisateur qui pointent vers des machines virtuelles de réseaux virtuels homologués en tant qu’adresse IP du « tronçon suivant » pour activer le chaînage de services. Le chaînage de services vous permet de diriger le trafic d’un réseau virtuel vers une appliance virtuelle d’un réseau virtuel homologué via les itinéraires définis.

Vous pouvez également déployer efficacement des environnements de type hub-and-spoke où le nœud peut héberger des composants d’infrastructure tels qu’une appliance virtuelle réseau. Tous les réseaux virtuels spoke peuvent ensuite être homologués avec le réseau virtuel hub. Le trafic peut passer par des appliances virtuelles réseau exécutées sur le réseau virtuel hub. En résumé, l’homologation de réseaux virtuels permet de définir l’adresse IP du tronçon suivant dans l’itinéraire défini par l’utilisateur sur l’adresse IP d’une machine virtuelle du réseau virtuel homologué. Pour en savoir plus sur les itinéraires définis par l’utilisateur, consultez l’article de présentation des [itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md). Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)

## <a name="gateways-and-on-premises-connectivity"></a>Passerelles et connectivité locale

Chaque réseau virtuel, qu’il soit homologué ou non avec un autre réseau virtuel, peut posséder sa propre passerelle et l’utiliser pour se connecter à un réseau local. Vous pouvez également configurer des [connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) à l’aide de passerelles, même si les réseaux virtuels sont homologués.

Lorsque les deux options d’interconnexion de réseaux virtuels sont configurées, le trafic entre les réseaux virtuels transite via la configuration d’homologation (c’est-à-dire via le réseau principal Azure).

Lorsque des réseaux virtuels sont homologués dans la même région, vous pouvez également configurer la passerelle du réseau virtuel homologué en tant que point de transit vers un réseau local. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne peut posséder qu’une seule passerelle. Il peut s’agir d’une passerelle locale ou distante (dans le réseau virtuel homologué), comme l’illustre l’image suivante :

![Transit d’homologation de réseaux virtuels](./media/virtual-networks-peering-overview/figure04.png)

Le transit de la passerelle n’est pas pris en charge dans la relation d’homologation entre des réseaux virtuels créés via des modèles de déploiement différents ou des régions différentes. Les deux réseaux virtuels dans la relation d’homologation doivent avoir été créés via Resource Manager et doivent être dans la même région pour qu’un transit de la passerelle fonctionne. Les réseaux virtuels homologués à l’échelle mondiale ne prennent actuellement pas en charge le transit de passerelle.

Lorsque des réseaux virtuels qui partagent une même connexion Azure ExpressRoute sont homologués, le trafic entre eux transite via la relation d’homologation (c’est-à-dire via le réseau principal Azure). Vous pouvez toujours utiliser des passerelles locales dans chaque réseau virtuel pour vous connecter au circuit local. Vous pouvez également utiliser une passerelle partagée et configurer le transit pour la connectivité locale.

## <a name="permissions"></a>Autorisations

L’homologation de réseaux virtuels est une opération nécessitant des privilèges. Il s’agit d’une fonction distincte sous l’espace de noms VirtualNetworks. Un utilisateur peut se voir accorder des droits spécifiques pour autoriser l’homologation. Un utilisateur qui dispose d’un accès en lecture-écriture au réseau virtuel hérite automatiquement de ces droits.

Un utilisateur administrateur ou disposant des privilèges d’homologation peut lancer une opération d’homologation sur un autre réseau virtuel. Le niveau de permission minimum nécessaire est Collaborateur de réseau. S’il existe une demande d’homologation correspondante de l’autre côté et si les autres exigences sont remplies, l’homologation est établie.

Par exemple, si vous homologuez des réseaux virtuels nommés myvirtual networkA et myvirtual networkB, votre compte doit avoir le rôle ou les autorisations minimaux suivants pour chaque réseau virtuel :

|Réseau virtuel|Modèle de déploiement|Rôle|Autorisations|
|---|---|---|---|
|myvirtual networkA|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/A|
|myvirtual networkB|Gestionnaire de ressources|[Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Classique|[Collaborateur de réseau classique](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

## <a name="monitor"></a>Surveiller

Lors de l’homologation de deux réseaux virtuels créés via le Gestionnaire de ressources, une homologation doit être configurée pour chaque réseau virtuel dans l’homologation.
Vous ne pouvez pas surveiller le statut de votre connexion d’homologation. Les valeurs possibles pour l’état de l’homologation sont les suivantes :

* **Initiée** : Lorsque vous créez l’homologation au deuxième réseau virtuel à partir du premier réseau virtuel, l’état d’homologation est Initiée.

* **Connectée** : Lorsque vous créez l’homologation à partir du deuxième réseau virtuel au premier réseau virtuel, l’état d’homologation est Connectée. Si vous affichez l’état d’homologation pour le premier réseau virtuel, vous voyez que son état est passé de Initiée à Connectée. L’homologation n’est pas correctement établie tant que l’état d’homologation pour les deux homologations de réseau virtuel est Connectée.

* **Déconnecté** : Si un de vos liens d’homologation est supprimé après qu’une connexion a été établie, l’état d’homologation est Déconnectée.

## <a name="troubleshoot"></a>Résolution des problèmes

Pour résoudre les problèmes liés aux flux de trafic sur votre connexion d’homologation, vous pouvez [vérifier les routages effectifs](virtual-network-routes-troubleshoot-portal.md).

Vous pouvez aussi résoudre les problèmes de connectivité à une machine virtuelle d’un réseau virtuel homologué à l’aide de la [vérification de la connectivité](../network-watcher/network-watcher-connectivity-portal.md) de Network Watcher. La vérification de la connectivité affiche le routage directement depuis l’interface réseau de la machine virtuelle source jusqu’à l’interface réseau de la machine virtuelle de destination.

## <a name="limits"></a>limites

Le nombre d’homologations autorisées pour un même réseau virtuel est limité. Le nombre par défaut des homologations est 50. Vous pouvez augmenter le nombre des homologations. Pour plus d’informations, consultez la section [Limites de mise en réseau d’Azure](../azure-subscription-service-limits.md#networking-limits).

## <a name="pricing"></a>Tarification

Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une connexion d’homologation de réseau virtuel. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Étapes suivantes

* Suivez un didacticiel d’homologation de réseaux virtuels. Une homologation est générée entre des réseaux virtuels créés via des modèles de déploiement identiques ou différents qui existent dans des abonnements identiques ou différents. Suivez un didacticiel pour l’un des scénarios suivants :

    |Modèle de déploiement Azure  | Abonnement  |
    |---------|---------|
    |Les deux modèles Resource Manager |[Identique](virtual-network-create-peering.md)|
    | |[Différent](create-peering-different-subscriptions.md)|
    |Un modèle Resource Manager, un modèle classique     |[Identique](create-peering-different-deployment-models.md)|
    | |[Différent](create-peering-different-deployment-models-subscriptions.md)|

* Découvrez comment créer une [topologie de réseau Hub and Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering)
* En savoir plus sur tous les [paramètres d’homologation de réseaux virtuels et sur la façon de les modifier](virtual-network-manage-peering.md).
