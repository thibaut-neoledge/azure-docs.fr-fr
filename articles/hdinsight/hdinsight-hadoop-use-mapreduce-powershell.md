<properties
   pageTitle="Utilisation de MapReduce et PowerShell avec Hadoop | Microsoft Azure"
   description="Apprenez à utiliser PowerShell pour exécuter des tâches MapReduce à distance avec Hadoop sur HDInsight."
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
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Exécution à distance de requêtes Hadoop sur HDInsight à l’aide de PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Ce document fournit un exemple d’utilisation d’Azure PowerShell pour exécuter une tâche MapReduce sur un Hadoop sur un cluster HDInsight.

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

- **Un cluster Azure HDInsight (Hadoop sur HDInsight) Windows ou Linux**

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md)

##<a id="powershell"></a>Exécution d’une tâche MapReduce avec Azure PowerShell

Azure PowerShell propose des *applets de commande* qui vous permettent d'exécuter à distance des tâches MapReduce sur HDInsight. En interne, cela est accompli en effectuant des appels REST à [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anciennement nommé Templeton) exécuté sur le cluster HDInsight.

Les applets de commande suivantes sont utilisées lors de l’exécution des tâches MapReduce sur un cluster HDInsight à distance.

* **Add-AzureAccount** : authentifie Azure PowerShell sur votre abonnement Azure.

* **New-AzureHDInsightMapReduceJobDefinition** : crée une nouvelle *définition de tâche* avec les informations MapReduce spécifiées.

* **Start-AzureHDInsightJob** : envoie la définition de la tâche à HDInsight, démarre la tâche et retourne un objet de *tâche* pouvant être utilisé pour vérifier le statut de la tâche.

* **Wait-AzureHDInsightJob** : utilise l’objet de la tâche pour vérifier le statut de la tâche. Il attend que la tâche soit terminée ou que le délai d’attente soit dépassé.

* **Get-AzureHDInsightJobOutput** : utilisé pour récupérer la sortie de la tâche

Les étapes suivantes montrent comment utiliser ces applets de commande pour exécuter une tâche sur votre cluster HDInsight.

1. À l’aide d’un éditeur, enregistrez le code suivant sous **mapreducejob.ps1**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. Ouvrez une invite de commandes **Azure PowerShell**. Accédez à l’emplacement du fichier **mapreducejob.ps1**, puis utilisez les éléments suivants pour exécuter le script :

		.\mapreducejob.ps1

3. Une fois la tâche terminée, vous devez recevoir une sortie similaire à celle présentée ci-dessous :

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	Cela indique que la tâche a été effectuée avec succès.

	> [AZURE.NOTE]Si **ExitCode** correspond à une valeur différente de 0, consultez [Dépannage](#troubleshooting).

##<a id="results"></a>Affichage de la sortie du travail

La tâche MapReduce a stocké les résultats de l’opération dans le stockage d’objets blob Azure, dans le chemin d’accès ****wasb:///example/data/WordCountOutput** spécifié en tant qu’argument de la tâche. Le stockage d’objets blob Azure est accessible via Azure PowerShell, mais vous devez connaître le nom du compte de stockage, la clé et un conteneur utilisé par votre cluster HDInsight pour accéder directement aux fichiers.

Vous pouvez obtenir ces informations à l’aide des applets de commande Azure PowerShell suivantes :

* **Get-AzureHDInsightCluster** : renvoie des informations sur un cluster HDInsight, y compris les comptes de stockage associés. Il y aura toujours un compte de stockage par défaut associé à un cluster.
* **New-AzureStorageContext** : avec le nom de compte de stockage et la clé récupérés à l’aide de **Get-AzureHDInsightCluster**, il renvoie un objet de contexte pouvant être utilisé pour accéder au compte de stockage.
* **Get-AzureStorageBlob** : avec un objet de contexte et un nom de conteneur, il renvoie une liste d’objets blob au sein du conteneur.
* **Get-AzureStorageBlobContent** : avec un objet de contexte, un nom et un chemin d’accès de fichier et un nom de conteneur (retournés par **Get-AzureHDinsightCluster**), il télécharge un fichier du stockage d’objets blob Azure.

L'exemple suivant récupère les informations de stockage, puis télécharge la sortie de ****wasb:///example/data/WordCountOutput**. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]Cet exemple stocke les fichiers téléchargés dans le dossier **example/data/WordCountOutput** dans le répertoire à partir duquel vous avez exécuté le script.

La sortie de la tâche MapReduce est stockée dans des fichiers portant le nom *part-r-#####*. Ouvrez le fichier **example/data/WordCountOutput/part-r-00000** dans un éditeur de texte pour afficher les mots et les décomptes générés par la tâche.

> [AZURE.NOTE]Les fichiers de résultat d’une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devez modifier le nom du fichier de sortie.

##<a id="troubleshooting"></a>Résolution des problèmes

Si aucune information n'est retournée lorsque la tâche est terminée, il se peut qu'une erreur soit survenue au cours du traitement. Pour afficher les informations d’erreur pour ce projet, ajoutez la commande suivante à la fin du fichier **mapreducejob.ps1**, enregistrez-le et exécutez-le à nouveau.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

Cela renvoie les informations écrites dans STDERR sur le serveur lors de l’exécution de la tâche et peut vous aider à déterminer pourquoi la tâche échoue.

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, Azure PowerShell offre un moyen facile d’exécuter des tâches MapReduce sur un cluster HDInsight, de surveiller l’état de la tâche et de récupérer la sortie.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

<!---HONumber=Oct15_HO3-->