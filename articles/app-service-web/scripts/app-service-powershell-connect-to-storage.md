---
title: "Exemple de script Azure PowerShell - Connecter une application web à un compte de stockage | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Connecter une application web à un compte de stockage"
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 923cfd624ef0f77bd59c4b9d196172bfd69ad806
ms.lasthandoff: 03/10/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>Connecter une application web à un compte de stockage

Dans ce scénario, vous allez apprendre à créer un compte de stockage Azure et une application web Azure. Ensuite, vous allez lier le compte de stockage à l’application web en vous appuyant sur les paramètres de l’application.

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Login-AzureRmAccount`.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connecter une application web à un compte de stockage")]

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
| [New-AzureRMStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/New-AzureRmStorageAccount) | Crée un compte de stockage. |
| [Get-AzureRMStorageAccountKey](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/get-azurermstorageaccountkey) | Obtient les clés d’accès pour le compte Azure Storage. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifie la configuration d’une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

