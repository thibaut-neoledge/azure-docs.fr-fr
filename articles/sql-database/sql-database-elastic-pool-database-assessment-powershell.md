<properties
	pageTitle="Script PowerShell pour identifier les bases de données uniques adaptées à un pool | Microsoft Azure"
	description="Un pool de bases de données élastique est un ensemble de ressources disponibles partagé par un groupe de bases de données élastiques. Ce document fournit un script PowerShell pour vous aider à évaluer la pertinence de l’utilisation d’un pool de bases de données élastique pour un groupe de bases de données."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/22/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>

# Script PowerShell pour identifier les bases de données adaptées à un pool de bases de données élastique

L’exemple de script PowerShell de cet article estime les valeurs d’eDTU globales pour les bases de données utilisateur sur un serveur de base de données SQL. Le script collecte des données pendant son exécution et, pour une charge de travail de production classique, vous devez exécuter le script pendant au moins un jour. Idéalement, exécutez le script pendant une durée qui représente la charge de travail classique de vos bases de données, autrement dit suffisamment longtemps pour capturer des données représentant une utilisation normale et une utilisation maximale des bases de données. L’exécution du script pendant une semaine ou même plus longtemps donnera probablement une estimation plus précise.

Ce script est particulièrement utile pour l’évaluation des bases de données sur les serveurs v11, où les pools ne sont pas pris en charge, pour la migration vers les serveurs v12, où les pools sont pris en charge. Sur les serveurs v12, la base de données SQL dispose d’une intelligence intégrée qui analyse la télémétrie de l’historique de l’utilisation et recommande un pool quand il s’avère plus rentable. Pour plus d’informations sur l’utilisation de cette fonctionnalité, consultez [Surveiller, gérer et dimensionner un pool de bases de données élastique](sql-database-elastic-pool-manage-portal.md)

> [AZURE.IMPORTANT] Vous devez conserver la fenêtre PowerShell ouverte pendant l'exécution du script. Ne fermez pas la fenêtre PowerShell tant que vous n’avez pas exécuté le script pendant la durée nécessaire.

## Composants requis 

Installez les éléments suivants avant d'exécuter le script :

