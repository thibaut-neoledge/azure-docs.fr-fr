---
title: "Créer une machine virtuelle Linux avec plusieurs cartes réseau à l’aide d’Azure CLI 2.0 | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Linux dotée de plusieurs cartes réseau avec Azure CLI 2.0 ou des modèles Resource Manager."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/10/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: 94bcd9543f231b11930962a0dc86a8ead13ad968
ms.lasthandoff: 04/14/2017


---
# <a name="create-a-linux-vm-with-multiple-nics"></a>Créer une machine virtuelle Linux avec plusieurs cartes réseau
Vous pouvez créer une machine virtuelle dans Azure, à laquelle sont attachées plusieurs interfaces réseau virtuelles (NIC). Un scénario courant consisterait à avoir des sous-réseaux différents pour les connectivités frontale et principale, ou un réseau dédié à une solution de surveillance ou de sauvegarde. Cet article fournit des commandes rapides pour créer une machine virtuelle avec plusieurs cartes d’interface réseau. Pour plus d’informations, notamment sur la création de plusieurs cartes réseau dans vos propres scripts Bash, consultez la page consacrée au [déploiement de machines virtuelles avec plusieurs cartes d’interface réseau](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md). Comme le nombre de cartes réseau prises en charge varie suivant la [taille des machines virtuelles](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , pensez à dimensionner la vôtre en conséquence.

Cet article explique comment créer une machine virtuelle avec plusieurs cartes réseau à l’aide d’Azure CLI 2.0. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](multiple-nics-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="create-supporting-resources"></a>Créer des ressources de support
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/#login).

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `mystorageaccount` et `myVM`.

Tout d’abord, créez un groupe de ressources avec la commande [az group create](/cli/azure/group#create). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` à l’emplacement `WestUS` :

```azurecli
az group create --name myResourceGroup --location westus
```

Créez le réseau virtuel avec la commande [az network vnet create](/cli/azure/network/vnet#create). L’exemple suivant permet de créer un réseau virtuel nommé `myVnet` et un sous-réseau nommé `mySubnetFrontEnd` :

```azurecli
az network vnet create --resource-group myResourceGroup --name myVnet \
  --address-prefix 192.168.0.0/16 --subnet-name mySubnetFrontEnd --subnet-prefix 192.168.1.0/24
```

Créez un sous-réseau pour le trafic principal avec la commande [az network vnet subnet create](/cli/azure/network/vnet/subnet#create). L’exemple suivant permet de créer un sous-réseau nommé `mySubnetBackEnd` :

```azurecli
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVnet \
    --name mySubnetBackEnd --address-prefix 192.168.2.0/24
```

## <a name="create-and-configure-multiple-nics"></a>Créer et configurer plusieurs cartes réseau
Vous trouverez plus d’informations sur le [déploiement de plusieurs cartes réseau à l’aide de l’interface de ligne de commande Azure](../../virtual-network/virtual-network-deploy-multinic-arm-cli.md), notamment les scripts pour le processus de bouclage pour créer toutes les cartes réseau.

En général, vous créez un [groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md) ou un [équilibreur de charge](../../load-balancer/load-balancer-overview.md) pour faciliter la gestion et la répartition du trafic entre vos machines virtuelles. Créez un groupe de sécurité réseau avec la commande [az network nsg create](/cli/azure/network/nsg#create). L’exemple suivant permet de créer un groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
az network nsg create --resource-group myResourceGroup \
  --name myNetworkSecurityGroup
```

Créez deux cartes réseau avec la commande [az network nic create](/cli/azure/network/nic#create). L’exemple suivant crée deux cartes réseau, nommées `myNic1` et `myNic2`, connectées au groupe de sécurité réseau et avec une carte réseau se connectant à chaque sous-réseau :

```azurecli
az network nic create --resource-group myResourceGroup --name myNic1 \
  --vnet-name myVnet --subnet mySubnetFrontEnd \
  --network-security-group myNetworkSecurityGroup
az network nic create --resource-group myResourceGroup --name myNic2 \
  --vnet-name myVnet --subnet mySubnetBackEnd \
  --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>Créer une machine virtuelle et attacher les cartes réseau
Lorsque vous créez la machine virtuelle, spécifiez les cartes réseau que vous avez créées avec `--nics`. Vous devez également faire attention en définissant la taille de la machine virtuelle. Il existe des limites pour le nombre maximal de cartes réseau que vous pouvez ajouter à une machine virtuelle. En savoir plus sur les [tailles des machines virtuelles Linux](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#create). L’exemple qui suit permet de créer une machine virtuelle nommée `myVM` qui utilise [Azure Managed Disks](../../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics myNic1 myNic2
```

## <a name="create-multiple-nics-using-resource-manager-templates"></a>Créer plusieurs cartes réseau à l’aide de modèles Resource Manager
Les modèles Azure Resource Manager utilisent des fichiers JSON déclaratifs pour définir votre environnement. Vous pouvez consulter une [vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). Grâce aux modèles Resource Manager, vous pouvez créer plusieurs instances d’une ressource pendant le déploiement, à l’image de la création de plusieurs cartes réseau. Utilisez *copy* pour spécifier le nombre d’instances à créer :

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

En savoir plus sur la [création de plusieurs instances à l’aide de *copy*](../../resource-group-create-multiple.md). 

Vous pouvez également utiliser `copyIndex()` pour ajouter ensuite un numéro à un nom de ressource permettant de créer `myNic1`, `myNic2`, etc. Voici un exemple d’ajout de la valeur d’index :

```json
"name": "[concat('myNic', copyIndex())]", 
```

Vous pouvez consulter un exemple complet de la [création de plusieurs cartes réseau à l’aide de modèles Resource Manager](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Étapes suivantes
Vérifiez les [tailles des machines virtuelles Linux](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous créez une machine virtuelle avec plusieurs cartes réseau. Faites attention au nombre maximal de cartes réseau pris en charge par chaque taille de machine virtuelle. 
