---
title: "Créer une machine virtuelle Linux dans Azure avec plusieurs cartes réseau | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Linux dotée de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure ou de modèles Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 51edde1be97885b92fe48f65d3f477c73cd19338
ms.lasthandoff: 03/21/2017


---
# <a name="create-a-linux-vm-with-multiple-nics-using-the-azure-cli-10"></a>Créer une machine virtuelle Linux avec plusieurs cartes réseau à l’aide d’Azure CLI 1.0
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consisterait à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes d’interface réseau. Pour plus d’informations, notamment sur la création de plusieurs cartes réseau dans vos propres scripts Bash, consultez la page consacrée au [déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , pensez à dimensionner la vôtre en conséquence.

> [!WARNING]
> Vous devez attacher plusieurs cartes réseau quand vous créez une machine virtuelle ; vous ne pouvez pas ajouter de cartes réseau à une machine virtuelle existante. Vous pouvez [créer une machine virtuelle basée sur les disques virtuels d’origine](virtual-machines-linux-copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et créer plusieurs cartes réseau quand vous déployez la machine virtuelle.


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#create-supporting-resources) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0](virtual-machines-linux-multiple-nics.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="create-supporting-resources"></a>Créer des ressources de support
Veillez à ce que [l’interface de ligne de commande Azure](../cli-install-nodejs.md) soit connectée et utilise le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

Créez d’abord un groupe de ressources. L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS` :

```azurecli
azure group create myResourceGroup -l WestUS
```

Créez un compte de stockage qui contiendra vos machines virtuelles. L’exemple qui suit permet de créer un compte de stockage nommé `mystorageaccount` :

```azurecli
azure storage account create mystorageaccount -g myResourceGroup \
    -l WestUS --kind Storage --sku-name PLRS
```

Créez un réseau virtuel auquel connecter vos machines virtuelles. L’exemple suivant crée un réseau virtuel nommé `myVnet` avec un préfixe d’adresse de `192.168.0.0/16` :

```azurecli
azure network vnet create -g myResourceGroup -l WestUS \
    -n myVnet -a 192.168.0.0/16
```

Créez deux sous-réseaux de réseau virtuel : l’un pour le trafic frontal, l’autre pour le trafic principal. L’exemple suivant crée deux sous-réseaux, nommés `mySubnetFrontEnd` et `mySubnetBackEnd` :

```azurecli
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetFrontEnd -a 192.168.1.0/24
azure network vnet subnet create -g myResourceGroup -e myVnet \
    -n mySubnetBackEnd -a 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Créer et configurer plusieurs cartes réseau
Vous trouverez plus d’informations sur le [déploiement de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure](../virtual-network/virtual-network-deploy-multinic-arm-cli.md), notamment les scripts pour le processus de bouclage pour créer toutes les cartes réseau.

L’exemple suivant crée deux cartes réseau, nommées `myNic1` et `myNic2`, avec une carte réseau se connectant à chaque sous-réseau :

```azurecli
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic1 --subnet-vnet-name myVnet --subnet-name mySubnetFrontEnd
azure network nic create --resource-group myResourceGroup --location WestUS \
    -n myNic2 --subnet-vnet-name myVnet --subnet-name mySubnetBackEnd
```

En général, vous devez également créer un [groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
azure network nsg create --resource-group myResourceGroup --location WestUS \
    --name myNetworkSecurityGroup
```

Liez vos cartes réseau au groupe de sécurité réseau à l’aide de `azure network nic set`. L’exemple suivant lie `myNic1` et `myNic2` avec `myNetworkSecurityGroup` :

```azurecli
azure network nic set --resource-group myResourceGroup --name myNic1 \
    --network-security-group-name myNetworkSecurityGroup
azure network nic set --resource-group myResourceGroup --name myNic2 \
    --network-security-group-name myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Créer une machine virtuelle et attacher les cartes réseau
Lorsque vous créez la machine virtuelle, vous spécifiez maintenant plusieurs cartes réseau. Au lieu d’utiliser `--nic-name` pour fournir une seule carte réseau, vous utilisez `--nic-names` et spécifiez une liste de cartes réseau séparée par des virgules. Vous devez également faire attention en définissant la taille de la machine virtuelle. Il existe des limites pour le nombre maximal de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles des machines virtuelles Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). L’exemple suivant montre comment spécifier plusieurs cartes réseau, puis sur une taille de machine virtuelle permettant l’utilisation de plusieurs cartes réseau (`Standard_DS2_v2`) :

```azurecli
azure vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location WestUS \
    --os-type linux \
    --nic-names myNic1,myNic2 \
    --vm-size Standard_DS2_v2 \
    --storage-account-name mystorageaccount \
    --image-urn UbuntuLTS \
    --admin-username azureuser \
    --ssh-publickey-file ~/.ssh/id_rsa.pub
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Créer plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Grâce aux modèles Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Utilisez *copy* pour spécifier le nombre d’instances à créer :

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../azure-resource-manager/resource-group-create-multiple.md). 

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource permettant de créer `myNic1`, `myNic2`, etc. Voici un exemple d’ajout de la valeur d’index :

```json
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Veillez à consulter les [tailles des machines virtuelles Linux](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle. 

N’oubliez pas que vous ne pouvez pas ajouter de cartes réseau à une machine virtuelle existante. Vous devez créer toutes les cartes réseau quand vous déployez la machine virtuelle. Quand vous planifiez vos déploiements, vérifiez que vous disposez de toute la connectivité réseau nécessaire dès le départ.


