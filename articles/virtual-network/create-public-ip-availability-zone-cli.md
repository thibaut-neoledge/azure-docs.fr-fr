---
title: "Créer une adresse IP publique zonale à l’aide d’Azure CLI | Microsoft Docs"
description: "Créez une adresse IP publique dans une zone de disponibilité à l’aide de l’interface de ligne de commande Azure (Azure CLI)."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: jdial
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: ef93d43bbd0c58950027810c8c335d9076574326
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-the-azure-cli"></a>Créer une adresse IP publique dans une zone de disponibilité à l’aide de l’interface Azure CLI

Vous pouvez déployer une adresse IP publique dans une zone de disponibilité Azure (préversion). Une zone de disponibilité est une zone physiquement séparée dans une région Azure. Découvrez comment :

> * Créer une adresse IP publique dans une zone de disponibilité
> * Identifier les ressources associées créées dans la zone de disponibilité

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Si vous choisissez d’installer et d’utiliser Azure CLI localement, vous devez exécuter une version d’Azure CLI ultérieure à la version 2.0.17 pour effectuer ce didacticiel. Pour connaître la version de l’interface, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

> [!NOTE]
> Les zones de disponibilité sont disponibles en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour plus d’informations sur le démarrage ainsi que sur les ressources, régions et familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez essayer les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md).

## <a name="create-a-zonal-public-ip-address"></a>Créer une adresse IP publique zonale

Créez une adresse IP publique dans une zone de disponibilité à l’aide de la commande suivante :


```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIp --zone 2 --location westeurope
```
> [!NOTE]
> Quand vous assignez une adresse IP publique de référence SKU standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](security-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obtenir des informations sur la zone d’une adresse IP publique

Obtenez les informations sur la zone d’une adresse IP publique à l’aide de la commande suivante :

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIp
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [zones de disponibilité](https://docs.microsoft.com/azure/availability-zones/az-overview)
- En savoir plus sur les [adresses IP publiques](../virtual-network/virtual-network-public-ip-address.md) 

