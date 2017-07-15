---
title: "Déployer des machines virtuelles Linux dans un réseau existant avec l’interface Azure CLI 1.0 | Microsoft Docs"
description: "Procédure de déploiement d’une machine virtuelle Linux dans un réseau virtuel existant à l’aide de l’interface Azure CLI 1.0"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 767a3f7cadba6b1e71e5a8f5995a9db090e419dd
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---

# Procédure de déploiement d’une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide de l’interface Azure CLI 1.0
<a id="how-to-deploy-a-linux-virtual-machine-into-an-existing-azure-virtual-network-with-the-azure-cli-10" class="xliff"></a>

Cet article vous montre comment utiliser l’interface Azure CLI 1.0 pour déployer une machine virtuelle dans un réseau virtuel existant (VNet). Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
- [des fichiers de clés SSH publiques et privées](mac-create-ssh-keys.md)


## Versions de l’interface de ligne de commande permettant d’effectuer la tâche
<a id="cli-versions-to-complete-the-task" class="xliff"></a>
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](deploy-linux-vm-into-existing-vnet-using-cli.md) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager


## Commandes rapides
<a id="quick-commands" class="xliff"></a>

Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes nécessaires. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](deploy-linux-vm-into-existing-vnet-using-cli.md#detailed-walkthrough).

Configuration requise : groupe de ressources, réseau virtuel, groupe de sécurité réseau avec SSH entrant, sous-réseau. Remplacez les exemples par vos propres paramètres.

### Déployer la machine virtuelle dans l’infrastructure de réseau virtuel
<a id="deploy-the-vm-into-the-virtual-network-infrastructure" class="xliff"></a>

```azurecli
azure vm create myVM \
    -g myResourceGroup \
    -l eastus \
    -y linux \
    -Q Debian \
    -o mystorageaccount \
    -u myAdminUser \
    -M ~/.ssh/id_rsa.pub \
    -n myVM \
    -F myVNet \
    -j mySubnet \
    -N myVNic
```

## Procédure pas à pas
<a id="detailed-walkthrough" class="xliff"></a>

Les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau doivent être des ressources statiques et durables qui sont rarement déployées. Une fois un réseau virtuel déployé, il peut être réutilisé par de nouveaux redéploiements, sans impact négatif sur l’infrastructure. Voyez les réseaux virtuels comme étant des commutateurs réseau physiques traditionnels. Vous n’avez pas besoin de configurer un nouveau commutateur matériel avec chaque déploiement. Avec un réseau virtuel correctement configuré, vous pouvez continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

## Créer le groupe de ressources
<a id="create-the-resource-group" class="xliff"></a>

Tout d’abord, créez un groupe de ressources pour organiser tous les éléments créés dans cette procédure pas à pas. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)

```azurecli
azure group create myResourceGroup --location eastus
```

## Créez le réseau virtuel.
<a id="create-the-vnet" class="xliff"></a>

La première étape consiste à créer un réseau virtuel dans lequel lancer les machines virtuelles. Le réseau virtuel contient un sous-réseau pour cette procédure pas à pas. Pour plus d’informations sur les réseaux virtuels Azure, consultez la section [Créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md).

```azurecli
azure network vnet create myVNet \
    --resource-group myResourceGroup \
    --address-prefixes 10.10.0.0/24 \
    --location eastus
```

## Créer le groupe de sécurité réseau
<a id="create-the-network-security-group" class="xliff"></a>

Le sous-réseau se trouvant derrière un groupe de sécurité réseau existant, vous devez développer le groupe de sécurité en premier. Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau. Pour plus d’informations sur les groupes de sécurité réseau Azure, consultez [Guide de création des groupes de sécurité réseau dans l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md)

```azurecli
azure network nsg create myNetworkSecurityGroup \
    --resource-group myResourceGroup \
    --location eastus
```

## Ajouter une règle d’autorisation SSH entrante
<a id="add-an-inbound-ssh-allow-rule" class="xliff"></a>

La machine virtuelle a besoin d’un accès à partir d’Internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle est donc nécessaire.

```azurecli
azure network nsg rule create inboundSSH \
    --resource-group myResourceGroup \
    --nsg-name myNSG \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range 22 \
    --destination-address-prefix 10.10.0.0/24 \
    --destination-port-range 22
```

## Ajouter un sous-réseau au réseau virtuel
<a id="add-a-subnet-to-the-vnet" class="xliff"></a>

Les machines virtuelles d’un réseau virtuel doivent se trouver dans un sous-réseau. Chaque réseau virtuel peut présenter plusieurs sous-réseaux. Créez le sous-réseau et associez-le au groupe de sécurité réseau.

```azurecli
azure network vnet subnet create mySubNet \
    --resource-group myResourceGroup \
    --vnet-name myVNet \
    --address-prefix 10.10.0.0/26 \
    --network-security-group-name myNetworkSecurityGroup
```

Le sous-réseau est désormais ajouté au sein du réseau virtuel et associé au groupe de sécurité réseau et à la règle dédiée.


## Ajouter une carte réseau virtuelle au réseau virtuel
<a id="add-a-vnic-to-the-subnet" class="xliff"></a>

Les cartes réseau virtuelles (VNic) sont importantes, car vous pouvez les réutiliser en les connectant sur différentes machines virtuelles. Cette approche conserve la carte réseau virtuelle comme une ressource statique, tandis que les machines virtuelles peuvent être temporaires. Créez une carte réseau virtuelle et associez-la au sous-réseau créé à l’étape précédente.

```azurecli
azure network nic create myVNic \
    --resource-group myResourceGroup \
    --location eastus \
    ---subnet-vnet-name myVNet \
    --subnet-name mySubNet
```

## Déployer la machine virtuelle dans le réseau virtuel et le groupe de sécurité réseau
<a id="deploy-the-vm-into-the-vnet-and-nsg" class="xliff"></a>

Vous disposez désormais d’un réseau virtuel et d’un sous-réseau au sein de celui-ci, ainsi que d’un groupe de sécurité réseau qui protège le sous-réseau en bloquant l’ensemble du trafic entrant, à l’exception du port 22 pour SSH. La machine virtuelle peut désormais être déployée au sein de cette infrastructure réseau existante.

À l’aide de l’interface CLI Azure, et de la commande `azure vm create`, la machine virtuelle Linux est déployée dans le groupe de ressources Azure, le réseau virtuel, le sous-réseau et la carte réseau virtuelle Azure existants. Pour plus d’informations sur l’utilisation de l’interface CLI pour déployer une machine virtuelle complète, consultez la section [Création d’un environnement Linux complet à l’aide de l’interface CLI Azure](create-cli-complete.md).

```azurecli
azure vm create myVM \
    --resource-group myResourceGroup \
    --location eastus \
    --os-type linux \
    --image-urn Debian \
    --storage-account-name mystorageaccount \
    --admin-username myAdminUser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --vnet-name myVNet \
    --vnet-subnet-name mySubnet \
    --nic-name myVNic
```

En utilisant les indicateurs CLI pour appeler les ressources existantes, vous indiquez à Azure de déployer la machine virtuelle au sein du réseau existant. Lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure.  

## Étapes suivantes
<a id="next-steps" class="xliff"></a>

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](../windows/cli-deploy-templates.md)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](create-cli-complete.md)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](create-ssh-secured-vm-from-template.md)

