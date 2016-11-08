---
title: Interfaces réseau | Microsoft Docs
description: En savoir plus sur les interfaces réseau Azure dans Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/23/2016
ms.author: jdial

---
# <a name="network-interfaces"></a>Interfaces réseau
Une carte d’interface réseau (NIC) est l’interconnexion entre une machine virtuelle et le réseau logiciel sous-jacent. Cet article explique ce qu’est une interface réseau et comment elle est utilisée dans le modèle de déploiement Azure Resource Manager.

Microsoft recommande de déployer de nouvelles ressources à l’aide du modèle de déploiement Resource Manager, mais vous pouvez également déployer des machines virtuelles avec une connectivité réseau dans le modèle de déploiement [classique](virtual-network-ip-addresses-overview-classic.md) . Si vous êtes familiarisé avec le modèle classique, il existe des différences importantes dans la mise en réseau de machines virtuelles par rapport au modèle de déploiement Resource Manager. Pour en savoir plus sur les différences, voir l’article [Mise en réseau de machine virtuelle - Classique](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

Dans Azure, une interface réseau :

1. est une ressource qui peut être créée ou supprimée, et qui possède ses propres paramètres configurables ;
2. doit être connectée à un sous-réseau dans un réseau virtuel Azure lors de sa création. Si vous n’êtes pas familiarisé avec les réseaux virtuels, pour en savoir plus à leur sujet, voir l’article [Présentation du réseau virtuel](virtual-networks-overview.md) . La carte d’interface réseau doit être connectée à un réseau virtuel existant dans les mêmes [emplacement](https://azure.microsoft.com/regions) et [abonnement](../azure-glossary-cloud-terminology.md#subscription) Azure que la carte d’interface réseau. Après la création d’une carte d’interface réseau, vous pouvez modifier le sous-réseau auquel elle est connectée, mais pas le réseau virtuel auquel elle est connectée.
3. A un nom affecté qui ne peut pas être modifié une fois la carte d’interface réseau créée. Le nom doit être unique dans un [groupe de ressources](../resource-group-overview.md#resource-groups)Azure, mais pas dans l’abonnement, l’emplacement Azure dans lequel il est créé, ou le réseau virtuel auquel il est connecté. Plusieurs cartes d’interface réseau sont généralement créées dans un abonnement Azure. Il est recommandé d’imaginer une convention d’affectation de noms qui rend la gestion de plusieurs cartes d’interface réseau plus simple que l’utilisation de noms par défaut. Pour des suggestions, voir l’article [Conventions d’affectation de noms recommandées pour les ressources Azure](../guidance/guidance-naming-conventions.md) .
4. Peut être attachée à une machine virtuelle unique existant dans le même emplacement que la carte d’interface réseau.
5. A une adresse MAC qui est conservée avec la carte d’interface réseau tant que celle-ci reste attachée à une machine virtuelle. L’adresse MAC est conservée si la machine virtuelle est redémarrée (à partir du système d’exploitation) ou arrêtée (désallouée) et démarrée à l’aide du portail Azure, d’Azure PowerShell ou de l’interface de ligne de commande Azure. Si la carte d’interface réseau est détachée d’une machine virtuelle, puis attachée à une autre, elle reçoit une adresse MAC différente. Si la carte d’interface réseau est supprimée, l’adresse MAC est affectée à une autre carte d’interface réseau.
6. Une seule adresse IP statique ou dynamique **IPv4** *privée* peut lui être attribuée.
7. Une seule ressource d’adresse IP publique peut lui être associée.
8. Prend en charge l’accélération réseau avec virtualisation d’E/S d’une racine unique (SR-IOV) pour des tailles de machine virtuelle spécifiques exécutant des versions spécifiques du système d’exploitation Microsoft Windows Server. Pour en savoir plus sur cette fonctionnalité EN VERSION PRÉLIMINAIRE, voir l’article [Accélération réseau pour une machine virtuelle](virtual-network-accelerated-networking-powershell.md) .
9. Peut recevoir du trafic non destiné à des adresses IP privées qui lui sont attribuées si le transfert IP est activé pour la carte d’interface réseau. Par exemple, si une machine virtuelle exécute un logiciel de pare-feu, elle route les paquets non destinés à ses propres adresses IP. La machine virtuelle doit toujours exécuter un logiciel capable de router ou transférer le trafic mais, pour ce faire, le transfert IP doit être activé pour une carte d’interface réseau.
10. Est souvent créée dans le même groupe de ressources que la machine virtuelle à laquelle elle est attachée, ou le même réseau virtuel que celui auquel elle est connectée, même si ce n’est pas obligatoire.

Plusieurs cartes d’interface réseau peuvent être attachées à une même machine virtuelle, pour autant que la taille de celle-ci le permette. Pour en savoir plus sur les tailles de machine virtuelle qui prennent en charge plusieurs cartes d’interface réseau, consultez les articles [Tailles des machines virtuelles Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à créer une machine virtuelle avec une seule carte d’interface réseau en lisant l’article [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Apprenez à créer une machine virtuelle avec plusieurs cartes d’interface réseau en lisant l’article [Créer une machine virtuelle avec plusieurs cartes d’interface réseau](virtual-network-deploy-multinic-arm-ps.md) .
* Découvrez comment créer une carte d’interface réseau avec plusieurs configurations IP en lisant l’article [Adresses IP multiples pour les machines virtuelles](virtual-network-multiple-ip-addresses-powershell.md) .

<!--HONumber=Oct16_HO2-->


