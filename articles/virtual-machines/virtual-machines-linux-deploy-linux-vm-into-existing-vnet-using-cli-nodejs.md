---
title: "Déployer des machines virtuelles Linux dans des réseaux existants - Azure CLI | Microsoft Docs"
description: "Déployez une machine virtuelle Linux dans un réseau virtuel existant à l’aide de l’interface CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: cce15fa7042b29de055a62b1b7a6efa4d0539acf
ms.openlocfilehash: fd73f2eae7f89c237a6a2ff75c6022cb0684e613


---

# <a name="deploy-a-linux-vm-into-an-existing-azure-virtual-network-using-the-cli"></a>Déployez une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide de l’interface de ligne de commande

Cet article explique comment utiliser les indicateurs de l’interface de ligne de commande pour déployer une machine virtuelle dans un réseau virtuel existant.  Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
- [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0 (version préliminaire)](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides

Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes nécessaires. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](virtual-machines-linux-deploy-linux-vm-into-existing-vnet-using-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#detailed-walkthrough).

Configuration requise : groupe de ressources, réseau virtuel, groupe de sécurité réseau avec SSH entrant, sous-réseau. Remplacez les exemples par vos propres paramètres.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Déployer la machine virtuelle dans l’infrastructure de réseau virtuel

```azurecli
azure vm create myVM \
-g myResourceGroup \
-l westus \
-y linux \
-Q Debian \
-o myStorageAcct \
-u myAdminUser \
-M ~/.ssh/id_rsa.pub \
-n myVM \
-F myVNet \
-j mySubnet \
-N myVNic
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Il est recommandé que les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau soient des ressources statiques et durables qui sont rarement déployées.  Une fois un réseau virtuel déployé, il peut être réutilisé par de nouveaux redéploiements, sans impact négatif sur l’infrastructure.  Voyez les réseaux virtuels comme étant des commutateurs réseau physiques traditionnels. Vous n’avez pas besoin de configurer un nouveau commutateur matériel avec chaque déploiement.  Avec un réseau virtuel correctement configuré, nous pouvons continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Tout d’abord, nous créons un groupe de ressources pour organiser tous les éléments créés dans cette procédure pas à pas.  Pour plus d’informations sur les groupes de ressources Azure, consultez l’article [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure group create myResourceGroup \
--location westus
```

## <a name="create-the-vnet"></a>Créez le réseau virtuel.

La première étape consiste à créer un réseau virtuel dans lequel lancer les machines virtuelles.  Le réseau virtuel contient un sous-réseau pour cette procédure pas à pas.  Pour plus d’informations sur les réseaux virtuels Azure, consultez la section [Créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure](../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network vnet create myVNet \
--resource-group myResourceGroup \
--address-prefixes 10.10.0.0/24 \
--location westus
```

## <a name="create-the-nsg"></a>Créer le groupe de sécurité réseau

Le sous-réseau se trouvant derrière un groupe de sécurité réseau existant, nous développons le groupe de sécurité en premier.  Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau.  Pour plus d’informations sur les groupes de sécurité réseau Azure, consultez la section [Comment créer des groupes de sécurité réseau dans l’interface de ligne de commande Azure](../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure network nsg create myNSG \
--resource-group myResourceGroup \
--location westus
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Ajouter une règle d’autorisation SSH entrante

La machine virtuelle Linux a besoin d’un accès à partir d’internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle Linux est donc créée.

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

## <a name="add-a-subnet-to-the-vnet"></a>Ajouter un sous-réseau au réseau virtuel

Les machines virtuelles d’un réseau virtuel doivent se trouver dans un sous-réseau.  Chaque réseau virtuel peut présenter plusieurs sous-réseaux.  Créez le sous-réseau et associez-le au groupe de sécurité réseau afin d’ajouter un pare-feu.

```azurecli
azure network vnet subnet create mySubNet \
--resource-group myResourceGroup \
--vnet-name myVNet \
--address-prefix 10.10.0.0/26 \
--network-security-group-name myNSG
```

Le sous-réseau est désormais ajouté au sein du réseau virtuel et associé au groupe de sécurité réseau et à la règle dédiée.


## <a name="add-a-vnic-to-the-subnet"></a>Ajouter une carte réseau virtuelle au réseau virtuel

Les cartes réseau virtuelles sont importantes, car vous pouvez les connecter à différentes machines virtuelles. La carte réseau virtuelle est ainsi conservée en tant que ressource statique, tandis que les machines virtuelles peuvent être temporaires.  Créez une carte réseau virtuelle et associez-la au sous-réseau créé à l’étape précédente.

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

## <a name="deploy-the-vm-into-the-vnet-and-nsg"></a>Déployer la machine virtuelle dans le réseau virtuel et le groupe de sécurité réseau

Nous disposons désormais d’un réseau virtuel, d’un sous-réseau intégré et d’un groupe de sécurité réseau jouant le rôle de pare-feu. Ce dernier protège notre sous-réseau en bloquant l’ensemble du trafic entrant, à l’exception du port 22 pour SSH.  La machine virtuelle peut désormais être déployée au sein de cette infrastructure réseau existante.

À l’aide de l’interface CLI Azure, et de la commande `azure vm create`, la machine virtuelle Linux est déployée dans le groupe de ressources Azure, le réseau virtuel, le sous-réseau et la carte réseau virtuelle Azure existants.  Pour plus d’informations sur l’utilisation de l’interface CLI pour déployer une machine virtuelle complète, consultez la section [Création d’un environnement Linux complet à l’aide de l’interface CLI Azure](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

```azurecli
azure vm create myVM \
--resource-group myResourceGroup \
--location westus \
--os-type linux \
--image-urn Debian \
--storage-account-name mystorageaccount \
--admin-username myAdminUser \
--ssh-publickey-file ~/.ssh/id_rsa.pub \
--vnet-name myVNet \
--vnet-subnet-name mySubnet \
--nic-name myVNic
```

En utilisant les indicateurs CLI pour appeler les ressources existantes, nous indiquons à Azure de déployer la machine virtuelle au sein du réseau existant.  Encore une fois, lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure.  

## <a name="next-steps"></a>Étapes suivantes

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Feb17_HO1-->


