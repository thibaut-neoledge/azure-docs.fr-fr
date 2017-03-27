---
title: "Script Azure PowerShell - Importer-bacpac-base de données SQL | Microsoft Docs"
description: "Exemple de script Azure PowerShell - Importer à partir d’un fichier bacpac dans une base de données SQL à l’aide de PowerShell"
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
ms.openlocfilehash: 6ca1560d6d3fa313bd24cb6226cf1e78992af257
ms.lasthandoff: 03/10/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>Importer à partir d’un fichier bacpac dans une base de données SQL à l’aide de PowerShell

Cet exemple de script PowerShell importe une base de données à partir d’un fichier bacpac.  

Avant d’exécuter ce script, vérifiez qu’une connexion avec Azure a bien été créée à l’aide de l’applet de commande `Add-AzureRmAccount`.

## <a name="sample-script"></a>Exemple de script

[!code-powershell[principal](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "Créer une instance SQL Database")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Remarques |
|---|---|
| [New-AzureRmResourceGroup]() | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmSqlServer]() | Crée un serveur logique qui héberge l’instance SQL Database. |
| [New-AzureRmSqlServerFirewallRule]() | Crée une règle de pare-feu pour autoriser l’accès à toutes les instances SQL Database sur le serveur à partir de la plage d’adresses IP entrée. |
| [New-AzureRmSqlDatabase]() | Crée une instance SQL Database au sein du serveur logique. |
| [Remove-AzureRmResourceGroup]() | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez [Documentation Azure PowerShell](https://docs.microsoft.com/powershell/).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Azure SQL Database PowerShell scripts (Scripts PowerShell Azure SQL Database)](../sql-database-powershell-samples.md).
