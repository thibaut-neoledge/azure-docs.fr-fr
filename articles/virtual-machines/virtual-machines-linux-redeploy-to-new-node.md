---
title: "Redéploiement des machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Redéploiement des machines virtuelles Linux dans Azure pour atténuer les problèmes de connexion SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7f3abdd63e43713d9d1f7ff28e44efc08167fddb
ms.openlocfilehash: f421ebdc81ce413d69f62a3b86563ab4a09b747a
ms.lasthandoff: 02/27/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Redéployer une machine virtuelle vers un nouveau nœud Azure
Si vous avez été confronté à des difficultés pour la résolution des problèmes SSH ou l’accès des applications à une machine virtuelle Linux dans Azure, vous pouvez tenter de redéployer la machine virtuelle. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise sous tension, conservant tous vos options de configuration et ressources associées. Cet article vous montre comment redéployer une machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du portail Azure.

> [!NOTE]
> Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour. 

Vous pouvez redéployer une machine virtuelle à l’aide d’une des options suivantes. Choisissez une seule option pour redéployer votre machine virtuelle :

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Portail Azure](#using-azure-portal)

## <a name="azure-cli-20"></a>Azure CLI 2.0
Installez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/#login).

Redéployez votre machine virtuelle avec la commande [az vm redeploy](/cli/azure/vm#redeploy). L’exemple suivant redéploie la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
Installez la [dernière version d’Azure CLI 1.0](../xplat-cli-install.md), connectez-vous à un compte Azure et vérifiez que le mode Resource Manager est activé (`azure config mode arm`).

L’exemple suivant redéploie la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup` :

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez rechercher une aide spécifique sur le [dépannage des connexions SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou sur les [étapes de dépannage détaillées des connexions SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous rencontrez des problèmes de connexion à votre machine virtuelle. Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.


