---
title: Comprendre les connexions sortantes dans Azure | Microsoft Docs
description: Cet article explique comment Azure permet aux machines virtuelles de communiquer avec les services Internet publics.
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: bec4f89556a2daa41e19b0ecb2ab9bbbed849107
ms.openlocfilehash: 0bf40c5b44ea87c88d4464baf958e8afb7a59c38

---

# <a name="understanding-outbound-connections-in-azure"></a>Comprendre les connexions sortantes dans Azure

Une machine virtuelle (VM) dans Azure peut communiquer avec des points de terminaison en dehors d’Azure dans l’espace d’adressage IP public. Lorsqu’une machine virtuelle démarre un flux sortant vers une destination dans l’espace d’adressage IP public, Azure mappe l’adresse IP privée de la machine virtuelle sur une adresse IP publique et autorise le trafic de retour vers la machine virtuelle.

Azure propose trois méthodes différentes pour réaliser la connectivité sortante. Chaque méthode possède ses propres fonctionnalités et contraintes. Passez en revue chaque méthode soigneusement pour choisir celle qui correspond à vos besoins.

| Scénario | Méthode | Remarque |
| --- | --- | --- |
| Machine virtuelle autonome (aucun équilibrage de charge, aucune adresse IP publique de niveau d’instance) |SNAT par défaut |Azure associe une adresse IP publique pour SNAT |
| Machine virtuelle à charge équilibrée (aucune adresse IP publique de niveau d’instance sur la machine virtuelle) |SNAT utilisant l’équilibrage de charge |Azure utilise une adresse IP publique de l’équilibrage de charge pour SNAT |
| Machine virtuelle avec l’adresse IP publique de niveau d’instance (avec ou sans équilibrage de charge) |SNAT n’est pas utilisé |Azure utilise l’adresse IP publique affectée à la machine virtuelle |

