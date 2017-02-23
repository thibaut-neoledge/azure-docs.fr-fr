---
title: "PowerShell : créer et gérer des bases de données SQL Azure uniques | Microsoft Docs"
description: "Aide-mémoire sur la création et la gestion de bases de données SQL Azure uniques avec le Portail Azure."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>Créer et gérer des bases de données SQL Azure uniques avec PowerShell

Vous pouvez créer et gérer des bases de données SQL Azure uniques avec le [Portail Azure](https://portal.azure.com/), PowerShell, Transact-SQL, l’API REST ou C#. Cette rubrique porte sur l’utilisation de PowerShell. Pour le Portail Azure, consultez la page [Créer et gérer des bases de données SQL Azure uniques avec le Portail Azure](sql-database-manage-single-databases-powershell.md). Pour Transact-SQL, consultez la page [Créer et gérer des bases de données uniques avec Transact-SQL](sql-database-manage-single-databases-tsql.md). 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>Création d’une base de données SQL Azure à l’aide de PowerShell

Pour créer une base de données SQL, utilisez l’applet de commande [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). Le groupe de ressources et le serveur doivent déjà exister dans votre abonnement. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> Pour obtenir un exemple de script, consultez [Créer un script PowerShell de base de données SQL](sql-database-get-started-powershell.md).
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Modifier les niveaux de service et de performances d’une base de données unique

Exécutez l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) et définissez **-RequestedServiceObjectiveName** sur le niveau de performances correspondant au niveau tarifaire souhaité ; par exemple, *S0*, *S1*, *S2*, *S3*, *P1*, *P2* et ainsi de suite.

Remplacez ```{variables}``` par vos valeurs (n’incluez pas d’accolades).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>Étapes suivantes
* Pour une vue d’ensemble des outils de gestion, consultez [Vue d’ensemble des outils de gestion](sql-database-manage-overview.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide du portail Azure, consultez [Gérer des bases de données SQL Azure au moyen du portail Azure](sql-database-manage-portal.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de PowerShell, consultez [Gérer des bases de données SQL Azure au moyen de PowerShell](sql-database-manage-powershell.md).
* Pour découvrir comment effectuer des tâches de gestion à l’aide de SQL Server Management Studio, consultez [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Pour plus d’informations sur le service Base de données SQL, consultez [Présentation de la base de données SQL](sql-database-technical-overview.md). 
* Pour plus d’informations sur les serveurs Azure Database et les fonctionnalités des bases de données, consultez [Fonctionnalités](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


