---
title: "Script Azure PowerShell - Déplacer une instance SQL Database et des pools élastiques | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Déplacer une instance SQL Database entre des pools élastiques à l’aide de PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: f43dc60993a9f73b0327ae524a45e915f42935a2
ms.lasthandoff: 03/10/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-powershell"></a>Créer des pools élastiques et déplacer les bases de données entre les pools et en dehors d’un pool à l’aide de PowerShell

Cet exemple de script PowerShell crée deux pools élastiques et déplace une base de données d’un pool élastique vers un autre pool élastique, puis déplace une base de données en dehors d’un pool élastique vers un niveau de performance de base de données unique. 

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Add-AzureRmAccount`.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1 "Déplacer une base de données entre les pools")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Crée un serveur logique qui héberge une base de données ou un pool élastique. |
| [New-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlelasticpool) | Crée un pool élastique au sein d’un serveur logique. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Crée une base de données au sein d’un serveur logique en tant que base de données unique ou regroupée. |
| [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabase) | Met à jour les propriétés de la base de données ou déplace une base de données vers, hors ou entre des pools élastiques. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |
|||

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Azure SQL Database PowerShell scripts (Scripts PowerShell Azure SQL Database)](../sql-database-powershell-samples.md).

