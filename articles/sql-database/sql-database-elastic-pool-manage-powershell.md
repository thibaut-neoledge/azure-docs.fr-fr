<properties 
    pageTitle="Gérer un pool de bases de données élastique (PowerShell) | Microsoft Azure" 
    description="Découvrez comment utiliser PowerShell pour gérer un pool de bases de données élastique."  
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# Surveiller et gérer un pool de base de données élastique avec PowerShell 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Gérez un [pool de base de données élastique](sql-database-elastic-pool.md) avec des applets de commande PowerShell.

Pour connaître les codes d’erreur courants, consultez la page [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md).

Vous trouverez les valeurs pour les pools dans la section [Limites relatives aux eDTU et au stockage](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

## Composants requis
* Azure PowerShell 1.0 ou version ultérieure. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
* Les pools de bases de données élastiques sont uniquement disponibles sur les serveurs de base de données SQL V12. Si vous disposez d’un serveur de base de données SQL V11, [utilisez PowerShell pour effectuer une mise à niveau vers la version V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.


## Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou retirer une base de données d’un pool avec l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx).

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Modifier les paramètres de performances d’un pool

Lorsque les performances sont insuffisantes, vous pouvez modifier les paramètres du pool pour faire face à l’augmentation de l’activité. Utilisez l’applet de commande [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx). Définissez la valeur du paramètre -Dtu sur le nombre d’eDTU par pool. Consultez la section [Limites relatives aux eDTU et au stockage](sql-database-elastic-pool#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases) pour connaître les valeurs acceptées.

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Obtenir l’état des opérations de pool

Créer un pool peut prendre un certain temps. Pour suivre l’état des opérations de pool, notamment la création et les mises à jour, utilisez l’applet de commande [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Obtenir l’état du déplacement d’une base de données élastique en direction et à partir d’un pool

Déplacer une base de données peut prendre un certain temps. Pour suivre l’état du déplacement, utilisez l’applet de commande [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Obtenir les données d’utilisation des ressources d’un pool

Mesures récupérables sous la forme d'un pourcentage de la limite de pool de ressources :


| Nom de métrique | Description |
| :-- | :-- |
| cpu\_percent | Utilisation moyenne des ressources de calcul en pourcentage de la limite du pool. |
| physical\_data\_read\_percent | Utilisation moyenne des E/S en pourcentage de la limite du pool. |
| log\_write\_percent | Utilisation moyenne des ressources d’écriture en pourcentage de la limite du pool. | 
| DTU\_consumption\_percent | Utilisation moyenne des eDTU en pourcentage de la limite d’eDTU du pool. | 
| storage\_percent | Utilisation moyenne du stockage en pourcentage de la limite de stockage du pool. |  
| workers\_percent | Nombre maximal d’ouvriers simultanés (demandes) en pourcentage de la limite du pool. |  
| sessions\_percent | Nombre maximal de sessions simultanées en pourcentage de la limite du pool. | 
| eDTU\_limit | Nombre maximal de DTU de pool élastique pour ce pool élastique au cours de cet intervalle. |
| storage\_limit | Espace de stockage de pool élastique maximal pour ce pool élastique (en Mo) au cours de cet intervalle. |
| eDTU\_used | Nombre moyen d’eDTU utilisées par le pool au cours de cet intervalle. |
| storage\_used | Espace de stockage moyen utilisé par le pool au cours de cet intervalle (en octets). |

**Granularité des mesures/périodes de rétention :**

* Les données seront renvoyées avec une granularité de 5 minutes.  
* La durée de conservation des données est de 14 jours.  

Cette applet de commande et API limite le nombre de lignes pouvant être récupérées au cours d'un seul appel à 1 000 (environ 3 jours de données avec une granularité de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps de début / fin différents pour récupérer plus de données.

Pour obtenir les mesures :

	$metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## Obtenir les données d’utilisation des ressources d’une base de données élastique

Ces API sont les mêmes que les API actuelles (V12) utilisées pour surveiller l’utilisation des ressources d’une base de données autonome, à l’exception de la différence sémantique suivante.

Dans ce cas, les mesures d’API sont obtenues sous forme de pourcentage du nombre maximal d’eDTU par base de données (ou du nombre maximal équivalent pour la mesure sous-jacente telle que le processeur, les E/S, etc.) défini pour ce pool. Par exemple, une utilisation de 50 % de l’une de ces mesures indique que la consommation des ressources spécifiques est de 50 % de la limite supérieure par base de données définie pour cette ressource dans le pool parent.

Pour obtenir les mesures :

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## Exemple : obtenir des mesures de consommation des ressources pour un pool et ses bases de données

Cet exemple consiste à récupérer les mesures de consommation pour un pool donné et l’ensemble de ses bases de données. Les données collectées sont mises en forme et écrites dans un fichier au format .csv. Le fichier peut être consulté à l’aide d’Excel.

	$subscriptionId = '<Azure subscription id>'	      # Azure subscription ID
	$resourceGroupName = '<resource group name>'             # Resource Group
	$serverName = <server name>                              # server name
	$poolName = <elastic pool name>                          # pool name
		
	# Login to Azure account and select the subscription.
	Login-AzureRmAccount
	Set-AzureRmContext -SubscriptionId $subscriptionId
	
	# Get resource usage metrics for an elastic pool for the specified time interval.
	$startTime = '4/27/2016 00:00:00'  # start time in UTC
	$endTime = '4/27/2016 01:00:00'    # end time in UTC
	
	# Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
	$poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
	$poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 
	
	# Get the list of databases in this pool.
	$dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName
	
	# Get resource usage metrics for a database in an elastic database for the specified time interval.
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
          }
          $table = $table += $sx
      }
    }
    
    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
	}
	
	# Format and output pool metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv
	
	# Format and output database metrics
	Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## Latence des opérations du pool élastique

- En général, le processus de modification du nombre minimal d’eDTU par base de données ou du nombre maximal d’eDTU par base de données prend 5 minutes au maximum.
- Le processus de modification du nombre d’eDTU par pool dépend quant à lui de la quantité totale d’espace utilisé par toutes les bases de données du pool. Ce processus prend en moyenne 90 minutes au maximum, pour chaque tranche de 100 Go. Par exemple, si l’espace total utilisé par toutes les bases de données du pool est égal à 200 Go, une opération de modification du nombre d’eDTU par pool prend 3 heures au maximum.

## Effectuer une migration des serveurs V11 vers les serveurs V12

Des applets de commande PowerShell sont disponibles pour démarrer, arrêter ou surveiller une mise à niveau vers Azure SQL Database V12, à partir d’une version 11 ou antérieure.

- [Mise à niveau vers SQL Database V12 à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour obtenir une documentation de référence sur ces applets de commande Powershell, consultez :


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


L’applet de commande Stop- signifie « annuler », et non « interrompre ». Il est impossible de reprendre une mise à niveau en cours. Il faut la recommencer au début. L’applet de commande Stop- nettoie et libère toutes les ressources appropriées.

## Étapes suivantes

- [Créer des tâches élastiques](sql-database-elastic-jobs-overview.md) : les tâches élastiques vous permettent d’exécuter des scripts T-SQL, quel que soit le nombre de bases de données contenues dans le pool.
- Consultez l’article [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md) : utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.

<!---HONumber=AcomDC_0504_2016-->