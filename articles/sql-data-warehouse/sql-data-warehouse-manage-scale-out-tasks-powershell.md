<properties
   pageTitle="Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (PowerShell) | Microsoft Azure"
   description="Tâches Powershell permettant une montée en charge des performances de l’Azure SQL Data Warehouse. Modifiez les ressources de calcul en ajustant les unités DWU Ou suspendez et reprenez des ressources de calcul pour réduire les coûts."
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
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gestion des tâches d’évolutivité de l’Azure SQL Data Warehouse (PowerShell)

> [AZURE.SELECTOR]
- [Vue d'ensemble](sql-data-warehouse-overview-scalability.md)
- [Portail](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

Mettez à l'échelle de façon élastique les ressources de calcul et la mémoire pour répondre aux besoins changeants de votre charge de travail, et réduisez vos coûts en réduisant les ressources pendant les heures creuses.

Cette collection de tâches utilise des applets de commande PowerShell pour :

- Mise à l’échelle des performances en ajustant les unités DWU
- Interrompre des ressources de calcul
- Reprendre des ressources de calcul

Pour en savoir plus, consultez la [vue d’ensemble de l’évolutivité des performances][] (en anglais).

<a name="before-you-begin-bk"></a>

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

<a name="scale-performance-bk"></a>

## Mise à l’échelle des performances

[AZURE.INCLUDE [Description de la mise à l’échelle des unités DWU SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les unités DWU, utilisez l’applet de commande PowerShell [Set-AzureRmSqlDatabase][]. L'exemple suivant définit l'objectif de niveau de service sur DW1000 pour la base de données MySQLDW hébergée sur le serveur MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## Suspension du calcul

[AZURE.INCLUDE [Description de la suspension de SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Pour suspendre une base de données, utilisez l’applet de commande [Suspend-AzureRmSqlDatabase][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est interrompue. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

> [AZURE.NOTE] Si votre serveur est nommé foo.database.windows.net, utilisez « foo » en tant que nom du serveur dans les applets de commande PowerShell.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Puis il redirige l’objet récupéré vers [Suspend-AzureRmSqlDatabase][]. Les résultats sont stockés dans l'objet resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## Reprise du calcul

[AZURE.INCLUDE [Description de la reprise de SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Pour démarrer une base de données, utilisez l’applet de commande [Resume-AzureRmSqlDatabase][]. Dans l’exemple suivant, une base de données appelée Database02 et hébergée sur un serveur appelé Server01 est démarrée. Le serveur est un groupe de ressources Azure appelé ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Une variante, l'exemple suivant récupère la base de données dans l'objet $database. Il redirige ensuite l'objet [Resume-AzureRmSqlDatabase][] et stocke les résultats dans $resultDatabase. La dernière commande affiche les résultats.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## Étapes suivantes

Pour d'autres tâches de gestion, consultez la [vue d'ensemble de la gestion][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[vue d'ensemble de la gestion]: ./sql-data-warehouse-overview-manage.md
[vue d’ensemble de l’évolutivité des performances]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->