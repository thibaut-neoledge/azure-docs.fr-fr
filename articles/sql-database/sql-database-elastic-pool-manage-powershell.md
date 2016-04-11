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
    ms.date="03/15/2016"
    ms.author="sstein"/>

# Surveiller, gérer et dimensionner un pool de base de données élastique avec PowerShell 

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


Découvrez comment gérer un [pool de bases de données élastique](sql-database-elastic-pool.md) avec des applets de commande PowerShell.

Pour connaître les codes d’erreur courants, consultez la page [Codes d’erreur SQL pour les applications clientes SQL Database : erreur de connexion à la base de données et autres problèmes](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Les pools élastiques de bases de données sont actuellement en version préliminaire et uniquement disponibles avec des serveurs SQL Database V12. Si vous disposez d’un serveur SQL Database V11, vous pouvez [utiliser PowerShell pour effectuer une mise à niveau vers V12 et créer un pool](sql-database-upgrade-server-portal.md) en une seule étape.

Vous devez utiliser Azure PowerShell 1.0 ou une version supérieure. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).




## Créer une base de données élastique dans un pool

Pour créer une base de données directement à l’intérieur d’un pool, utilisez l’applet de commande [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) et définissez le paramètre **ElasticPoolName**.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Déplacer une base de données autonome vers un pool

Pour placer une base de données dans un pool, utilisez l’applet de commande [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) et définissez le paramètre **ElasticPoolName**.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## Modifier les paramètres de performances d’un pool

Pour modifier les paramètres de performances d’un pool, utilisez l’applet de commande [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx).

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## Obtenir l’état des opérations de pool

Vous pouvez suivre l’état des opérations de pool, notamment la création et les mises à jour, à l’aide de l’applet de commande [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx).

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## Obtenir l’état du déplacement d’une base de données élastique en direction et à partir d’un pool

Vous pouvez suivre l’état d’opérations de bases de données élastiques, notamment la création et les mises à jour, à l’aide de l’applet de commande [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx).

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## Obtenir les données d’utilisation d’un pool

Mesures récupérables sous la forme d'un pourcentage de la limite de pool de ressources :

* Utilisation moyenne du processeur - cpu\_percent 
* Utilisation moyenne des E/S - data\_io\_percent 
* Utilisation moyenne du journal - log\_write\_percent 
* Utilisation moyenne de la mémoire - memory\_percent 
* Utilisation moyenne d'eDTU (en tant que valeur maximale de l'utilisation du processeur, des E/S, du journal) – DTU\_percent 
* Nombre maximum de requêtes utilisateur simultanées (travaux) – max\_concurrent\_requests 
* Nombre maximum de sessions utilisateur simultanées – max\_concurrent\_sessions 
* Taille de stockage totale pour le pool élastique – storage\_in\_megabytes 


Granularité des mesures/périodes de rétention :

* Les données seront renvoyées avec une granularité de 5 minutes.  
* La durée de conservation des données est de 14 jours.  


Cette applet de commande et API limite le nombre de lignes pouvant être récupérées au cours d'un seul appel à 1 000 (environ 3 jours de données avec une granularité de 5 minutes). Mais cette commande peut être appelée plusieurs fois avec des intervalles de temps de début / fin différents pour récupérer plus de données.


Obtenez les mesures :

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Obtenez des jours supplémentaires en répétant l'appel et en ajoutant les données :

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
Formatez la table :

    $table = Format-MetricsAsTable $metrics 

Exportez vers un fichier CSV :

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## Obtenir des mesures de consommation de ressources pour une base de données élastique

Ces API sont les mêmes que les API actuelles (V12) utilisées pour surveiller l'utilisation des ressources d'une base de données autonome, à l'exception de la différence sémantique suivante :

* dans ce cas, les mesures d'API sont obtenues sous forme de pourcentage de DTU MAX par base de données (databaseDtuMax) (ou le nombre maximal équivalent pour la mesure sous-jacente telle que le processeur, les E/S, etc.) défini pour ce pool. Par exemple, une utilisation de 50 % de l'une de ces mesures indique que la consommation des ressources spécifiques est de 50 % de la limite supérieure par base de données définie pour cette ressource dans le pool parent. 

Obtenez les métriques :

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

Le cas échéant, obtenez des jours supplémentaires en répétant l'appel et en ajoutant les données :

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

Formatez la table :

    $table = Format-MetricsAsTable $metrics 

Exportez vers un fichier CSV :

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## Exemple PowerShell de surveillance et de gestion d’un pool


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## Étapes suivantes

- [Créer des tâches élastiques](sql-database-elastic-jobs-overview.md) : les tâches élastiques vous permettent d’exécuter des scripts T-SQL, quel que soit le nombre de bases de données contenues dans le pool.


## Référence des bases de données élastiques

Pour en savoir plus sur les bases de données et les pools de bases de données élastiques, y compris les détails des API et des erreurs, consultez [Référence du pool de bases de données élastique](sql-database-elastic-pool-reference.md).

<!---HONumber=AcomDC_0330_2016-->