---
title: Ouverture de ports et de points de terminaison sur une machine virtuelle Linux dans Azure | Microsoft Docs
description: "Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Linux à l’aide du modèle de déploiement Azure Resource Manager et de l’interface de ligne de commande Azure 1.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2ca514ec1bbfb5404070de904effd1522fcac892
ms.openlocfilehash: fd43b3a16607ccd383fbf185176d94af5aa97d89


---
# <a name="opening-ports-and-endpoints-to-a-linux-vm-in-azure"></a>Ouverture de ports et de points de terminaison sur une machine virtuelle Linux dans Azure
Pour ouvrir un port ou créer un point de terminaison sur une machine virtuelle dans Azure, créez un filtre réseau sur un sous-réseau ou une interface réseau de machine virtuelle. Vous placez ces filtres, qui contrôlent le trafic entrant et sortant, dans un groupe de sécurité réseau associé à la ressource qui reçoit le trafic. Nous allons utiliser un exemple courant de trafic web sur le port 80. Cet article vous montre comment ouvrir un port sur une machine virtuelle à l’aide de l’interface de ligne de commande Azure 1.0.


## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 1.0](#quick-commands) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)
- [Azure CLI 2.0 (version préliminaire)](virtual-machines-linux-nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) : notre interface de ligne de commande nouvelle génération pour le modèle de déploiement Resource Manager


## <a name="quick-commands"></a>Commandes rapides
Pour créer un groupe de sécurité réseau et des règles, vous devez installer[l’interface de ligne de commande Azure 1.0](../xplat-cli-install.md) et utiliser le mode Resource Manager :

```azurecli
azure config mode arm
```

Dans les exemples suivants, remplacez les exemples de noms de paramètre par vos propres valeurs. Exemples de noms de paramètre : `myResourceGroup`, `myNetworkSecurityGroup` et `myVnet`.

Créez votre groupe de sécurité réseau en entrant votre nom et votre emplacement en conséquence. L’exemple suivant crée un groupe de sécurité réseau nommé `myNetworkSecurityGroup` à l’emplacement `WestUS` :

```azurecli
azure network nsg create --resource-group myResourceGroup --location westus \
    --name myNetworkSecurityGroup
```

Ajoutez une règle pour autoriser le trafic HTTP sur votre serveur Web (ou ajustez une règle en fonction de votre propre scénario, notamment l’accès SSH ou la connectivité de base de données). L’exemple suivant crée une règle nommée `myNetworkSecurityGroupRule` pour autoriser le trafic TCP sur le port 80 :

```azurecli
azure network nsg rule create --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule \
    --protocol tcp --direction inbound --priority 1000 \
    --destination-port-range 80 --access allow
```

Associez un groupe de sécurité réseau associé à l’interface réseau (NIC) de vos machines virtuelles. L’exemple suivant associe une carte réseau existante nommée `myNic` au groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
azure network nic set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup --name myNic
```

Vous pouvez également associer votre groupe de sécurité réseau à un sous-réseau de réseau virtuel et non uniquement à l’interface réseau d’une seule machine virtuelle. L’exemple suivant associe un sous-réseau existant nommé `mySubnet` dans le réseau virtuel `myVnet` au groupe de sécurité réseau nommé `myNetworkSecurityGroup` :

```azurecli
azure network vnet subnet set --resource-group myResourceGroup \
    --network-security-group-name myNetworkSecurityGroup \
    --vnet-name myVnet --name mySubnet
```

## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../virtual-network/virtual-networks-create-nsg-arm-cli.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../load-balancer/load-balancer-get-started-internet-arm-cli.md)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../virtual-network/virtual-networks-nsg.md)
* [Présentation d’Azure Resource Manager](../load-balancer/load-balancer-arm.md)




<!--HONumber=Dec16_HO2-->


