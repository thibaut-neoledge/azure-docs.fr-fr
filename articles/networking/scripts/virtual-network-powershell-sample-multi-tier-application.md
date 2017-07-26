---
title: "Exemple de script Azure PowerShell - Créer un réseau pour les applications multiniveau | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Créer un réseau pour les applications multiniveau."
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: ab49e78ef17b093d2bbe4e3276a1ece3a4247f91
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---

# <a name="create-a-network-for-multi-tier-applications"></a>Créer un réseau pour les applications multiniveau

Cet exemple de script permet de créer un réseau virtuel avec des sous-réseaux frontaux et principaux. Le trafic vers le sous-réseau frontal est limité à HTTP et SSH, tandis que le trafic vers le sous-réseau principal est limité à MySQL, port 3306. Après avoir exécuté le script, vous obtenez deux machines virtuelles, une dans chaque sous-réseau sur laquelle que vous pouvez déployer le serveur web et le logiciel MySQL.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script


[!code-powershell[principal](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Créer un réseau virtuel pour les applications multiniveau")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, un réseau virtuel et les groupes de sécurité réseau. Chaque commande de la table renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crée un réseau virtuel et un sous-réseau frontal Azure. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée un sous-réseau principal. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée une adresse IP publique pour accéder à la machine virtuelle à partir d’Internet. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Crée des interfaces réseau virtuelles et les attache aux sous-réseaux frontaux et principaux du réseau virtuel. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) | Crée des groupes de sécurité réseau (NSG) associés aux sous-réseaux frontaux et principaux. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) |Crée des règles NSG qui autorisent ou bloquent des ports spécifiques sur des sous-réseaux donnés. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Crée des machines virtuelles et associe une carte d’interface réseau à chacune d’elles. Cette commande spécifie également l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, ainsi que toutes ses ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts PowerShell de mise en réseau dans la [documentation Vue d’ensemble de la mise en réseau Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
