---
title: Exemple de script Azure PowerShell - Surveiller une application web avec les journaux de serveur web | Microsoft Docs
description: Exemple de script Azure PowerShell - Surveiller une application web avec les journaux de serveur web
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 5805d7cd-9e56-4eba-bd85-75b013690ff5
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ccadd21df02d58fac8eac8e3910092c4d8374504
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-a-web-app-with-web-server-logs"></a>Surveiller une application web avec les journaux de serveur web

Dans ce scénario, vous créez un groupe de ressources, un plan App Service, une application web, et vous configurez l’application web afin d’activer les journaux de serveur web. Vous téléchargez ensuite les fichiers journaux pour révision.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Login-AzureRmAccount`.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1 "Surveiller une application web avec les journaux de serveur web")]

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
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifie la configuration d’une application web. |
| [Get-AzureRMWebAppMetrics](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebappmetrics) | Obtient les mesures de l’application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

