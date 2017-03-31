---
title: Configurer des points de terminaison sur une machine virtuelle Windows classique | Microsoft Docs
description: "Apprenez à configurer des points de terminaison pour une machine virtuelle Windows dans le portail Azure Classic pour permettre la communication avec une machine virtuelle Windows dans Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: dcafe045203d77b6169f2e7a5d7a0c42de37f685
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle Windows classique dans Azure
Toutes les machines virtuelles Windows créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer à travers un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Dans le modèle de déploiement **Resource Manager**, les points de terminaison sont configurés à l’aide de **groupes de sécurité réseau (NSG)**. Pour plus d’informations, voir [Autoriser l’accès externe à votre machine virtuelle à l’aide du portail Azure](../../virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quand vous créez une machine virtuelle Windows dans le portail Azure Classic, les points de terminaison courants, notamment ceux du Bureau à distance et de l’Accès distant Windows PowerShell, sont généralement créés pour vous automatiquement. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes
* Pour configurer un point de terminaison de machine virtuelle à l’aide d’une applet de commande Azure PowerShell, consultez [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Pour gérer une liste de contrôle d’accès sur un point de terminaison à l’aide d’une applet de commande Azure PowerShell, consultez [Gestion des listes de contrôle d’accès (ACL) pour les points de terminaison à l’aide de PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez utiliser Azure PowerShell pour [créer des groupes de sécurité réseau](../../../virtual-network/virtual-networks-create-nsg-arm-ps.md) afin de contrôler le trafic vers la machine virtuelle.


