---
title: "Exemple de script Azure PowerShell - Acheminer le trafic pour la haute disponibilité des applications | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Acheminer le trafic pour la haute disponibilité des applications"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: georgewallace
tags: azure-infrastructure
ms.assetid: 
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 2f0ac4fd1779661aab04bafb217e64af5d619a2f
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017

---

# <a name="route-traffic-for-high-availability-of-applications"></a>Acheminer le trafic pour la haute disponibilité des applications

Ce script crée un groupe de ressources, deux plans App Service, deux applications web, un profil Traffic Manager et deux points de terminaison Traffic Manager. Traffic Manager dirige le trafic vers l’application d’une région considérée comme région principale et d’une région secondaire lorsque l’application de la région principale n’est pas disponible. Avant d’exécuter le script, veillez à modifier les valeurs MyWebApp, MyWebAppL1 et MyWebAppL2 pour leur attribuer des valeurs uniques dans Azure. Après avoir exécuté le script, vous pouvez accéder à l’application de la région principale avec l’URL mywebapp.trafficmanager.net.

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), puis exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Acheminer le trafic pour la haute disponibilité")]


Exécutez la commande suivante pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup1
Remove-AzureRmResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour créer un groupe de ressources, une application web, un profil Traffic Manager et toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)  | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Crée un plan App Service. Cela équivaut à une batterie de serveurs pour votre application web Azure. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Crée une application web Azure dans le plan App Service. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Crée une application web Azure dans le plan App Service. |
| [New-AzureRmTrafficManagerProfile](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerprofile) | Crée un profil Azure Traffic Manager. |
| [New-AzureRmTrafficManagerEndpoint](/powershell/module/azurerm.trafficmanager/new-azurermtrafficmanagerendpoint) | Ajoute un point de terminaison à un profil Azure Traffic Manager. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Vous pouvez trouver des exemples supplémentaires de scripts PowerShell de mise en réseau dans la [documentation Vue d’ensemble de la mise en réseau Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
