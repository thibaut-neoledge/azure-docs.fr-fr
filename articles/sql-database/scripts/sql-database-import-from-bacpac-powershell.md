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
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 5234b7ee73dee8348a5354ba354ae459a82492b9
ms.contentlocale: fr-fr
ms.lasthandoff: 04/27/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>Importer à partir d’un fichier bacpac dans une base de données SQL à l’aide de PowerShell

Cet exemple de script PowerShell importe une base de données à partir d’un fichier **bacpac**.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

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
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Crée un serveur logique qui héberge l’instance SQL Database. |
| [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) | Crée une règle de pare-feu pour autoriser l’accès à toutes les instances SQL Database sur le serveur à partir de la plage d’adresses IP entrée. |
| [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) | Importe un fichier .bacpac et crée une base de données sur le serveur. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts SQL Database PowerShell sur la page [Scripts PowerShell Azure SQL Database](../sql-database-powershell-samples.md).

