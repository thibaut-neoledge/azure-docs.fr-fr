<properties
   pageTitle="Tâches de mises à l’échelle des ressources de calcul dans Azure SQL Data Warehouse | Microsoft Azure"
   description="Découvrez comment interrompre (suspendre) ou démarrer (reprendre) des ressources de calcul pour une base de données Azure SQL Data Warehouse et comment augmenter ou diminuer le paramètre DWU pour l'objectif de niveau de service (SLO). Ces tâches utilisent les applets de commande Azure PowerShell."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/21/2016"
   ms.author="barbkess;sonyama"/>

# Tâches de mises à l’échelle (augmentation ou diminution) des ressources de calcul dans Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Portail Azure](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)


Mettez à l'échelle de façon élastique les ressources de calcul et la mémoire pour répondre aux besoins changeants de votre charge de travail, et réduisez vos coûts en réduisant les ressources pendant les heures creuses.

Cette collection de tâches utilise des applets de commande PowerShell pour :

- Interrompre des ressources de calcul
- Reprendre des ressources de calcul
- Modifier les ressources de calcul en ajustant les unités DWU

## Avant de commencer

### Installer la dernière version d’Azure PowerShell

> [AZURE.NOTE]  Pour utiliser Azure PowerShell avec SQL Data Warehouse, vous devez installer Azure PowerShell version 1.0.3 ou supérieure. Pour vérifier votre version, exécutez la commande **Get-Module -ListAvailable -Name Azure**. Vous pouvez installer la version la plus récente à partir de [Microsoft Web Platform Installer][]. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell][].

### Prise en main des applets de commande Azure PowerShell

Pour commencer :

1. Ouvrez Azure PowerShell. 
2. À l’invite de PowerShell, exécutez les commandes suivantes pour vous connecter à Azure Resource Manager et sélectionnez votre abonnement.

    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## Tâche 1 : Suspendre le calcul

[AZURE.INCLUDE [Description de la suspension de SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’applet de commande [Suspend-AzureRmSqlDatabase][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est interrompue. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

> [AZURE.NOTE] Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Puis il redirige l’objet récupéré vers [Suspend-AzureRmSqlDatabase][]. Les résultats sont stockés dans l'objet resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Tâche 2 : Reprendre le calcul

[AZURE.INCLUDE [Description de la reprise de SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’applet de commande [Resume-AzureRmSqlDatabase][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est démarrée. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Il redirige ensuite l'objet [Resume-AzureRmSqlDatabase][] et stocke les résultats dans $resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```


## Tâche 3 : Mettre à l'échelle les unités DWU

[AZURE.INCLUDE [Description de la mise à l’échelle des unités DWU SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU, utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase][]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

## Étapes suivantes

Pour d'autres tâches de gestion, consultez la rubrique [Vue d'ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Vue d'ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0427_2016-->