---
title: "Déployer des machines virtuelles Linux dans un réseau existant avec le portail Azure | Microsoft Docs"
description: "Déployez une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide du portail."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 964c0fc41773b50a9fbe476df47460484c2ada66
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# Procédure de déploiement d’une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide du portail Azure
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-portal" class="xliff"></a>

Cet article vous montre comment déployer une machine virtuelle dans un réseau virtuel existant (VNet). Les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau doivent être des ressources statiques et durables qui sont rarement déployées. Une fois un réseau virtuel déployé, il peut être réutilisé par des redéploiements constants sans impact négatif sur l’infrastructure. Si on considère un réseau virtuel comme un commutateur réseau matériel classique, il est évident qu’il serait inutile de configurer un tout nouveau commutateur matériel lors de chaque déploiement.  

Avec un réseau virtuel correctement configuré, vous pouvez continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

## Créer le groupe de ressources
<a id="create-the-resource-group" class="xliff"></a>

Tout d’abord, créez un groupe de ressources pour organiser tous les éléments créés dans cette procédure pas à pas. Pour plus d’informations sur les groupes de ressources Azure, consultez la [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

![createResourceGroup](./media/deploy-linux-vm-into-existing-vnet-using-portal/createResourceGroup.png)


## Créez le réseau virtuel.
<a id="create-the-vnet" class="xliff"></a>

Ensuite, créez un réseau virtuel dans lequel lancer les machines virtuelles. Le réseau virtuel contient un sous-réseau et est associé au groupe de sécurité réseau avec ce sous-réseau dans une étape ultérieure.

![createVNet](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNet.png)

## Ajouter une carte réseau virtuelle au réseau virtuel
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

Les cartes réseau virtuelles (VNic) sont importantes, car vous pouvez les connecter sur différentes machines virtuelles. Cette approche conserve la carte réseau virtuelle comme une ressource statique, tandis que les machines virtuelles peuvent être temporaires. Créez une carte réseau virtuelle et associez-la au sous-réseau créé à l’étape précédente.

![createVNic](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVNic.png)

## Créer le groupe de sécurité réseau
<a id="create-the-network-security-group" class="xliff"></a>

Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau. Pour plus d’informations sur les groupes de sécurité réseau Azure, consultez [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md).

![createNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/createNSG.png)

## Ajouter une règle d’autorisation SSH entrante
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

La machine virtuelle a besoin d’un accès à partir d’Internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle est donc créée.

![createInboundSSH](./media/deploy-linux-vm-into-existing-vnet-using-portal/createInboundSSH.png)

## Associer le groupe de sécurité réseau au sous-réseau
<a id="associate-the-nsg-with-the-subnet" class="xliff"></a>

Maintenant que le réseau virtuel et le sous-réseau ont été créés, associez le groupe de sécurité réseau au sous-réseau. Les groupes de sécurité réseau peuvent être associés à un sous-réseau entier ou à une carte réseau virtuelle individuelle. Le pare-feu filtrant le trafic au niveau du sous-réseau, toutes les cartes réseau et les machines virtuelles au sein du sous-réseau sont protégées par le groupe de sécurité réseau. L’autre approche consiste à associer le groupe de sécurité réseau à une seule carte réseau virtuelle pour ne protéger qu’une seule machine virtuelle.

![associateNSG](./media/deploy-linux-vm-into-existing-vnet-using-portal/associateNSG.png)


## Déployer la machine virtuelle dans le réseau virtuel et le groupe de sécurité réseau
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

À l’aide du portail Azure, la machine virtuelle Linux est déployée dans le groupe de ressources, le réseau virtuel, le sous-réseau et la carte réseau virtuelle Azure existants.

![createVM](./media/deploy-linux-vm-into-existing-vnet-using-portal/createVM.png)

En utilisant le portail pour choisir les ressources existantes, vous indiquez à Azure de déployer la machine virtuelle dans le réseau existant. Lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure.  

## Étapes suivantes
<a id="next-steps" class="xliff"></a>

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](../windows/cli-deploy-templates.md)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](create-cli-complete.md)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](create-ssh-secured-vm-from-template.md)

