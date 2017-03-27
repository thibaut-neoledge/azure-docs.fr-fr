---
title: "Exemple de script Azure PowerShell - Créer une application web et déployer du code dans un environnement intermédiaire | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Créer une application web et déployer du code dans un environnement intermédiaire"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: afb3c401c9c41d2cd0a1ef24e175137057019da0
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Créer une application web et déployer du code dans un environnement intermédiaire

Cet exemple de script crée une application web dans App Service avec un emplacement de déploiement supplémentaire appelé « intermédiaire », puis déploie un exemple d’application à l’emplacement « intermédiaire ».

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Login-AzureRmAccount`. 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Créer une application web et déployer du code dans un environnement intermédiaire")]

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
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Crée un plan App Service. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crée une application web. |
| [Set-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermappserviceplan) | Modifie le niveau tarifaire d’un plan App Service. |
| [New-AzureRmWebAppSlot](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebappslot) | Crée un emplacement de déploiement pour une application web. |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | Modifie une ressource dans un groupe de ressources. |
| [Swap-AzureRmWebAppSlot](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/swap-azurermwebappslot) | Bascule un déploiement d’application web en production. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

