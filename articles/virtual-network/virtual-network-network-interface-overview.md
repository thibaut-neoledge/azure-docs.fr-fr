---
title: "Interfaces réseau dans Azure | Microsoft Docs"
description: "Découvrez plus d’informations sur les interfaces réseau Azure et la façon dont elles sont utilisées avec les machines virtuelles."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f58b503f-18bf-4377-aa63-22fc8a96e4be
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 395cff80b3f97b6340e15f370c13f783e2f5dde3
ms.lasthandoff: 02/28/2017


---
# <a name="what-are-network-interfaces"></a>Que sont les interfaces réseau ?

Une carte d’interface réseau (NIC) est l’interconnexion entre une machine virtuelle et le réseau logiciel sous-jacent. Cet article explique ce qu’est une interface réseau et comment elle est utilisée dans le modèle de déploiement Azure Resource Manager.

Microsoft recommande de déployer de nouvelles ressources à l’aide du modèle de déploiement Resource Manager, mais vous pouvez également déployer des machines virtuelles avec une connectivité réseau dans le modèle de déploiement [classique](virtual-network-ip-addresses-overview-classic.md) . Si vous êtes familiarisé avec le modèle classique, il existe des différences importantes dans la mise en réseau de machines virtuelles par rapport au modèle de déploiement Resource Manager. Pour en savoir plus sur les différences, voir l’article [Mise en réseau de machine virtuelle - Classique](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

Dans Azure, une interface réseau :

1. est une ressource qui peut être créée ou supprimée, et qui possède ses propres paramètres configurables ;
2. doit être connectée à un sous-réseau dans un réseau virtuel Azure lors de sa création. Si vous n’êtes pas familiarisé avec les réseaux virtuels, pour en savoir plus à leur sujet, voir l’article [Présentation du réseau virtuel](virtual-networks-overview.md) . La carte d’interface réseau doit être connectée à un réseau virtuel existant dans les mêmes [emplacement](https://azure.microsoft.com/regions) et [abonnement](../azure-glossary-cloud-terminology.md#subscription) Azure que la carte d’interface réseau. Après la création d’une carte d’interface réseau, vous pouvez modifier le sous-réseau auquel elle est connectée, mais pas le réseau virtuel auquel elle est connectée.
3. A un nom affecté qui ne peut pas être modifié une fois la carte d’interface réseau créée. Le nom doit être unique dans un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups)Azure, mais pas dans l’abonnement, l’emplacement Azure dans lequel il est créé, ou le réseau virtuel auquel il est connecté. Plusieurs cartes d’interface réseau sont généralement créées dans un abonnement Azure. Il est recommandé d’imaginer une convention d’affectation de noms qui rend la gestion de plusieurs cartes d’interface réseau plus simple que l’utilisation de noms par défaut. Pour des suggestions, voir l’article [Conventions d’affectation de noms recommandées pour les ressources Azure](../guidance/guidance-naming-conventions.md) .
4. Peut être attachée à une machine virtuelle unique existant dans le même emplacement que la carte d’interface réseau.
5. A une adresse MAC qui est conservée avec la carte d’interface réseau tant que celle-ci reste attachée à une machine virtuelle. L’adresse MAC est conservée si la machine virtuelle est redémarrée (à partir du système d’exploitation) ou arrêtée (désallouée) et démarrée à l’aide du portail Azure, d’Azure PowerShell ou de l’interface de ligne de commande Azure. Si la carte d’interface réseau est détachée d’une machine virtuelle, puis attachée à une autre, elle reçoit une adresse MAC différente. Si la carte d’interface réseau est supprimée, l’adresse MAC est affectée à une autre carte d’interface réseau.
6. Une seule adresse IP statique ou dynamique **IPv4** *privée* peut lui être attribuée.
7. Peut être associé à une ou plusieurs ressources d’adresse IP publique. Lisez la documentation [Plusieurs adresses IP par carte réseau](virtual-network-multiple-ip-addresses-portal.md) pour plus d’informations.
8. Prend en charge l’accélération réseau avec virtualisation d’E/S d’une racine unique (SR-IOV) pour des tailles de machine virtuelle spécifiques exécutant des versions spécifiques du système d’exploitation Microsoft Windows Server. Pour en savoir plus sur cette fonctionnalité EN VERSION PRÉLIMINAIRE, voir l’article [Accélération réseau pour une machine virtuelle](virtual-network-accelerated-networking-powershell.md) .
9. Peut recevoir du trafic non destiné à des adresses IP privées qui lui sont attribuées si le transfert IP est activé pour la carte d’interface réseau. Par exemple, si une machine virtuelle exécute un logiciel de pare-feu, elle route les paquets non destinés à ses propres adresses IP. La machine virtuelle doit toujours exécuter un logiciel capable de router ou transférer le trafic mais, pour ce faire, le transfert IP doit être activé pour une carte d’interface réseau.
10. Est souvent créée dans le même groupe de ressources que la machine virtuelle à laquelle elle est attachée, ou le même réseau virtuel que celui auquel elle est connectée, même si ce n’est pas obligatoire.

## <a name="vms-with-multiple-network-interfaces"></a>Machines virtuelles avec plusieurs interfaces réseau
Il est possible d’associer plusieurs cartes d’interface réseau à une machine virtuelle, mais dans ce cas, vous devez garder à l’esprit les points suivants :  

* La taille de la machine virtuelle doit prendre en charge plusieurs cartes d’interface réseau. Pour en savoir plus sur les tailles de machine virtuelle qui prennent en charge plusieurs cartes d’interface réseau, consultez les articles [Tailles des machines virtuelles Windows Server](../virtual-machines/virtual-machines-windows-sizes.md) ou [Tailles des machines virtuelles Linux](../virtual-machines/virtual-machines-linux-sizes.md).
* La machine virtuelle doit être créée avec au moins deux cartes d’interface réseau. Si la machine virtuelle est créée avec une seule carte d’interface réseau (même si elle en prend plusieurs en charge), vous ne pouvez pas lui associer des cartes d’interface réseau supplémentaires après sa création. Dans la mesure où la machine virtuelle a été créée avec au moins deux cartes d’interface réseau, vous pouvez associer des cartes d’interface réseau supplémentaires à la machine virtuelle après sa création, tant que la taille de la machine virtuelle prend en charge plus de deux cartes d’interface réseau.  
* Vous pouvez détacher des cartes d’interface réseau secondaires (la carte principale ne peut pas être détachée) d’une machine virtuelle si cette dernière a au moins trois cartes d’interface réseau qui lui sont associées. Vous ne pouvez pas détacher des cartes d’interface réseau si la machine virtuelle a au plus deux cartes qui lui sont associées.  
* L’ordre des NIC à l’intérieur de la machine virtuelle est aléatoire et peut changer lors des mises à jour de l’infrastructure Azure. Toutefois, les adresses IP et les adresses MAC Ethernet correspondantes restent identiques. Par exemple, supposons que le système d’exploitation identifie NIC1 Azure comme Eth1. Eth1 a l’adresse IP 10.1.0.100 et l’adresse MAC 00-0D-3A-B0-39-0D. Une fois l’infrastructure Azure mise à jour et redémarrée, le système d’exploitation peut identifier NIC1 Azure comme Eth2, mais les adresses IP et MAC restent identiques à celles enregistrées lorsque le système d’exploitation identifiait NIC1 Azure comme Eth1. Lorsqu’un redémarrage est effectué par le client, l’ordre des NIC reste identique au sein du système d’exploitation.  
* Si la machine virtuelle est membre d’un [groupe à haute disponibilité](../azure-glossary-cloud-terminology.md#availability-set), toutes les machines virtuelles comportent une seule ou plusieurs cartes d’interface réseau. Si les machines virtuelles ont plusieurs cartes d’interface réseau, le nombre détenu par chacune n’a pas à être identique, dans la mesure où chaque machine virtuelle a au moins deux cartes d’interface réseau.

## <a name="next-steps"></a>Étapes suivantes
* Apprenez à créer une machine virtuelle avec une seule carte d’interface réseau en lisant l’article [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
* Apprenez à créer une machine virtuelle avec plusieurs cartes d’interface réseau en lisant l’article [Créer une machine virtuelle avec plusieurs cartes d’interface réseau](virtual-network-deploy-multinic-arm-ps.md) .
* Découvrez comment créer une carte d’interface réseau avec plusieurs configurations IP en lisant l’article [Adresses IP multiples pour les machines virtuelles](virtual-network-multiple-ip-addresses-powershell.md) .


