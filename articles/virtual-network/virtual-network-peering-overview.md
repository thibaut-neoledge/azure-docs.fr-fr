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
ms.date: 10/17/2016
ms.author: narayan
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6fbcdcf77f46a3c643e8fedc1d112588cbd7befc
ms.lasthandoff: 04/03/2017


---
# <a name="virtual-network-peering"></a>Homologation de réseaux virtuels
L’homologation de réseaux virtuels vous permet de connecter deux réseaux virtuels situés dans la même région via le réseau principal Azure. Une fois homologués, les deux réseaux virtuels apparaissent comme un seul réseau pour les besoins de connectivité. Les deux réseaux virtuels sont toujours gérés comme des ressources distinctes, mais les machines virtuelles se trouvant dans les réseaux virtuels homologués peuvent communiquer directement entre elles à l’aide d’adresses IP privées.

Le trafic entre les machines virtuelles des réseaux virtuels homologués est acheminé via l’infrastructure Azure de façon assez similaire au trafic entre des machines virtuelles d’un même réseau virtuel. Voici quelques-uns des avantages de l’homologation de réseaux virtuels :

* Connexion à latence faible et haut débit entre les ressources de différents réseaux virtuels.
* Possibilité d’utiliser des ressources telles que des appliances de réseau virtuel et des passerelles VPN en tant que points de transit dans un réseau virtuel homologué.
* Possibilité d’homologuer deux réseaux virtuels créés via le modèle de déploiement Azure Resource Manager ou d’homologuer un réseau virtuel créé via Resource Manager pour un réseau virtuel créé via le modèle de déploiement classique. Lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md) pour en savoir plus sur les différences entre les deux modèles de déploiement Azure.

Exigences et principaux aspects de l’homologation de réseaux virtuels :

* Les réseaux virtuels homologués doivent se trouver dans la même région Azure.
* Les réseaux virtuels homologués doivent avoir des espaces d’adressage IP qui ne se recouvrent pas.
* L’homologation concerne deux réseaux virtuels, mais aucune relation transitive n’en découle entre les homologations. Par exemple, si VNetA est homologué avec VNetB et que VNetB est homologué avec VNetC, VNetA n’est *pas* homologué pour VNetC.
* Vous pouvez homologuer des réseaux virtuels dans deux abonnements, à condition qu’un utilisateur privilégié de chacun de ces abonnements autorise l’homologation et que les abonnements soient associés au même locataire Active Directory.
* Deux réseaux virtuels peuvent être homologués s’ils sont créés via le modèle de déploiement Resource Manager, ou si l’un est créé via le modèle de déploiement Resource Manager et l’autre via le modèle de déploiement classique. Toutefois, deux réseaux virtuels créés via le modèle de déploiement classique ne peuvent pas être homologués l’un envers l’autre. Lors de l’homologation de réseaux virtuels créés via des modèles de déploiement différents, les réseaux virtuels doivent se trouver dans le *même* abonnement. La possibilité d’homologuer des réseaux virtuels créés via des modèles de déploiement différents qui se trouvent dans des abonnements *différents* est disponible dans la **version préliminaire**. Lisez l’article [Créer une homologation de réseau virtuel à l’aide de PowerShell](virtual-networks-create-vnetpeering-arm-ps.md) pour plus de détails.
* Si la communication entre les machines virtuelles dans des réseaux virtuels homologués présente des restrictions de bande passante supplémentaires, une bande passante réseau maximale qui dépend de la taille de la machine virtuelle s’applique toujours. Pour en savoir plus sur la bande passante réseau maximale pour différentes tailles de machine virtuelle, lisez les articles [Windows](../virtual-machines/windows/sizes.md) ou [Linux](../virtual-machines/linux/sizes.md) sur les tailles de machine virtuelle.

![Homologation de réseaux virtuels de base](./media/virtual-networks-peering-overview/figure01.png)

## <a name="connectivity"></a>Connectivité
Une fois les deux réseaux virtuels homologués, les machines virtuelles ou les rôles de services cloud dans le réseau virtuel peuvent se connecter directement à d’autres ressources connectées au réseau virtuel homologué. Les deux réseaux virtuels bénéficient d’une connectivité de niveau IP totale.

La latence du réseau pour un aller-retour entre deux machines virtuelles de réseaux virtuels homologués est la même qu’au sein d’un seul réseau virtuel. Le débit du réseau repose sur la bande passante autorisée pour la machine virtuelle proportionnellement à sa taille. Aucune restriction de bande passante supplémentaire n’est appliquée au sein de l’homologation.

Le trafic entre les machines virtuelles de réseaux virtuels homologués est acheminé directement via l’infrastructure principale d’Azure et non par le biais d’une passerelle.

Les machines virtuelles connectées à un réseau virtuel peuvent accéder aux points de terminaison d’équilibrage de charge (ILB) internes dans le réseau virtuel homologué. Des groupes de sécurité réseau peuvent être appliqués dans un des réseaux virtuels pour bloquer l’accès à d’autres réseaux virtuels ou à des sous-réseaux si nécessaire.

Lors de la configuration de l’homologation, vous pouvez ouvrir ou fermer les règles du groupe de sécurité réseau entre les réseaux virtuels. Si vous ouvrez une connectivité totale entre des réseaux virtuels homologués (qui est l’option par défaut), vous pouvez appliquer des groupes de sécurité réseau à des sous-réseaux ou des machines virtuelles spécifiques pour bloquer ou refuser un accès spécifique. Lisez l’article [Groupes de sécurité réseau](virtual-networks-nsg.md) pour en savoir plus sur les groupes de sécurité réseau.

