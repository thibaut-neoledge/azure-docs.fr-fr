---
title: "Redéployer des machines virtuelles Windows | Microsoft Docs"
description: "Décrit comment redéployer des machines virtuelles Windows pour atténuer les problèmes de connexion RDP."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 61dbf8fb96233d2e0f592deacf139a83cee51954


---
# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Redéployer une machine virtuelle vers un nouveau nœud Azure
Si vous avez été confronté à des difficultés pour la résolution des problèmes de connexion de Bureau à distance ou l’accès des applications à une machine virtuelle Azure basée sur Windows, le redéploiement de la machine virtuelle peut vous aider. Lorsque vous redéployez une machine virtuelle, celle-ci est déplacée vers un nouveau nœud au sein de l’infrastructure Azure, puis remise sous tension, conservant tous vos options de configuration et ressources associées. Cet article vous montre comment redéployer une machine virtuelle à l’aide d’Azure PowerShell ou du portail Azure.

> [!NOTE]
> Une fois que vous redéployez une machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour. 
> 
> 

## <a name="using-azure-powershell"></a>Utilisation de Microsoft Azure PowerShell
Assurez-vous que vous avez installé la dernière version d’Azure PowerShell 1.x. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

Utilisez cette commande Azure PowerShell pour redéployer votre machine virtuelle :

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes de connexion à votre machine virtuelle, vous pouvez rechercher une aide spécifique sur la [résolution des problèmes de connexion Bureau à distance](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou sur les [étapes de dépannage détaillées des connexions Bureau à distance](virtual-machines-windows-detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vous pouvez également lire des informations sur les [problèmes de dépannage des applications](virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) si vous ne pouvez pas accéder à une application exécutée sur votre machine virtuelle.




<!--HONumber=Nov16_HO3-->


