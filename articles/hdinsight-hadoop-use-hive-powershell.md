<properties
   pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure"
   description="Utilisation de Hadoop Hive via PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Exécution de requêtes Hive à l'aide de PowerShell

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple d'utilisation de PowerShell pour exécuter des requêtes Hive sur un cluster Hadoop sur HDInsight.

> [AZURE.NOTE] Ce document ne fournit pas de description détaillée des instructions HiveQL utilisées dans les exemples. Pour en savoir plus sur le HiveQL utilisé dans cet exemple, consultez la rubrique <a href="../hdinsight-use-hive/" target="_blank">Utilisation de Hive avec Hadoop sur HDInsight</a>.


##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux

* <a href="http://azure.microsoft.com/ documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>Exécution de requêtes Hive à l'aide de PowerShell

Azure PowerShell fournit *cmdlets* qui vous permet d'exécuter à distance des requêtes Hive sur HDInsight. En interne, ceci est accompli à l'aide d'appels REST vers <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat,</a> (anciennement appelé Templeton) exécutés sur le cluster HDInsight.

Les applets de commande suivants sont utilisés lors de l'exécution des requêtes Hive sur un cluster à distance HDInsight.

* **Add-AzureAccount** : authentifie PowerShell à votre abonnement Azure

* **New-AzureHDInsightHiveJobDefinition** : crée une nouvelle *job definition* à l'aide des instructions HiveQL spécifiées

* **Start-AzureHDInsightJob** : envoie la définition de la tâche à HDInsight, lance l'exécution de la tâche, et renvoie un objet *job* qui peut être utilisé pour vérifier l'état de la tâche

* **Wait-AzureHDInsightJob** : utilise l'objet de la tâche pour vérifier l'état de la tâche Il attend que la tâche se termine, ou que le temps d'attente soit dépassé

* **Get-AzureHDInsightJobOutput** : utilisé pour récupérer la sortie de la tâche

* **Invoke-Hive** : utilisé pour exécuter des instructions HiveQL et bloquer celles qui se terminent. Puis, il renvoie les résultats

* **Use-AzureHDInsightCluster** : configure le cluster actuel à utiliser pour la commande **Invoke-Hive**

La procédure suivante vous indique comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster de HDInsight. 

1. À l'aide d'un éditeur, enregistrez le code suivant en tant que**hivejob.ps1**. Vous devez remplacer **CLUSTERNAME** par le nom de votre cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Ouvrez une nouvelle invite **Microsoft Azure PowerShell**. Accédez aux répertoires du fichier **hivejob.ps1**, puis utilisez la commande suivante pour exécuter le script.

		.\hivejob.ps1

7. Une fois la tâche terminée, ceci doit renvoyer des informations similaires à celles-ci :

		Display the standard output...
		[ERROR]	3

4. Comme mentionné précédemment, **Invoke-Hive** peut être utilisé pour exécuter une requête et attendre la réponse. Utilisez les commandes suivantes, en remplaçant **CLUSTERNAME** par le nom de votre cluster.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	La sortie se présente comme suit :

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script Here-Strings ou HiveQL de PowerShell. L'extrait de code suivant montre comment utiliser le l'applet de commande *Invoke-Hive* pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Pour plus d'informations sur Here-Strings, consultez la rubrique <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Utilisation de Here-Strings PowerShell Windows</a>.

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n'est renvoyée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d'erreur relatives à cette tâche, ajoutez les commandes suivantes à la fin du fichier **hivejob.ps1**, enregistrez-le, puis exécutez-le à nouveau.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Ceci renverra l'information écrite vers STDERR sur le serveur lors de l'exécution de la tâche, et pourra vous aider à identifier la raison pour laquelle la tâche a échoué.

##<a id="summary"></a>Résumé

Comme vous pouvez le constater, Azure PowerShell permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](../hdinsight-use-hive/)

Pour découvrir d'autres manières d'utiliser Hadoop sur HDInsight.

* [Utilisation de Pig avec Hadoop sur HDInsight](../hdinsight-use-pig/)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](../hdinsight-use-mapreduce/)

<!--HONumber=45--> 
