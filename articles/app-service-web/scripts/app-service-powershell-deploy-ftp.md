---
title: "Exemple de script Azure PowerShell - Télécharger des fichiers vers une application web via FTP | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Télécharger des fichiers vers une application web via FTP"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 5f3bf8087fa6601ae410d6db907471fd6faa2742
ms.lasthandoff: 03/10/2017

---

# <a name="upload-files-to-a-web-app-using-ftp"></a>Télécharger des fichiers vers une application web via FTP

Cet exemple de script effectue les opérations suivantes à l’aide d’Azure PowerShell : 

* Créer une application web dans Azure App Service dans la région Europe de l’Ouest Azure.
* Obtenir des informations de connexion FTP à partir de l’application web
* Déployer le code d’application web à l’aide de FTP (via [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)).

## <a name="prerequisites"></a>Composants requis

* Exécutez `Login-AzureRmAccount` pour vous connecter à Azure.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Télécharger des fichiers vers une application web via FTP")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Crée un plan App Service. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crée une application web. |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | Obtient un profil de publication d’application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).

