---
title: "Applets de commande PowerShell pour Azure SQL Data Warehouse"
description: "Recherchez les principaux applets de commande PowerShell pour Azure SQL Data Warehouse, y compris comment suspendre et reprendre une base de données."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 6f0d5772-f05f-4cc8-9749-4adb153dfd50
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: e993e8c0cb7b7143f9e7be5bd413f42742666fa8


---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>Applets de commande PowerShell et API REST pour SQL Data Warehouse
De nombreuses tâches d’administration de SQL Data Warehouse peuvent être gérées à l’aide d’applets de commande Azure PowerShell ou d’API REST.  Voici quelques exemples d’utilisation des commandes PowerShell pour automatiser les tâches courantes dans SQL Data Warehouse.  Pour obtenir de bons exemples REST, consultez l’article [Gérer l’évolutivité avec REST][Gérer l’évolutivité avec REST].

> [!NOTE]
> Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure.  Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**.  La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][Installation et configuration d’Azure PowerShell].
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Prise en main des applets de commande Azure PowerShell
1. Ouvrez Windows PowerShell.
2. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>Exemple : suspendre une base de données SQL Data Warehouse
Une base de données appelée « Database02 » et hébergée sur un serveur appelé « Server01 » est interrompue.  Le serveur est un groupe de ressources Azure appelé « ResourceGroup1 ».

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variante, dans cet exemple, l’objet récupéré est redirigé vers [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase].  En conséquence, la base de données est interrompue. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>Exemple : démarrer une base de données SQL Data Warehouse
Les opérations d’une base de données appelée « Database02 » et hébergée sur un serveur « Server01 » sont reprises. Le serveur est hébergé dans un groupe de ressources appelé « ResourceGroup1 ».

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variante, dans cet exemple une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». L’objet récupéré est redirigé vers [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.
> 
> 

## <a name="frequently-used-powershell-cmdlets"></a>Applets de commande PowerShell couramment utilisées
Ces applets de commande PowerShell sont souvent utilisées avec Azure SQL Data Warehouse :

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’exemples PowerShell, consultez :

* [Création de SQL Data Warehouse à l’aide de Powershell][Création de SQL Data Warehouse à l’aide de Powershell]
* [Restauration de base de données][Restauration de base de données]

Pour obtenir la liste de toutes les tâches pouvant être automatisées avec PowerShell, consultez [Applets de commande de la base de données SQL Azure][Applets de commande de la base de données SQL Azure].  Pour obtenir la liste des tâches pouvant être automatisées avec REST, consultez [Opérations des bases de données SQL Azure][Opérations des bases de données SQL Azure].

<!--Image references-->

<!--Article references-->
[Installation et configuration d’Azure PowerShell]: ../powershell-install-configure.md
[Création de SQL Data Warehouse à l’aide de Powershell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restauration de base de données]: ./sql-data-warehouse-restore-database-powershell.md
[Gérer l’évolutivité avec REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Applets de commande de la base de données SQL Azure]: https://msdn.microsoft.com/library/mt574084.aspx
[Opérations des bases de données SQL Azure]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps



<!--HONumber=Nov16_HO3-->


