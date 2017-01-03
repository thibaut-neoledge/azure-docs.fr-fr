---
title: "Déployer une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide du portail | Microsoft Docs"
description: "Déployez une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide du portail."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 6b285b67c3d5bcc89be4d371e444796501eb7de2
ms.openlocfilehash: 7c07a99f9a72cb69812191ca042fac905d2e3706


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-portal"></a>Déployer une machine virtuelle Linux dans un réseau virtuel et un groupe de sécurité réseau existants à l’aide du portail

Cet article explique comment déployer une machine virtuelle dans un réseau virtuel existant.  Il est recommandé que les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau soient des ressources statiques et durables qui sont rarement déployées.  Une fois un réseau virtuel déployé, il peut être réutilisé par des redéploiements constants sans impact négatif sur l’infrastructure.  Si on considère un réseau virtuel comme un commutateur réseau matériel classique, il est évident qu’il serait inutile de configurer un tout nouveau commutateur matériel lors de chaque déploiement.  

Avec un réseau virtuel correctement configuré, nous pouvons continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Tout d’abord, nous déployons un groupe de ressources pour organiser tous les éléments créés dans cette procédure pas à pas.  Pour plus d’informations sur les groupes de ressources Azure, consultez l’article [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

![createResourceGroup](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## <a name="create-the-vnet"></a>Créer le réseau virtuel

La première étape consiste à créer un réseau virtuel dans lequel lancer les machines virtuelles.  Le réseau virtuel contient un sous-réseau et nous associerons le groupe de sécurité réseau à ce sous-réseau à une étape ultérieure.

![createVNet](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## <a name="add-a-vnic-to-the-subnet"></a>Ajouter une carte réseau virtuelle au sous-réseau

Les cartes réseau virtuelles sont importantes, car vous pouvez les connecter à différentes machines virtuelles. La carte réseau virtuelle est ainsi conservée en tant que ressource statique, tandis que les machines virtuelles peuvent être temporaires. Créez une carte réseau virtuelle et associez-la au sous-réseau créé à l’étape précédente.

![createVNic](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## <a name="create-the-nsg"></a>Créer le groupe de sécurité réseau

Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau. Pour plus d’informations sur les groupes de sécurité réseau, voir [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

![createNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## <a name="add-an-inbound-ssh-allow-rule"></a>Ajouter une règle d’autorisation SSH entrante

La machine virtuelle Linux a besoin d’un accès à partir d’internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle Linux est donc créée.

![createInboundSSH](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## <a name="associate-the-nsg-with-the-subnet"></a>Associer le groupe de sécurité réseau au sous-réseau

Maintenant le réseau virtuel et le sous-réseau créés, nous associons le groupe de sécurité réseau au sous-réseau.  Les groupes de sécurité réseau peuvent être associés à un sous-réseau entier ou à une carte réseau virtuelle individuelle.  Avec le filtrage par le pare-feu du trafic au niveau du sous-réseau, l’ensemble des cartes réseau virtuelles et des machines virtuelles au sein du sous-réseau sont protégées par le groupe de sécurité réseau, tandis que lorsque le groupe de sécurité réseau est associé à une seule carte réseau virtuelle, il protège une seule machine virtuelle.

![associateNSG](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Déployer la machine virtuelle dans le réseau virtuel et le groupe de sécurité réseau

À l’aide du portail Azure, la machine virtuelle Linux est déployée dans le groupe de ressources, le réseau virtuel, le sous-réseau et la carte réseau virtuelle Azure existants.

![createVM](../media/virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

En utilisant le portail pour choisir les ressources existantes, nous indiquons à Azure de déployer la machine virtuelle dans le réseau existant.  Encore une fois, lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure.  

## <a name="next-steps"></a>Étapes suivantes

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Jan17_HO1-->


