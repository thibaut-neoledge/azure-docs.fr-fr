---
title: "Déploiement d’une machine virtuelle Linux dans un réseau existant avec l’interface Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment déployer une machine virtuelle Linux dans un réseau virtuel existant à l’aide de l’interface Azure CLI 2.0"
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
ms.devlang: azurecli
ms.topic: article
ms.date: 01/31/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a43e740bde8d91a47b84787e4bf72e4667b84de6
ms.lasthandoff: 04/03/2017


---

# <a name="deploy-a-linux-vm-into-an-existing-virtual-network"></a>Déployer une machine virtuelle Linux dans un réseau virtuel existant

Cet article vous montre comment utiliser l’interface Azure CLI 2.0 pour déployer une machine virtuelle dans un réseau virtuel existant. Les conditions requises sont :

- [un compte Azure](https://azure.microsoft.com/pricing/free-trial/)
- [des fichiers de clés SSH publiques et privées](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Vous pouvez également effectuer ces étapes à l’aide [d’Azure CLI 1.0](deploy-linux-vm-into-existing-vnet-using-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="quick-commands"></a>Commandes rapides
Si vous avez besoin d’accomplir rapidement cette tâche, la section suivante décrit les commandes nécessaires. Pour obtenir plus d’informations et davantage de contexte pour chaque étape, lisez la suite de ce document, [à partir de cette section](#detailed-walkthrough).

Pour créer cet environnement personnalisé, la dernière version de l’interface [Azure CLI 2.0](/cli/azure/install-az-cli2) doit être installée et connectée à un compte Azure à l’aide de la commande [az login](/cli/azure/#login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `myVnet` et `myVM`.

**Conditions préalables :** groupe de ressources Azure, réseau virtuel et sous-réseau, groupe de sécurité réseau avec SSH entrant et une carte d’interface réseau virtuelle.

### <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Déployer la machine virtuelle dans l’infrastructure de réseau virtuel

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

Il est recommandé que les ressources Azure telles que les réseaux virtuels et les groupes de sécurité réseau soient des ressources statiques et durables qui sont rarement déployées. Une fois un réseau virtuel déployé, il peut être réutilisé par de nouveaux redéploiements, sans impact négatif sur l’infrastructure. Si on considère un réseau virtuel comme un commutateur réseau matériel classique, il est évident qu’il serait inutile de configurer un tout nouveau commutateur matériel lors de chaque déploiement. Avec un réseau virtuel correctement configuré, nous pouvons continuer à déployer de nouveaux serveurs dans ce réseau virtuel encore et encore avec peu de modifications requises, voire aucune, tout au long de la durée de vie du réseau virtuel.

Pour créer cet environnement personnalisé, la dernière version de l’interface [Azure CLI 2.0](/cli/azure/install-az-cli2) doit être installée et connectée à un compte Azure à l’aide de la commande [az login](/cli/azure/#login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `myVnet` et `myVM`.

## <a name="create-the-resource-group"></a>Créer le groupe de ressources

Tout d’abord, nous créons un groupe de ressources Azure pour organiser tous les éléments créés dans cette procédure pas à pas. Pour plus d’informations sur les groupes de ressources, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Créez le groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `westus` :

```azurecli
az group create \
    --name myResourceGroup \
    --location westus
```

## <a name="create-the-virtual-network"></a>Créer un réseau virtuel

Permet de créer un réseau virtuel Azure dans lequel lancer les machines virtuelles. Pour plus d’informations sur les réseaux virtuels Azure, consultez la section [Créer un réseau virtuel à l’aide de l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-vnet-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Créez le réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#create). L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` et un sous-réseau nommé `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --location westus \
    --name myVnet \
    --address-prefix 10.10.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.10.1.0/24
```

## <a name="create-the-network-security-group"></a>Créer le groupe de sécurité réseau

Les groupes de sécurité réseau Azure s’apparentent à un pare-feu au niveau de la couche réseau. Pour plus d’informations sur les groupes de sécurité réseau, voir [Guide de création des groupes de sécurité réseau dans l’interface de ligne de commande Azure](../../virtual-network/virtual-networks-create-nsg-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Créez le groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#create). L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-ssh-allow-rule"></a>Ajouter une règle d’autorisation SSH entrante

La machine virtuelle Linux a besoin d’un accès à partir d’internet. Une règle autorisant le trafic entrant sur le port 22 à être acheminé à travers le réseau jusqu’au port 22 de la machine virtuelle Linux est donc créée. Ajoutez une règle de trafic entrant pour le groupe de sécurité réseau avec la commande [az network nsg rule create](/cli/azure/network/nsg/rule#create). L’exemple suivant permet de créer une règle nommée `myNetworkSecurityGroupRuleSSH` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="attach-the-subnet-to-the-network-security-group"></a>Joindre le sous-réseau au groupe de sécurité réseau

Les règles des groupes de sécurité réseau peuvent être appliquées à un sous-réseau ou à une interface réseau spécifique. Permet de joindre le groupe de sécurité réseau à notre sous-réseau. Joignez votre sous-réseau au groupe de sécurité réseau avec [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="add-a-virtual-network-interface-card-to-the-subnet"></a>Ajout d’une carte réseau virtuelle au sous-réseau

Les cartes réseau virtuelles (VNic) sont importantes, car vous pouvez les réutiliser en les connectant sur différentes machines virtuelles. Cette réutilisation vous permet de conserver la carte réseau virtuelle comme une ressource statique, tandis que les machines virtuelles peuvent être temporaires. Créez une carte réseau virtuelle et associez-la au sous-réseau avec [az network nic create](/cli/azure/network/nic#create). L’exemple suivant permet de créer une carte réseau virtuelle nommée `myNic` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --location westus \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>Déployer la machine virtuelle dans l’infrastructure de réseau virtuel

Nous disposons désormais d’un réseau virtuel, d’un sous-réseau intégré au réseau virtuel et d’un groupe de sécurité réseau jouant le rôle de pare-feu. Ce dernier protège notre sous-réseau en bloquant l’ensemble du trafic entrant, à l’exception du port 22 pour SSH. La machine virtuelle peut désormais être déployée au sein de cette infrastructure réseau existante.

Créez votre machine virtuelle avec [az vm create](/cli/azure/vm#create). Pour plus d’informations sur l’utilisation des indicateurs avec l’interface Azure 2.0 pour déployer une machine virtuelle complète, consultez la section [Créer un environnement Linux complet à l’aide de l’interface Azure CLI 2.0 (version préliminaire)](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

L’exemple qui suit permet de créer une machine virtuelle qui utilise Azure Managed Disks. Ces disques sont gérés par la plateforme Azure et ne nécessitent pas de préparation ou d’emplacement pour les stocker. Pour plus d’informations sur les disques gérés, consultez [Vue d’ensemble d’Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Si vous souhaitez utiliser des disques non gérés, consultez la remarque supplémentaire ci-dessous.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Debian \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic
```

Si vous utilisez des disques gérés, ignorez cette étape. Si vous voulez utiliser des disques non gérés, vous devez ajouter les paramètres supplémentaires suivants à la commande de traitement pour créer les disques non gérés dans le compte de stockage nommé `mystorageaccount` : 

```azurecli
    --use-unmanaged-disk \
    --storage-account mystorageaccount
```

En utilisant les indicateurs CLI pour appeler les ressources existantes, nous indiquons à Azure de déployer la machine virtuelle au sein du réseau existant. Encore une fois, lorsqu’un réseau virtuel et un sous-réseau ont été déployés, ils peuvent être conservés en tant que ressources statiques ou permanentes à l’intérieur de votre région Azure. Dans cet exemple, nous n’avons pas créé ou affecté une adresse IP publique à la carte réseau virtuelle, cette machine virtuelle n’est donc pas accessible publiquement sur Internet. Pour plus d’informations, consultez [Créer une machine virtuelle avec une adresse IP publique statique à l’aide de l’interface de ligne de commande Azure](../../virtual-network/virtual-network-deploy-static-pip-arm-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les façons de créer des machines virtuelles dans Azure, consultez les ressources suivantes :

* [Déploiement et gestion de machines virtuelles à l’aide des modèles Azure Resource Manager et de l’interface de ligne de commande Azure](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer votre propre environnement personnalisé pour une machine virtuelle Linux à l’aide des commandes de l’interface de ligne de commande Azure directement](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Création d’une machine virtuelle Linux sur Azure à l’aide de modèles](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

