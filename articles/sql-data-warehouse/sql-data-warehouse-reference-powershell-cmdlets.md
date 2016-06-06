<properties
   pageTitle="Applets de commande PowerShell pour Azure SQL Data Warehouse"
   description="Recherchez les principaux applets de commande PowerShell pour Azure SQL Data Warehouse, y compris comment suspendre et reprendre une base de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/18/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Applets de commande PowerShell et API REST pour SQL Data Warehouse

De nombreuses tâches d’administration de SQL Data Warehouse peuvent être gérées à l’aide d’applets de commande Azure PowerShell ou d’API REST. Voici quelques exemples d’utilisation des commandes PowerShell pour automatiser les tâches courantes dans SQL Data Warehouse. Pour obtenir de bons exemples REST, consultez l’article [Gérer l’évolutivité avec REST][].

> [AZURE.NOTE]  Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure. Vous pouvez vérifier la version en exécutant **Get-Module -ListAvailable -Name Azure**. La version la plus récente peut être installée à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations sur l’installation de la dernière version, consultez la page [Installation et configuration d’Azure PowerShell][].

## Prise en main des applets de commande Azure PowerShell

1. Ouvrez Windows PowerShell. 
2. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Exemple : suspendre une base de données SQL Data Warehouse

Une base de données appelée « Database02 » et hébergée sur un serveur appelé « Server01 » est interrompue. Le serveur est un groupe de ressources Azure appelé « ResourceGroup1 ».

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variante, dans cet exemple l’objet récupéré est redirigé vers [Suspend-AzureRmSqlDatabase][]. En conséquence, la base de données est interrompue. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Exemple : démarrer une base de données SQL Data Warehouse

Les opérations d’une base de données appelée « Database02 » et hébergée sur un serveur « Server01 » sont reprises. Le serveur est hébergé dans un groupe de ressources appelé « ResourceGroup1 ».

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variante, dans cet exemple une base de données appelée « Database02 » est récupérée d’un serveur appelé « Server01 » hébergé dans un groupe de ressources appelé « ResourceGroup1 ». L’objet récupéré est redirigé vers [Resume-AzureRmSqlDatabase][].

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [AZURE.NOTE] Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.

## Applets de commande PowerShell couramment utilisées

Ces applets de commande PowerShell sont souvent utilisées avec Azure SQL Data Warehouse :

- [Get-AzureRmSqlDatabase][]
- [Get-AzureRmSqlDeletedDatabaseBackup][]
- [Get-AzureRmSqlDatabaseRestorePoints][]
- [New-AzureRmSqlDatabase][]
- [Remove-AzureRmSqlDatabase][]
- [Restore-AzureRmSqlDatabase][] 
- [Resume-AzureRmSqlDatabase][]
- [Select-AzureRmSubscription][]
- [Set-AzureRmSqlDatabase][]
- [Suspend-AzureRmSqlDatabase][]

## Étapes suivantes
Pour plus d’exemples PowerShell, consultez :

- [Création de SQL Data Warehouse à l’aide de Powershell][]
- [Restaurer à partir d’un instantané][]
- [Géo-restauration à partir d’un instantané][]

Pour obtenir la liste de toutes les tâches pouvant être automatisées avec PowerShell, consultez [Azure SQL Database Cmdlets][] (Applets de commande de la base de données SQL Azure). Pour obtenir la liste des tâches pouvant être automatisées avec REST, consultez [Operations for Azure SQL Databases][] (Opérations des bases de données SQL Azure).

<!--Image references-->

<!--Article references-->
[Installation et configuration d’Azure PowerShell]: ./powershell-install-configure.md
[Création de SQL Data Warehouse à l’aide de Powershell]: ./sql-data-warehouse-get-started-provision-powershell.md
[Restaurer à partir d’un instantané]: ./sql-data-warehouse-backup-and-restore-from-snapshot.md
[Géo-restauration à partir d’un instantané]: ./sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md
[Gérer l’évolutivité avec REST]: ./sql-data-warehouse-manage-scale-out-tasks-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://msdn.microsoft.com/library/mt574084.aspx
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
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

<!---HONumber=AcomDC_0525_2016-->