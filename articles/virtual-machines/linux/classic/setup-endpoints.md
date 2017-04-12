---
title: Configurer des points de terminaison sur une machine virtuelle Linux classique | Microsoft Docs
description: "Apprenez à configurer des points de terminaison pour une machine virtuelle Linux dans le portail Azure Classic pour permettre la communication avec une machine virtuelle Linux dans Azure"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: c5fbdaacca1a946a94f2af1dada953a0d9c8e011
ms.lasthandoff: 04/03/2017


---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Comment configurer des points de terminaison sur une machine virtuelle Linux classique dans Azure
Toutes les machines virtuelles Linux créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer sur un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Windows](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Dans le modèle de déploiement **Resource Manager**, les points de terminaison sont configurés à l’aide de **groupes de sécurité réseau (NSG)**. Pour plus d’informations, consultez la page [Ouverture des ports et des points de terminaison](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Lorsque vous créez une machine virtuelle Linux sur le portail Azure Classic, un point de terminaison pour Secure Shell (SSH) est en général créé automatiquement pour vous. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Étapes suivantes
* Vous pouvez également créer un point de terminaison de machine virtuelle à l’aide de l’ [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Exécutez la commande **azure vm endpoint create** .
* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez utiliser l’interface CLI Azure en mode Resource Manager pour [créer des groupes de sécurité réseau](../../../virtual-network/virtual-networks-create-nsg-arm-cli.md) afin de contrôler le trafic vers la machine virtuelle.


