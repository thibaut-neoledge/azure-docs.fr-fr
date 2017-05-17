---
title: "PowerShell : Créer et gérer un pool élastique Azure SQL | Microsoft Docs"
description: "Découvrez comment utiliser PowerShell pour gérer un pool élastique."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 61289770-69b9-4ae3-9252-d0e94d709331
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 04/10/2017
ms.author: srinia
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: b84185d0f224352e7cf5449068bee359914bd4ed
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---

# <a name="create-and-manage-an-elastic-pool-with-powershell"></a>Créer et gérer un pool élastique avec PowerShell
Cette rubrique montre comment créer et gérer des [pools élastiques](sql-database-elastic-pool.md) évolutifs avec PowerShell.  Vous pouvez également créer et gérer un pool élastique Azure avec le [portail Azure](https://portal.azure.com/), l’API REST ou [C#](sql-database-elastic-pool-manage-csharp.md). Vous pouvez également créer et déplacer des bases de données vers et depuis des pools élastiques à l’aide de [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-elastic-pool"></a>Créer un pool élastique
L’applet de commande [New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) permet de créer un pool élastique. Les valeurs correspondant au nombre d’eDTU par pool, ainsi qu’au nombre de DTU minimal et maximal, sont limitées par la valeur de niveau de service (De base, Standard, Premium ou Premium RS). Consultez l’article [Limites relatives aux eDTU et au stockage pour les pools de bases de données](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools).

```PowerShell
New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100
```

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>Créer une base de données regroupée dans un pool élastique
Utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) et définissez le paramètre **ElasticPoolName** sur le pool cible. Pour déplacer une base de données existante vers un pool élastique, consultez l’article [Déplacer une base de données dans un pool élastique](sql-database-elastic-pool-manage-powershell.md#move-a-database-into-an-elastic-pool).

```PowerShell
New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"
```

### <a name="complete-script"></a>Terminer le script
Ce script crée un groupe de ressources Azure et un serveur. Lorsque vous y êtes invité, fournissez un nom d’utilisateur de l’administrateur et un mot de passe pour le nouveau serveur (différents de vos informations d’identification Azure).

```PowerShell
$subscriptionId = '<your Azure subscription id>'
$resourceGroupName = '<resource group name>'
$location = '<datacenter location>'
$serverName = '<server name>'
$poolName = '<pool name>'
$databaseName = '<database name>'

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB
```

## <a name="create-an-elastic-pool-and-add-multiple-pooled-databases"></a>Créer un pool élastique et ajouter plusieurs bases de données regroupées
La création d’un grand nombre de bases de données dans un pool élastique peut prendre du temps si elle se fait par le biais du portail ou d’applets de commande PowerShell qui créent une seule base de données à la fois. Pour automatiser la création dans un pool élastique, consultez l’article [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="move-a-database-into-an-elastic-pool"></a>Déplacer une base de données dans un pool élastique
Vous pouvez ajouter ou retirer une base de données d’un pool élastique avec l’applet de commande [Set-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlelasticpool).

```PowerShell
Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"
```

## <a name="change-performance-settings-of-an-elastic-pool"></a>Modifier les paramètres de performances d'un pool élastique
Lorsque les performances sont insuffisantes, vous pouvez modifier les paramètres du pool pour faire face à l’augmentation de l’activité. Utilisez l’applet de commande [Set-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlelasticpool) . Définissez la valeur du paramètre -Dtu sur le nombre d’eDTU par pool. Consultez la section [Limites relatives aux eDTU et au stockage](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) pour connaître les valeurs acceptées.

```PowerShell
Set-AzureRmSqlElasticPool -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1” -Dtu 1200 -DatabaseDtuMax 100 -DatabaseDtuMin 50
```

## <a name="change-the-storage-limit-for-an-elastic-pool"></a>Modifier la limite de stockage d’un pool élastique

Utilisez la cmdlet [Set-AzureRmSqlElasticPool](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlelasticpool) pour définir le paramètre _-StorageMB_. Indiquez la limite de stockage en Mo (p. ex. 2097152 définit la limite de stockage sur 2 To). Consultez la section [Limites relatives aux eDTU et au stockage](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools) pour connaître les valeurs acceptées.

> [!IMPORTANT]
> Le stockage de données max. par défaut par pool pour les pools Premium avec 1 500 eDTU ou plus est de 750 Go. Pour obtenir la _taille de stockage de données max. par pool_ la plus élevée, la limite de stockage doit être définie explicitement. Les pools Premium avec plus de 750 Go de stockage sont actuellement en version préliminaire publique dans les régions suivantes : Est des États-Unis 2, États-Unis de l’Ouest, Europe de l’Ouest, Asie du Sud-Est, Japon de l’Est, Est de l’Australie, Canada Centre et Canada Est.

```PowerShell
Set-AzureRmSqlElasticPool -ServerName "server1" -ElasticPoolName “elasticpool1” -StorageMB 2097152
```

## <a name="get-the-status-of-pool-operations"></a>Obtenir l’état des opérations de pool
Créer un pool élastique peut prendre un certain temps. Pour suivre l’état des opérations de pool, notamment la création et les mises à jour, utilisez l’applet de commande [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812\(v=azure.300\).aspx).

```PowerShell
Get-AzureRmSqlElasticPoolActivity -ResourceGroupName “resourcegroup1” -ServerName “server1” -ElasticPoolName “elasticpool1”
```

## <a name="get-the-status-of-moving-a-database-into-and-out-of-an-elastic-pool"></a>Obtenir l’état du déplacement d’une base de données en direction et à partir d’un pool élastique
Déplacer une base de données peut prendre un certain temps. Pour suivre l’état du déplacement, utilisez l’applet de commande [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687\(v=azure.300\).aspx).

```PowerShell
Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"
```

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>Obtenir les données d’utilisation des ressources d’un pool élastique
Mesures récupérables sous la forme d'un pourcentage de la limite de pool de ressources :

| Nom de métrique | Description |
|:--- |:--- |
| cpu\_percent |Utilisation moyenne des ressources de calcul en pourcentage de la limite du pool. |
| physical\_data\_read\_percent |Utilisation moyenne des E/S en pourcentage de la limite du pool. |
| log\_write\_percent |Utilisation moyenne des ressources d’écriture en pourcentage de la limite du pool. |
| DTU\_consumption\_percent |Utilisation moyenne des eDTU en pourcentage de la limite d’eDTU du pool. |
| storage\_percent |Utilisation moyenne du stockage en pourcentage de la limite de stockage du pool. |
| workers\_percent |Nombre maximal d’ouvriers simultanés (demandes) en pourcentage de la limite du pool. |
| sessions\_percent |Nombre maximal de sessions simultanées en pourcentage de la limite du pool. |
| eDTU_limit |Nombre maximal de DTU de pool élastique pour ce pool élastique au cours de cet intervalle. |
| storage\_limit |Espace de stockage de pool élastique maximal pour ce pool élastique (en Mo) au cours de cet intervalle. |
| eDTU\_used |Nombre moyen d’eDTU utilisées par le pool au cours de cet intervalle. |
| storage\_used |Espace de stockage moyen utilisé par le pool au cours de cet intervalle (en octets). |

**Granularité des mesures/périodes de rétention :**

* Les données seront renvoyées avec une granularité de 5 minutes.  
* La durée de conservation des données est de 35 jours.  

Cette applet de commande et API limite le nombre de lignes pouvant être récupérées au cours d'un seul appel à 1000 (environ 3 jours de données avec une granularité de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps de début / fin différents pour récupérer plus de données.

Pour obtenir les mesures :

```PowerShell
$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")
```

## <a name="get-resource-usage-data-for-a-database-in-an-elastic-pool"></a>Afficher les données d’utilisation des ressources d’une base de données dans un pool élastique
Ces API sont les mêmes que celles utilisées pour analyser l’utilisation des ressources d’une base de données, à l’exception de la différence sémantique suivante : les métriques récupérées sont exprimées sous forme d’un pourcentage du nombre maximal d’eDTU par base de données (ou du nombre maximal équivalent pour la métrique sous-jacente telle que le processeur ou les E/S) défini pour ce pool. Par exemple, une utilisation de 50 % de l’une de ces mesures indique que la consommation des ressources spécifiques est de 50 % de la limite supérieure par base de données définie pour cette ressource dans le pool parent.

Pour obtenir les mesures :

```PowerShell
$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")
```

## <a name="add-an-alert-to-an-elastic-pool-resource"></a>Ajouter une alerte à une ressource de pool élastique
Vous pouvez ajouter des règles d’alerte à un pool élastique qui envoient des notifications par message électronique ou des chaînes d’alertes à des [points de terminaison d’URL](https://msdn.microsoft.com/library/mt718036.aspx) quand le pool élastique atteint un seuil d’utilisation que vous configurez. Utilisez l’applet de commande Add-AzureRmMetricAlertRule.

> [!IMPORTANT]
> La surveillance de l’utilisation des ressources pour les pools élastiques subit un décalage d’au moins 20 minutes. La définition d’alertes de moins de 30 minutes pour les pools élastiques n’est actuellement pas prise en charge. Les alertes définies pour les pools élastiques sur une période (paramètre appelé « -WindowSize » dans les API PowerShell) de moins de 30 minutes peuvent ne pas se déclencher. Assurez-vous que les alertes que vous définissez pour les pools élastiques utilisent une période (WindowSize) de 30 minutes ou plus.
>
>

Cet exemple ajoute une alerte pour recevoir une notification lorsque la consommation eDTU d’un pool élastique dépasse un certain seuil.

```PowerShell
# Set up your resource ID configurations
$subscriptionId = '<Azure subscription id>'      # Azure subscription ID
$location =  '<location'                         # Azure region
$resourceGroupName = '<resource group name>'     # Resource Group
$serverName = '<server name>'                    # server name
$poolName = '<elastic pool name>'                # pool name

#$Target Resource ID
$ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

# Create an email action
$actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

# create a unique rule name
$alertName = $poolName + "- DTU consumption rule"

# Create an alert rule for DTU_consumption_percent
Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail
```

## <a name="add-alerts-to-all-databases-in-an-elastic-pool"></a>Ajouter des alertes à toutes les bases de données dans un pool élastique
Vous pouvez ajouter des règles d’alerte à toutes les bases de données dans un pool élastique qui envoient des notifications par message électronique ou des chaînes d’alertes à des [points de terminaison d’URL](https://msdn.microsoft.com/library/mt718036.aspx) quand une ressource atteint un seuil d’utilisation configuré par l’alerte.

> [!IMPORTANT]
> La surveillance de l’utilisation des ressources pour les pools élastiques subit un décalage d’au moins 20 minutes. La définition d’alertes de moins de 30 minutes pour les pools élastiques n’est actuellement pas prise en charge. Les alertes définies pour les pools élastiques sur une période (paramètre appelé « -WindowSize » dans les API PowerShell) de moins de 30 minutes peuvent ne pas se déclencher. Assurez-vous que les alertes que vous définissez pour les pools élastiques utilisent une période (WindowSize) de 30 minutes ou plus.
>
>

Cet exemple ajoute une alerte à chacune des bases de données dans un pool élastique pour obtenir une notification lorsque la consommation DTU de cette base de données dépasse un certain seuil.

```PowerShell
# Set up your resource ID configurations
$subscriptionId = '<Azure subscription id>'      # Azure subscription ID
$location = '<location'                          # Azure region
$resourceGroupName = '<resource group name>'     # Resource Group
$serverName = '<server name>'                    # server name
$poolName = '<elastic pool name>'                # pool name

# Get the list of databases in this pool.
$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

# Create an email action
$actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

# Get resource usage metrics for a database in an elastic pool for the specified time interval.
foreach ($db in $dbList)
{
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
}
```

## <a name="collect-and-monitor-resource-usage-data-across-multiple-pools-in-a-subscription"></a>Collecter et surveiller les données d’utilisation des ressources dans plusieurs pools d’un abonnement
Lorsque vous disposez d’un grand nombre de bases de données dans un abonnement, il est fastidieux d’analyser chaque pool élastique séparément. Au lieu de cela, vous pouvez associer les applets de commande PowerShell de la base de données SQL et les requêtes T-SQL pour collecter les données d’utilisation des ressources de plusieurs pools et de leurs bases de données pour la surveillance et l’analyse de l’utilisation des ressources. Un [exemple d’implémentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools) d’un ensemble de scripts PowerShell de ce type est disponible dans le référentiel d’exemples SQL Server GitHub, accompagné d’une documentation sur sa fonction et son utilisation.

Pour utiliser cet exemple d’implémentation, procédez comme suit.

1. Téléchargez les [scripts et la documentation](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools):
2. Modifiez les scripts pour votre environnement. Spécifiez le ou les serveurs qui hébergent les pools élastiques.
3. Spécifiez une base de données de télémétrie où les métriques collectées doivent être stockées.
4. Personnalisez le script pour spécifier la durée de l’exécution des scripts.

D’un point de vue global, le script effectue les opérations suivantes :

* Il énumère tous les serveurs d’un abonnement Azure donné (ou d’une liste spécifiée de serveurs).
* Il exécute une tâche en arrière-plan pour chaque serveur. La tâche s’exécute en boucle à intervalles réguliers et collecte les données de télémétrie pour tous les pools du serveur. Il charge ensuite les données collectées dans la base de données de télémétrie spécifiée.
* Il énumère la liste des bases de données de chaque pool pour collecter les données d’utilisation des ressources des bases de données. Il charge ensuite les données collectées dans la base de données de télémétrie.

Les métriques collectées chargées dans la base de données de télémétrie peuvent être analysées pour surveiller l’intégrité des pools élastiques et des bases de données qu’ils contiennent. Le script installe également une fonction table prédéfinie dans la base de données de télémétrie pour faciliter l’agrégation des métriques d’une plage de temps spécifiée. Par exemple, les résultats de la fonction table peuvent servir à afficher les N premiers pools élastiques en termes d’utilisation des eDTU au cours d’une plage de temps donnée. Si vous le souhaitez, vous pouvez utiliser des outils analytiques tels que Excel ou Power BI pour interroger et analyser les données collectées.

### <a name="example-retrieve-resource-consumption-metrics-for-an-elastic-pool-and-its-databases"></a>Exemple : obtenir des mesures de consommation des ressources pour un pool élastique et ses bases de données
Cet exemple consiste à récupérer les mesures de consommation pour un pool donné et l’ensemble de ses bases de données. Les données collectées sont mises en forme et écrites dans un fichier au format .csv. Le fichier peut être consulté à l’aide d’Excel.

```PowerShell
$subscriptionId = '<Azure subscription id>'          # Azure subscription ID
$resourceGroupName = '<resource group name>'             # Resource Group
$serverName = <server name>                              # server name
$poolName = <elastic pool name>                          # pool name

# Login to Azure account and select the subscription.
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId

# Get resource usage metrics for an elastic pool for the specified time interval.
$startTime = '4/27/2016 00:00:00'  # start time in UTC
$endTime = '4/27/2016 01:00:00'    # end time in UTC

# Construct the pool resource ID and retrive pool metrics at 5-minute granularity.
$poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
$poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)

# Get the list of databases in this pool.
$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

# Get resource usage metrics for a database in an elastic pool for the specified time interval.
$dbMetrics = @()
foreach ($db in $dbList)
{
     $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
     $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
}

#Optionally you can format the metrics and output as .csv file using the following script block.
$command = {
param($metricList, $outputFile)

# Format metrics into a table.
$table = @()
foreach($metric in $metricList) {
   foreach($metricValue in $metric.MetricValues) {
      $sx = New-Object PSObject -Property @{
      Timestamp = $metricValue.Timestamp.ToString()
      MetricName = $metric.Name;
      Average = $metricValue.Average;
      ResourceID = $metric.ResourceId
   }$table = $table += $sx
   }
}

# Output the metrics into a .csv file.
write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
}

# Format and output pool metrics
Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

# Format and output database metrics
Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv
```

## <a name="latency-of-elastic-pool-operations"></a>Latence des opérations du pool élastique
* En général, le processus de modification du nombre minimal d’eDTU par base de données ou du nombre maximal d’eDTU par base de données prend 5 minutes au maximum.
* Le processus de modification du nombre d’eDTU par pool dépend quant à lui de la quantité totale d’espace utilisé par toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de modification du nombre d’eDTU par pool prend 3 heures au maximum.



## <a name="next-steps"></a>Étapes suivantes
* [Créer des tâches élastiques](sql-database-elastic-jobs-overview.md) : les tâches élastiques vous permettent d’exécuter des scripts T-SQL, quel que soit le nombre de bases de données contenues dans le pool.
* Consultez [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md): utilisez les outils élastiques pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.

