---
title: "Exemple de script Azure PowerShell - Créer une application web et déployer du code à partir de GitHub | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Créer une application web et déployer du code à partir de GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 81524766b651068d1e7c97d016a0b3f649fc1080
ms.lasthandoff: 04/27/2017

---

# <a name="create-a-web-app-and-deploy-code-from-github"></a>Créer une application web et déployer du code à partir de GitHub

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis déploie votre code d’application web dans un référentiel GitHub public (sans déploiement continu). Pour le déploiement GitHub avec déploiement continu, consultez [Créer une application web avec un déploiement continu à partir de GitHub](app-service-powershell-continuous-deployment-github.md).

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). En outre, vous avez besoin d’un lien vers le dépôt GitHub qui contient le code d’application web.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/app-service/deploy-github/deploy-github.ps1?highlight=1-2 "Créer une application web et déployer du code à partir de GitHub")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Crée un plan App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Crée une application web. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifie une ressource dans un groupe de ressources. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

