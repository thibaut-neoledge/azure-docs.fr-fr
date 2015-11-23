<properties 
	pageTitle="Activité de diffusion en continu Hadoop" 
	description="Découvrez comment vous pouvez utiliser l’activité de diffusion en continu Hadoop dans une fabrique de données Azure pour exécuter des programmes de diffusion en continu Hadoop sur votre cluster HDInsight propre/à la demande." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Activité de diffusion en continu Hadoop
Vous pouvez utiliser l’activité HDInsightStreamingActivity pour appeler une tâche de diffusion en continu Hadoop à partir d’un pipeline Azure Data Factory. L’extrait de code JSON suivant illustre la syntaxe pour l’utilisation de HDInsightStreamingActivity dans un fichier JSON de pipeline.

L’activité de diffusion en continu HDInsight dans un [pipeline](data-factory-create-pipelines.md) Data Factory exécute des programmes de diffusion en continu Hadoop sur votre cluster HDInsight [propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [à la demande](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) sous Windows ou Linux. Cet article s’appuie sur l’article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d’ensemble de la transformation des données et les activités de transformation prises en charge.

## Exemple

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

Notez les points suivants :

1. Définissez **linkedServiceName** sur le nom du service lié qui pointe vers votre cluster HDInsight sur lequel sera exécutée la tâche mapreduce de diffusion en continu.
2. Affectez au type de l’activité la valeur **HDInsightStreaming**.
3. Pour la propriété **mapper**, spécifiez le nom du fichier exécutable du mappeur. Dans l’exemple ci-dessus, cat.exe est le fichier exécutable du mappeur.
4. Pour la propriété **reducer**, spécifiez le nom du fichier exécutable du raccord de réduction. Dans l’exemple ci-dessus, wc.exe est le fichier exécutable du raccord de réduction.
5. Pour la propriété de type **input**, spécifiez le fichier en entrée (y compris son emplacement) du mappeur. Dans l’exemple « wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt », adfsample est le conteneur de l’objet blob, example/data/Gutenberg est le dossier et davinci.txt est l’objet blob.
6. Pour la propriété de type **output**, spécifiez le fichier en sortie (y compris son emplacement) du raccord de réduction. La sortie de la tâche de diffusion en continu Hadoop est écrite à l’emplacement spécifié pour cette propriété.
7. Dans la section **filePaths**, spécifiez les chemins des fichiers exécutables du mappeur et du raccord de réduction. Dans l’exemple « adfsample/example/apps/wc.exe », adfsample est le conteneur de l’objet blob, example/apps est le dossier et wc.exe est le fichier exécutable.
8. Pour la propriété **fileLinkedService**, spécifiez le service lié Azure Storage qui représente le stockage Azure qui contient les fichiers spécifiés dans la section filePaths.
9. Pour la propriété **arguments**, spécifiez les arguments de la tâche de diffusion en continu.

> [AZURE.NOTE]Comme le montre l’exemple, vous devrez spécifier un ensemble de données en sortie pour l’activité de diffusion en continu Hadoop pour la propriété **outputs**. Il s'agit simplement d'un ensemble de données factice qui est nécessaire au fonctionnement de la planification de pipeline. Il est inutile de spécifier un jeu de données en entrée pour l'activité de la propriété **inputs**.

	

<!---HONumber=Nov15_HO3-->