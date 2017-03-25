---
title: "Exemple de script Azure PowerShell - Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 8e21e2c88578177eb8bc6120ecfcd1222b173ff2
ms.lasthandoff: 03/10/2017

---

# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité

Dans ce scénario, vous créez un groupe de ressources, deux plans App Service, deux applications web, un profil Traffic Manager et deux points de terminaison Traffic Manager. Une fois cette procédure terminée, vous bénéficiez d’une architecture haute disponibilité qui offre une disponibilité globale de votre application web en fonction de la plus faible latence réseau.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Login-AzureRmAccount`.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Mettre à l’échelle une application web dans le monde entier avec une architecture haute disponibilité")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRMTrafficManagerProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.trafficmanager/v2.5.0/new-azurermtrafficmanagerprofile) | Crée un profil Traffic Manager. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Crée un plan App Service. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crée une application web. |
| [New-AzureRMTrafficManagerEndpoint](https://docs.microsoft.com/powershell/resourcemanager/azurerm.trafficmanager/v2.5.0/new-azurermtrafficmanagerendpoint) | Crée un point de terminaison dans un profil Azure Traffic Manager. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

