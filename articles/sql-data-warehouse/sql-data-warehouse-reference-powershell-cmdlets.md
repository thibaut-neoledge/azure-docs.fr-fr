<properties
   pageTitle="Applets de commande PowerShell pour Azure SQL Data Warehouse"
   description="Recherchez les principaux applets de commande PowerShell pour Azure SQL Data Warehouse, y compris comment suspendre et reprendre une base de données."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyama"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/02/2016"
   ms.author="sonyama;barbkess;mausher"/>

# Applets de commande PowerShell et API REST pour SQL Data Warehouse

SQL Data Warehouse peut être géré à l’aide d’applets de commande Azure PowerShell ou d’API REST.

La plupart des commandes définies pour la **base de données Azure SQL** sont également utilisées pour **SQL Data Warehouse**. Pour consulter une liste actualisée, accédez à la section [Applets de commande Azure SQL](https://msdn.microsoft.com/library/mt574084.aspx). Les applets de commande [Suspend-AzureRmSqlDatabase][] et [Resume-AzureRmSqlDatabase][] sont des ajouts spécialement conçus pour SQL Data Warehouse.

De la même manière, les API REST dédiés à la **base de données SQL Azure** peuvent également être utilisés avec les instances **SQL Data Warehouse**. Pour consulter une liste actualisée, accédez à la page [Opérations pour les bases de données SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Prise en main des applets de commande Azure PowerShell

1. Pour télécharger le module Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://aka.ms/webpi-azps). Pour plus d’informations sur ce programme d’installation, consultez [Comment installer et configurer Azure PowerShell][].
2. Pour démarrer PowerShell, cliquez sur **Démarrer** et tapez **Windows PowerShell**.
3. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```


## Applets de commande PowerShell fréquemment utilisés

Ces applets de commande PowerShell sont souvent utilisés avec Azure SQL Data Warehouse :


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


## Exemples pour SQL Data Warehouse

Ces exemples s’appliquent à des fonctions qui concernent uniquement SQL Data Warehouse.

### [Suspend-AzureRmSqlDatabase][]

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

### [Resume-AzureRmSqlDatabase][]

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


## Étapes suivantes
Pour plus d’informations, consultez la [vue d’ensemble de référence de SQL Data Warehouse][]. Pour plus d’exemples PowerShell, consultez :
- [Création de SQL Data Warehouse à l’aide de Powershell](sql-data-warehouse-get-started-provision-powershell.md)
- [Restaurer à partir d’un instantané](sql-data-warehouse-backup-and-restore-from-snapshot.md)
- [Géo-restauration à partir d’un instantané](sql-data-warehouse-backup-and-restore-from-geo-restore-snapshot.md)

<!--Image references-->

<!--Article references-->
[vue d’ensemble de référence de SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Comment installer et configurer Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->
[Get-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt603648.aspx
[Get-AzureRmSqlDeletedDatabaseBackup]: https://msdn.microsoft.com/library/mt693387.aspx
[Get-AzureRmSqlDatabaseRestorePoints]: https://msdn.microsoft.com/library/mt603642.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Remove-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619368.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx
[Resume-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619347.aspx
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureRmSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx
[Suspend-AzureRmSqlDatabase]: http://msdn.microsoft.com/library/mt619337.aspx



<!--Other Web references-->

<!---HONumber=AcomDC_0420_2016-->