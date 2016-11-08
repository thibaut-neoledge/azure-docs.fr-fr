---
title: Configurer des points de terminaison sur une machine virtuelle Linux classique | Microsoft Docs
description: Apprenez à configurer des points de terminaison pour une machine virtuelle Linux dans le portail Azure Classic pour permettre la communication avec une machine virtuelle Linux dans Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: cynthn

---
# Comment configurer des points de terminaison sur une machine virtuelle Linux classique dans Azure
Toutes les machines virtuelles Linux créées dans Azure à l’aide du modèle de déploiement classique peuvent automatiquement communiquer sur un canal réseau privé avec d’autres machines virtuelles dans le même service cloud ou réseau virtuel. Toutefois, les ordinateurs sur Internet ou d'autres réseaux virtuels requièrent des points de terminaison pour diriger le trafic réseau entrant vers une machine virtuelle. Cet article est également disponible pour les [machines virtuelles Windows](virtual-machines-windows-classic-setup-endpoints.md).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Dans le modèle de déploiement **Resource Manager**, les points de terminaison sont configurés à l’aide de **groupes de sécurité réseau (NSG)**. Pour plus d’informations, consultez la page [Ouverture des ports et des points de terminaison](virtual-machines-linux-nsg-quickstart.md).

Lorsque vous créez une machine virtuelle Linux sur le portail Azure Classic, un point de terminaison pour Secure Shell (SSH) est en général créé automatiquement pour vous. Vous pouvez configurer des points de terminaison supplémentaires lors de la création de la machine virtuelle, ou ultérieurement si nécessaire.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## Étapes suivantes
* Vous pouvez également créer un point de terminaison de machine virtuelle à l’aide de l’[interface de ligne de commande Azure](../virtual-machines-command-line-tools.md). Exécutez la commande **azure vm endpoint create**.
* Si vous avez créé une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez utiliser l’interface CLI Azure en mode Resource Manager pour [créer des groupes de sécurité réseau](../virtual-network/virtual-networks-create-nsg-arm-cli.md) afin de contrôler le trafic vers la machine virtuelle.

<!---HONumber=AcomDC_0713_2016-->