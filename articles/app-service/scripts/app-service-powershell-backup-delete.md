---
title: Exemple de script Azure PowerShell - Supprimer une sauvegarde pour une application web | Microsoft Docs
description: Exemple de script Azure PowerShell - Supprimer une sauvegarde pour une application web
services: app-service\web
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="delete-a-backup-for-a-web-app"></a>Supprimer une sauvegarde pour une application web

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis crée une sauvegarde unique pour celle-ci. 

Pour exécuter ce script, vous avez besoin d’une sauvegarde existante pour une application web. Pour en créer une, consultez [Sauvegarder une application web](app-service-powershell-backup-onetime.md) ou [Créer une sauvegarde planifiée pour une application web](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Obtient une liste des sauvegardes d’une application web. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Supprime la sauvegarde spécifiée d’une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../app-service-powershell-samples.md) (Exemples Azure PowerShell).
