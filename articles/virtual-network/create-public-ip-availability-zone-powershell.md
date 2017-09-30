---
title: "Créer une adresse IP publique zonale à l’aide de PowerShell| Microsoft Docs"
description: "Créez une adresse IP publique dans une zone de disponibilité à l’aide de PowerShell."
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
ms.openlocfilehash: 701a8febb3f897fd7f1a81a00adb4635df153d3a
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-public-ip-address-in-an-availability-zone-with-powershell"></a>Créer une adresse IP publique dans une zone de disponibilité à l’aide de PowerShell

Vous pouvez déployer une adresse IP publique dans une zone de disponibilité Azure (préversion). Une zone de disponibilité est une zone physiquement séparée dans une région Azure. Découvrez comment :

> * Créer une adresse IP publique dans une zone de disponibilité
> * Identifier les ressources associées créées dans la zone de disponibilité
  

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Cet article suppose que le module AzureRM version 4.4.0 ou ultérieure est installé. Pour connaître la version de l’interface, exécutez `Get-Module -ListAvailable AzureRM`. Si vous devez installer ou mettre à niveau le module AzureRM, installez la dernière version du module à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureRM).

> [!NOTE]
> Les zones de disponibilité sont disponibles en préversion et sont préparées pour vos scénarios de développement et de test. La prise en charge est fournie pour certaines ressources, régions et familles de tailles de machine virtuelle Azure. Pour plus d’informations sur le démarrage ainsi que sur les ressources, régions et familles de tailles de machine virtuelle Azure pour lesquelles vous pouvez essayer les zones de disponibilité, consultez [Vue d’ensemble des zones de disponibilité](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview). Pour obtenir de l’aide, vous pouvez nous contacter sur [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) ou [ouvrir un ticket de support Azure](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```
## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Dans cet exemple, un groupe de ressources nommé *myResourceGroupVM* est créé dans la région *westeurope*. *westeurope* est une des régions Azure qui prennent en charge les zones de disponibilité en préversion.

```powershell
New-AzureRmResourceGroup -Name AzTest -Location westeurope
```

## <a name="create-a-zonal-public-ip-address"></a>Créer une adresse IP publique zonale

Créez une adresse IP publique dans une zone de disponibilité à l’aide de la commande suivante :

```powershell
    New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroup `
        -Name myPublicIp `
        -Location westeurope `
        -AllocationMethod Static `
        -Zone 2
```

> [!NOTE]
> Quand vous assignez une adresse IP publique de référence SKU standard à l’interface réseau d’une machine virtuelle, vous devez explicitement autoriser le trafic prévu avec un [groupe de sécurité réseau](security-overview.md#network-security-groups). La communication avec la ressource est possible uniquement si vous créez et associez un groupe de sécurité réseau et que vous autorisez explicitement le trafic prévu.

## <a name="get-zone-information-about-a-public-ip-address"></a>Obtenir des informations sur la zone d’une adresse IP publique

Obtenez les informations sur la zone d’une adresse IP publique à l’aide de la commande suivante :

```powershell
Get-AzureRmPublicIpAddress ` 
    -ResourceGroup myResourceGroup `
    -Name myPublicIp
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [zones de disponibilité](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview)
- En savoir plus sur les [adresses IP publiques](virtual-network-public-ip-address.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
