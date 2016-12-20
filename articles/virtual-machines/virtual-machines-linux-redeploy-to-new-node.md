---
title: "Redéploiement de machines virtuelles Linux | Microsoft Docs"
description: "Décrit comment redéployer des machines virtuelles Linux pour atténuer les problèmes de connexion SSH."
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
ms.date: 09/19/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 1cd81944ff1e8b6048315946220adee4bf68e576


---
# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployer une machine virtuelle vers un nouveau nœud Azure
Si vous avez été confronté à des difficultés pour la résolution des problèmes SSH ou l’accès des applications à une machine virtuelle Azure, le redéploiement de la machine virtuelle peut vous aider. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise sous tension, conservant tous vos options de configuration et ressources associées. Cet article vous montre comment redéployer une machine virtuelle à l’aide de l’interface de ligne de commande Azure ou du portail Azure.

> [!NOTE]
> Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour. 
> 
> 

## <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Assurez-vous que la [dernière interface de ligne de commande Azure est installée](../xplat-cli-install.md) sur votre ordinateur et que vous êtes en mode Resource Manager (`azure config mode arm`).

Utilisez la commande de l’interface de ligne de commande Azure suivante pour redéployer votre machine virtuelle :

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Vous pouvez voir l’état de la machine virtuelle évoluer tandis qu’elle passe par le processus de redéploiement. Le paramètre `PowerState` de la machine virtuelle passe de « Exécution » à « Mise à jour », puis à « Démarrage » et enfin « Exécution » tandis qu’elle passe par le processus de redéploiement sur un nouvel hôte. Vérifiez l’état des machines virtuelles au sein d’un groupe de ressources avec :

```bash
azure vm list -g <resourcegroup>
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez rechercher une aide spécifique sur le [dépannage des connexions SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou sur les [étapes de dépannage détaillées des connexions SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous rencontrez des problèmes de connexion à votre machine virtuelle. Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.




<!--HONumber=Nov16_HO3-->