- La dernière version de Microsoft Azure PowerShell. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
- le [pack de fonctionnalités SQL Server 2014](https://www.microsoft.com/download/details.aspx?id=42295).

### Détails du script

Vous pouvez exécuter le script à partir de votre ordinateur local ou d’un ordinateur virtuel sur le cloud. Lorsque vous l’exécutez à partir de votre ordinateur local, vous risquez de subir des frais de sortie de données, car le script a besoin de télécharger des données depuis vos bases de données cibles. Vous trouverez ci-dessous une estimation du volume de données en fonction du nombre de bases de données cibles et de la durée d'exécution du script. Pour les coûts de transfert de données Azure, consultez [Détails de la tarification de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/).
       
 -     1 base de données par heure = 38 Ko
 -     1 base de données par jour = 900 Ko
 -     1 base de données par semaine = 6 Mo
 -     100 bases de données par jour = 90 Mo
 -     500 bases de données par semaine = 3 Go

Le script exclut les bases de données qui ne sont pas de bons candidats à l’offre de version préliminaire publique actuelle de la couche standard. Si vous avez besoin d’exclure d’autres bases de données du serveur cible, modifiez le script pour qu’il réponde à vos critères. Par défaut, le script ne compile pas d'informations pour les éléments suivants :

* bases de données élastiques (bases de données déjà présentes dans un pool élastique) ;
* base de données MASTER du serveur.

Le script a besoin d’une base de données de sortie pour stocker les données intermédiaires en vue de l'analyse. Vous pouvez utiliser une base de données nouvelle ou existante. Bien qu’elle ne soit pas techniquement nécessaire pour exécuter l'outil, la base de données de sortie doit se trouver sur un autre serveur pour ne pas affecter le résultat de l'analyse. Le niveau de performance de la base de données de sortie doit être au moins égal à S0 ou supérieur. Lors de la collecte de données pour un grand nombre de bases de données pendant une longue période, vous pouvez envisager de mettre à niveau votre base de données de sortie vers un niveau de performance supérieur.

Le script a besoin que vous fournissiez les informations d’identification permettant de se connecter au serveur cible (le candidat au pool de bases de données élastique) avec un nom complet du serveur comme <*nom\_bd*>**.database.windows.net**. Le script ne prend pas en charge l’analyse de plusieurs serveurs à la fois.

Après avoir soumis les valeurs du jeu de paramètres initial, vous êtes invité à vous connecter à votre compte Azure. Il s'agit de vous connecter à votre serveur cible, et non de vous connecter au serveur de la base de données de sortie.
	
Si vous rencontrez les avertissements suivants lors de l'exécution du script, vous pouvez les ignorer :

- AVERTISSEMENT : l'applet de commande Switch-AzureMode est déconseillée.
- AVERTISSEMENT : impossible d'obtenir des informations sur le service SQL Server. Une tentative de connexion à WMI sur « Microsoft.Azure.Commands.Sql.dll » a échoué avec l'erreur suivante : le serveur RPC n'est pas disponible.

Une fois le script terminé, il génère le nombre d’eDTU estimé nécessaire pour qu’un pool puisse contenir toutes les bases de données candidates sur le serveur cible. Ce nombre d’eDTU estimé permet de créer et de configurer le pool. Une fois que le pool est créé et que les bases de données ont été déplacées dans le pool, surveillez étroitement le pool pendant quelques jours et effectuez des réglages de la configuration des eDTU du pool en fonction des besoins. Consultez [Surveiller, gérer et dimensionner un pool de bases de données élastique](sql-database-elastic-pool-manage-portal.md).


   [AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]
    
    param (
	[Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
	[Parameter(Mandatory=$true)][string]$username, # user name
	[Parameter(Mandatory=$true)][string]$serverPassword, # password
	[Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
	[Parameter(Mandatory=$true)][string]$outputdatabaseName,
	[Parameter(Mandatory=$true)][string]$outputDBUsername,
	[Parameter(Mandatory=$true)][string]$outputDBpassword,
	[Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
	)
	
	Login-AzureRmAccount
	Select-AzureSubscription $AzureSubscriptionName
	
	$server = Get-AzureRmSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	
	# Check version/upgrade status of the server
	$upgradestatus = Get-AzureRmSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	$version = ""
	if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
	{
	$version = $upgradestatus.Status
	}
	else
	{
	$version = $server.ServerVersion
	}
	
	# For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
	$ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
	
	$outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
	$destinationTableName = "resource_stats_output"
	
	# Create a table in output database for metrics collection
	$sql = "
	IF  NOT EXISTS (SELECT * FROM sys.objects 
	WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
	
	BEGIN
	Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
	Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
	END
	TRUNCATE TABLE $($destinationTableName);
	"
	Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
	
	# waittime (minutes) is interval between data collection queries in the loop below.
	$Waittime = 10
	$end_Time = [DateTime]::UtcNow
	$start_time = $end_time.AddMinutes(-$Waittime)
	$finish_time = $end_Time.AddMinutes($duration_minutes)
	
	While ($end_time -lt $finish_time)
	{
	Write-Host "Collecting metrics..." 
	foreach ($db in $ListOfDBs)
	{
	if ($version -in ("12.0", "Completed")) # for V12 databases 
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'S3' THEN 100
	WHEN 'P1' THEN 125
	WHEN 'P2' THEN 250
	WHEN 'P3' THEN 1000
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	else
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'P1' THEN 100
	WHEN 'P2' THEN 200
	WHEN 'P3' THEN 800
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	
	$result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
	#bulk copy the metrics to output database
	$bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
	$bulkCopy.BulkCopyTimeout = 600
	$bulkCopy.DestinationTableName = "$destinationTableName";
	$bulkCopy.WriteToServer($result);
	
	}
	
	$start_time = $start_time.AddMinutes($Waittime)
	$end_time = $end_time.AddMinutes($Waittime)
	Write-Host $start_time
	Write-Host $end_time
	do {
	Start-Sleep 1
	   }
	until (([DateTime]::UtcNow) -ge $end_time)
	}
	
	Write-Host "Analyzing the collected metrics...."
	# Analysis query that does aggregation of the resource metrics to calculate pool size.
	$sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
	WITH group_stats AS
	(
	SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
	FROM resource_stats_output 
	WHERE slo LIKE '
	
	$sql2 = '
	GROUP BY end_time
	)
	-- calculate aggregate storage and DTUs for all DBs in the group
	, group_DTU AS
	(
	SELECT end_time, avg_group_Storage, 
	(SELECT Max(v)
	   FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
	FROM group_stats
	)
	-- Get top 1 percent of the storage and DTU utilization samples.
	, top1_percent AS (
	SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
	)
	
	-- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
	--SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
	SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
	IF @DTU_Storage > @DTU_Perf_99 
	SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
	ELSE 
	SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
	
	#check if there are any web/biz edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Shared*")
	{
	write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any basic edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Basic*")
	{
	write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Basic%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]} 
	}
	
	#check if there are any standard edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "S*")
	{
	write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any premium edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "P*")
	{
	write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'P%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
}

        

<!---HONumber=AcomDC_0928_2016-->