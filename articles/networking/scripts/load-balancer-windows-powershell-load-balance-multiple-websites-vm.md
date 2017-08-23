---
title: "Exemple de script Azure PowerShell - Équilibrer la charge de plusieurs sites web avec l’interface Azure PowerShell | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Équilibrer la charge de plusieurs sites web sur la même machine virtuelle"
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: d73cdc98ff279c3ee1b93443abe4b6c7c97786a2
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---

# <a name="load-balance-multiple-websites"></a>Équilibrer la charge de plusieurs sites web

Cet exemple de script crée un réseau virtuel avec deux machines virtuelles qui sont membres d’un groupe à haute disponibilité. Un équilibrage de charge dirige le trafic pour les deux adresses IP distinctes vers les deux machines virtuelles. Après avoir exécuté le script, vous pouvez déployer le logiciel de serveur web pour les machines virtuelles et héberger plusieurs sites web, chacun avec sa propre adresse IP.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Équilibrer la charge de plusieurs sites web")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel, un équilibreur de charge et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) | Crée un groupe de disponibilité Azure pour fournir une haute disponibilité. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée une configuration de sous-réseau. Cette configuration est utilisée avec le processus de création du réseau virtuel. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Créer un réseau virtuel. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée une adresse IP publique. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Crée une configuration IP frontale pour un équilibreur de charge. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Crée une configuration de pool d’adresses principales pour un équilibreur de charge. |
| [New-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Crée une sonde d’équilibrage de charge réseau. Une sonde d’équilibrage de charge réseau permet de surveiller chaque machine virtuelle dans le jeu d’équilibrage de charge réseau. Si une machine virtuelle n’est plus accessible, le trafic n’est pas acheminé vers cette machine virtuelle. |
| [New-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Crée une règle d’équilibrage de charge réseau. Dans cet exemple, une règle est créée pour le port 80. Comme le trafic HTTP arrive au niveau de l’équilibrage de charge réseau, il est acheminé vers le port 80 de l’une des machines virtuelles du jeu d’équilibrage de charge réseau. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Crée un équilibreur de charge. |
| [New-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/new-azurermnetworkinterfaceipconfig) | Définit les fonctionnalités avancées d’une interface réseau virtuelle. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crée une interface réseau. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Crée une configuration de machine virtuelle. Cette configuration inclut des informations telles que le nom de la machine virtuelle, le système d’exploitation et les informations d’identification d’administration. La configuration est utilisée lors de la création de machines virtuelles. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Création d’une machine virtuelle |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts PowerShell de mise en réseau dans la [documentation Vue d’ensemble de la mise en réseau Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).