La résolution de noms DNS internes fournie par Azure pour les machines virtuelles ne fonctionne pas entre des réseaux virtuels homologués. Les machines virtuelles présentent des noms DNS internes pouvant uniquement être résolus au sein du réseau virtuel local. Vous pouvez toutefois configurer des machines virtuelles connectées à des réseaux virtuels homologués en tant que serveurs DNS pour un réseau virtuel. Lisez l’article [Résolution de noms à l’aide de votre propre serveur DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) pour plus de détails.

## <a name="service-chaining"></a>Chaînage de services
Vous pouvez configurer des itinéraires définis par l’utilisateur (UDR) qui pointent vers des machines virtuelles dans des réseaux virtuels homologués en tant qu’adresse IP du « tronçon suivant », comme illustré dans le schéma plus loin dans cet article. Cela active un chaînage de services, vous permettant ainsi de diriger le trafic d’un réseau virtuel vers une appliance virtuelle exécutée dans un réseau virtuel homologué via les UDR.

Vous pouvez également déployer efficacement des environnements de type hub-and-spoke où le nœud peut héberger des composants d’infrastructure tels qu’une appliance virtuelle réseau. Tous les réseaux virtuels reliés en étoile peuvent être homologués avec ce nœud et diriger un sous-ensemble du trafic vers des appliances exécutées dans le réseau virtuel central. En résumé, l’homologation de réseaux virtuels permet de définir l’adresse IP du tronçon suivant dans l’UDR sur l’adresse IP d’une machine virtuelle du réseau virtuel homologué. Lisez l’article [Itinéraires définis par l’utilisateur](virtual-networks-udr-overview.md) pour plus d’informations sur les UDR.

## <a name="gateways-and-on-premises-connectivity"></a>Passerelles et connectivité locale
Chaque réseau virtuel, qu’il soit homologué ou non avec un autre réseau virtuel, peut posséder sa propre passerelle et l’utiliser pour se connecter à un réseau local. Les utilisateurs peuvent également configurer des [connexions de réseau virtuel à réseau virtuel](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) à l’aide de passerelles, même si les réseaux virtuels sont homologués.

Lorsque les deux options d’interconnexion de réseaux virtuels sont configurées, le trafic entre les réseaux virtuels transite via la configuration d’homologation (c’est-à-dire via le réseau principal Azure).

Lorsque des réseaux virtuels sont homologués, les utilisateurs peuvent également configurer l’environnement de manière à utiliser la passerelle du réseau virtuel homologué comme point de transit vers un réseau local. Dans ce cas, le réseau virtuel qui utilise une passerelle distante ne peut pas posséder sa propre passerelle. Un réseau virtuel ne peut posséder qu’une seule passerelle. La passerelle peut être une passerelle locale ou distante (dans le réseau virtuel homologué), comme l’illustre l’image suivante :

![Transit entre des réseaux virtuels homologués](./media/virtual-networks-peering-overview/figure02.png)

Le transit de la passerelle n’est pas pris en charge dans la relation d’homologation entre des réseaux virtuels créés via des modèles de déploiement différents. Les deux réseaux virtuels dans la relation d’homologation doivent avoir été créés via Resource Manager pour qu’un le transit de la passerelle fonctionne.

Lorsque des réseaux virtuels qui partagent une même connexion Azure ExpressRoute sont homologués, le trafic entre eux transite via la relation d’homologation (c’est-à-dire via le réseau principal Azure). Vous pouvez toujours utiliser des passerelles locales dans chaque réseau virtuel pour vous connecter au circuit local. Vous pouvez également utiliser une passerelle partagée et configurer le transit pour la connectivité locale.

## <a name="provisioning"></a>Approvisionnement
L’homologation de réseaux virtuels est une opération nécessitant des privilèges. Il s’agit d’une fonction distincte sous l’espace de noms VirtualNetworks. Un utilisateur peut se voir accorder des droits spécifiques pour autoriser l’homologation. Un utilisateur qui dispose d’un accès en lecture-écriture au réseau virtuel hérite automatiquement de ces droits.

Un utilisateur administrateur ou disposant des privilèges d’homologation peut lancer une opération d’homologation sur un autre réseau virtuel. S’il existe une demande d’homologation correspondante de l’autre côté et si les autres exigences sont remplies, l’homologation est établie.

Consultez les articles de la section [Étapes suivantes](#next-steps) de cet article pour en savoir plus sur la création d’une homologation entre deux réseaux virtuels.

## <a name="limits"></a>limites
Le nombre d’homologations autorisées pour un même réseau virtuel est limité. Pour plus d’informations, consultez la section [Limites de mise en réseau d’Azure](../azure-subscription-service-limits.md#networking-limits) .

## <a name="pricing"></a>Tarification
Un coût nominal s’applique pour le trafic entrant et sortant qui utilise une homologation de réseau virtuel. Pour plus d’informations, consultez la [page de tarification](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la création d’une homologation de réseau virtuel à l’aide de :

* [Le portail Azure](virtual-networks-create-vnetpeering-arm-portal.md)
* [Azure PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
* [Un modèle Azure Resource Manager](virtual-networks-create-vnetpeering-arm-template-click.md)

