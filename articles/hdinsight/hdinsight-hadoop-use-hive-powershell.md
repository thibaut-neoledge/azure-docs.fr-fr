<properties
   pageTitle="Utilisation de Hadoop Hive avec PowerShell dans HDInsight | Microsoft Azure"
   description="Utilisez PowerShell pour exécuter des requêtes Hive dans Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Exécution de requêtes Hive avec PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ce document fournit un exemple d'utilisation d’Azure PowerShell pour exécuter des requêtes Hive sur un cluster Hadoop sur HDInsight.

> [AZURE.NOTE]Ce document ne fournit pas de description détaillée de ce que font les instructions HiveQL utilisées dans les exemples. Pour plus d’informations sur le langage HiveQL utilisé dans cet exemple, consultez la page [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md).


##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux**
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##<a id="powershell"></a>Exécution de requêtes Hive avec Azure PowerShell

Azure PowerShell fournit des *cmdlets* qui vous permettent d'exécuter à distance des requêtes Hive sur HDInsight. En interne, cela est accompli en effectuant des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivants sont utilisés lors de l'exécution de requêtes Hive sur un cluster à distance HDInsight :

* **Add-AzureAccount** : authentifie Azure PowerShell sur votre abonnement Azure.

* **New-AzureHDInsightHiveJobDefinition** : crée une nouvelle *définition de travail* à l'aide des instructions HiveQL spécifiées.

* **Start-AzureHDInsightJob** : envoie la définition de la tâche à HDInsight, démarre la tâche et retourne un objet de *tâche* pouvant être utilisé pour vérifier le statut de la tâche.

* **Wait-AzureHDInsightJob** : utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.

* **Get-AzureHDInsightJobOutput** : utilisé pour récupérer la sortie de la tâche.

* **Invoke-Hive** : utilisé pour exécuter des instructions HiveQL. Cela bloque la fin de la requête, puis retourne les résultats.

* **Use-AzureHDInsightCluster** : configure le cluster actuel pour utiliser pour la commande **Invoke-Hive**.

Les étapes suivantes montrent comment utiliser ces cmdlets pour exécuter une tâche sur votre cluster HDInsight :

1. À l'aide d'un éditeur, enregistrez le code suivant en tant que **hivejob.ps1**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

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

2. Ouvrez une invite de commandes **Azure PowerShell**. Accédez au répertoire du fichier **hivejob.ps1**, puis utilisez la commande suivante pour exécuter le script :

		.\hivejob.ps1

7. Une fois la tâche terminée, des informations similaires à celles présentées ci-dessous doivent s’afficher :

		Display the standard output...
		[ERROR]	3

4. Comme mentionné précédemment, **Invoke-Hive** peut être utilisé pour exécuter une requête et attendre la réponse. Utilisez les commandes suivantes, en remplaçant **CLUSTERNAME** par le nom de votre cluster :

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	La sortie se présente comme suit :

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script HiveQL de PowerShell ou la cmdlet **Here-Strings** Azure PowerShell. L'extrait suivant montre comment utiliser la cmdlet **Invoke-Hive** pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers wasb://.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Pour plus d'informations sur **Here-Strings**, consultez la page <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Utilisation du fichier de script Here-Strings de PowerShell</a>.

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n'est retournée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d'erreur relatives à cette tâche, ajoutez les commandes suivantes à la fin du fichier **hivejob.ps1**, enregistrez-le, puis exécutez-le à nouveau.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Cela renvoie les informations écrites dans STDERR sur le serveur lors de l’exécution du travail et peut vous aider à déterminer pourquoi la tâche échoue.

##<a id="summary"></a>Résumé

Comme vous pouvez le constater, Azure PowerShell permet d'exécuter facilement des requêtes Hive sur un cluster HDInsight, de surveiller l'état de la tâche et de récupérer le résultat.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur Hive dans HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->