Si vous ne souhaitez pas qu’une machine virtuelle communique avec les points de terminaison en dehors d’Azure dans l’espace d’adressage IP public, vous pouvez utiliser des groupes de sécurité réseau (NSG) pour bloquer l’accès. L’utilisation de groupes de sécurité réseau est abordée plus en détail dans [Empêchement de la connectivité publique](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>Machine virtuelle autonome sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle ne fait pas partie d’un pool Azure Load Balancer et aucune adresse IP publique de niveau d’instance (ILPIP) n’y est affectée. Lorsque la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique. L’adresse IP publique utilisée pour ce flux sortant n’est pas configurable. Azure utilise le mode SNAT (Source Network Address Translation) pour exécuter cette fonction. Les ports éphémères de l’adresse IP publique sont utilisés pour distinguer chaque flux provenant de la machine virtuelle. SNAT alloue dynamiquement des ports éphémères lors de la création de flux. Dans ce contexte, les ports éphémères utilisés pour SNAT sont appelés ports SNAT.

Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre leur utilisation. Un seul port SNAT est utilisé par flux vers une adresse IP de destination unique. En cas de flux multiples vers la même adresse IP de destination, chaque flux utiliser un seul port SNAT. Cela garantit que les flux sont uniques s’ils proviennent de la même adresse IP publique et sont dirigés vers la même adresse IP de destination. Plusieurs flux, chacun d’eux dirigé vers une adresse IP de destination différente, utilisent un seul port SNAT par destination. L’adresse IP de destination rend les flux uniques.

Vous pouvez utiliser [Analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) et le [Journal des événements d’alerte](load-balancer-monitor-log.md#alert-event-log) pour surveiller la présence de messages signalant l’épuisement des ressources de port SNAT. En cas d’épuisement des ressources de port SNAT, les flux sortants échouent jusqu'à ce que des ports SNAT soient libérés par flux existants. L’équilibrage de charge utilise un délai d’inactivité de 4 minutes pour la récupération des ports SNAT.

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>Machine virtuelle à charge équilibrée sans adresse IP publique de niveau d’instance

Dans ce scénario, la machine virtuelle fait partie d’un pool Azure Load Balancer. La machine virtuelle n’a pas d’adresse IP publique affectée. Lorsque la machine virtuelle à charge équilibrée crée un flux sortant, Azure convertit l’adresse IP source privée du flux sortant en une adresse IP source publique du frontend d’équilibrage de charge public. Azure utilise le mode SNAT (Source Network Address Translation) pour exécuter cette fonction. Les ports éphémères de l’adresse IP publique de l’équilibrage de charge sont utilisés pour distinguer chaque flux provenant de la machine virtuelle. SNAT alloue dynamiquement des ports éphémères lors de la création de flux sortants. Dans ce contexte, les ports éphémères utilisés pour SNAT sont appelés ports SNAT.

Les ports SNAT sont une ressource limitée qui peut être épuisée. Il est important de comprendre leur utilisation. Un seul port SNAT est utilisé par flux vers une adresse IP de destination unique. En cas de flux multiples vers la même adresse IP de destination, chaque flux utiliser un seul port SNAT. Cela garantit que les flux sont uniques s’ils proviennent de la même adresse IP publique et sont dirigés vers la même adresse IP de destination. Plusieurs flux, chacun d’eux dirigé vers une adresse IP de destination différente, utilisent un seul port SNAT par destination. L’adresse IP de destination rend les flux uniques.

Vous pouvez utiliser [Analytique des journaux pour l’équilibreur de charge](load-balancer-monitor-log.md) et le [Journal des événements d’alerte](load-balancer-monitor-log.md#alert-event-log) pour surveiller la présence de messages signalant l’épuisement des ressources de port SNAT. En cas d’épuisement des ressources de port SNAT, les flux sortants échouent jusqu'à ce que des ports SNAT soient libérés par flux existants. L’équilibrage de charge utilise un délai d’inactivité de 4 minutes pour la récupération des ports SNAT.

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>Machine virtuelle avec une adresse IP publique de niveau d’instance (avec ou sans équilibrage de charge)

Dans ce scénario, une adresse IP publique de niveau d’instance (ILPIP) est affectée à la machine virtuelle. Peu importe si la machine virtuelle est à charge équilibrée ou non. Lorsqu’une adresse ILPIP est utilisée, SNAT (Source Network Address Translation) n’est pas utilisé. La machine virtuelle utilise l’adresse ILPIP pour tous les flux sortants. Si votre application lance plusieurs flux sortants et que les ressources SNAT sont épuisées, vous devez envisager d’affecter une adresse ILPIP afin d’éviter les contraintes de SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Découverte de l’adresse IP publique utilisée par une machine virtuelle donnée

Il existe de nombreuses manières de déterminer l’adresse IP source publique d’une connexion sortante. OpenDNS fournit un service qui peut vous indiquer l’adresse IP publique votre machine virtuelle. La commande nslookup vous permet d’envoyer une requête DNS pour le myip.opendns.com du nom au programme de résolution OpenDNS. Le service retourne l’adresse IP source qui a été utilisée pour envoyer la requête. Lorsque vous exécutez la requête suivante à partir de votre machine virtuelle, la réponse est l’adresse IP publique utilisée pour cette machine virtuelle.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Empêchement de la connectivité publique

Parfois, il n’est pas souhaitable d’autoriser une machine virtuelle à créer un flux sortant, ou il peut y avoir une exigence de gestion les destinations pouvant être atteinte avec des flux sortants. Dans ce cas, vous utilisez des [groupes de sécurité réseau (NSG)](../virtual-network/virtual-networks-nsg.md) pour gérer les destinations que la machine virtuelle peut atteindre. Lorsque vous appliquez un groupe de sécurité réseau à une machine Virtuelle à charge équilibrée, vous devez faire attention aux [balises par défaut](../virtual-network/virtual-networks-nsg.md#default-tags) et aux [règles par défaut](../virtual-network/virtual-networks-nsg.md#default-rules).

Vous devez vous assurer que la machine virtuelle peut recevoir des demandes d’analyse d’intégrité d’Azure Load Balancer. Si un groupe de sécurité réseau bloque les demandes d’analyse d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre analyse de l’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme défaillante. L’équilibrage de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.



<!--HONumber=Nov16_HO3-->


