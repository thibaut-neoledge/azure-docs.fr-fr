---
title: "Déployer une machine virtuelle Linux dans un réseau virtuel Azure existant à l’aide de l’interface CLI | Microsoft Docs"
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
ms.date: 11/30/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2fad20978f40150ef9f1cb44054da2ba66848bda
ms.openlocfilehash: 613ce9b27bc26643b2f46c490d7f550b370df998


---

# <a name="deploy-a-linux-vm-into-an-existing-vnet--nsg-using-the-cli"></a>Déployer une machine virtuelle Linux dans un réseau virtuel et un groupe de sécurité réseau existants à l’aide de l’interface CLI

Cet article vous explique comment utiliser les indicateurs de l’interface CLI pour déployer une machine virtuelle dans un réseau virtuel existant, sécurisé à l’aide d’un groupe de sécurité réseau existant.  Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)

- [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quick-commands"></a>Commandes rapides

Remplacez les exemples par vos propres paramètres.

### <a name="create-the-resource-group"></a>Créer le groupe de ressources

```azurecli
azure group create myResourceGroup \
-l westus
```

### <a name="create-the-vnet"></a>Créez le réseau virtuel.

```azurecli
azure network vnet create myVNet \
-g myResourceGroup \
-a 10.10.0.0/24 \
-l westus
```

### <a name="create-the-nsg"></a>Créer le groupe de sécurité réseau

```azurecli
azure network nsg create myNSG \
-g myResourceGroup \
-l westus
```

### <a name="add-an-inbound-ssh-allow-rule"></a>Ajouter une règle d’autorisation SSH entrante

```azurecli
azure network nsg rule create inboundSSH \
-g myResourceGroup \
-a myNSG \
-c Allow \
-p Tcp \
-r Inbound \
-y 100 \
-f Internet \
-o 22 \
-e 10.10.0.0/24 \
-u 22
```

### <a name="add-a-subnet-to-the-vnet"></a>Ajouter un sous-réseau au réseau virtuel

```azurecli
azure network vnet subnet create mySubNet \
-g myResourceGroup \
-e myVNet \
-a 10.10.0.0/26 \
-o myNSG
```

### <a name="add-a-vnic-to-the-subnet"></a>Ajouter une carte réseau virtuelle au réseau virtuel

```azurecli
azure network nic create myVNic \
-g myResourceGroup \
-l westus \
-m myVNet \
-k mySubNet
```

### <a name="deploy-the-vm-into-the-vnet-nsg-and-connect-the-vnic"></a>Déployer la machine virtuelle dans le réseau virtuel, le groupe de sécurité réseau et connecter la carte réseau virtuelle

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

Il est recommandé que les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau soient des ressources statiques et durables qui sont rarement déployées.  Une fois un réseau virtuel déployé, il peut être réutilisé par de nouveaux redéploiements, sans impact négatif sur l’infrastructure.  Si on considère un réseau virtuel comme un commutateur réseau matériel classique, il est évident qu’il serait inutile de configurer un tout nouveau commutateur matériel lors de chaque déploiement.  Avec un réseau virtuel correctement configuré, nous pouvons continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

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



<!--HONumber=Nov16_HO5-->


