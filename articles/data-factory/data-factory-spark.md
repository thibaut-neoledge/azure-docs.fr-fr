<properties 
	pageTitle="Appeler des programmes Spark à partir d’Azure Data Factory" 
	description="Apprenez à appeler des programmes Spark à partir d'une fabrique de données Azure avec l'activité MapReduce." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Appeler des programmes Spark à partir de Data Factory
## Introduction
Vous pouvez utiliser l'activité MapReduce dans un pipeline Data Factory pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez l’article [Activité MapReduce](data-factory-map-reduce.md) pour plus d'informations sur l'utilisation de l'activité avant de lire cet article.

## Exemple Spark sur GitHub
L’[exemple Spark - Data Factory sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) montre comment utiliser l'activité MapReduce pour appeler un programme Spark. Le programme Spark copie simplement les données d'un conteneur d'objets blob Azure vers un autre.

## Entités Data Factory
Le dossier **Spark-ADF/src/ADFJsons** contient des fichiers pour les entités Data Factory (services liés, jeux de données, pipeline).

Il y a deux **services liés** dans cet exemple : Azure Storage et Azure HDInsight. Vous devez spécifier le nom et les valeurs de clés Azure Storage dans **StorageLinkedService.json** et clusterUri, le nom d'utilisateur et le mot de passe dans **HDInsightLinkedService.json**.

Il y a deux **jeux de données** dans cet exemple : **input.json** et **output.json**. Ces fichiers se trouvent dans le dossier **Datasets**. Ces fichiers représentent les jeux de données d'entrée et de sortie de l'activité MapReduce

Il y a un **pipeline** dans l'exemple, dans le dossier **ADFJsons/Pipeline**. Il s'agit de l'entité la plus importante à examiner pour comprendre comment appeler un programme Spark à l'aide de l'activité MapReduce.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

Comme vous pouvez le voir, l'activité MapReduce est configurée pour appeler **spark-adf-job-bin.jar** dans le conteneur **libs** dans votre stockage Azure (spécifié dans StorageLinkedService.json). Le code source de ce programme se trouve dans le dossier Spark-ADF/src/main/java/com/adf/spark et il appelle spark-submit et exécute des tâches Spark.

Ce programme MapReduce (spark-adf-job-bin.jar) en cours d'exécution sur votre cluster HDInsight Spark appelle un programme Spark **sparkdemoapp\_2.10-1.0.jar** et transmet les arguments qu'il reçoit par le biais de l'activité MapReduce (illustrée dans le JSON ci-dessus) au programme Spark. **sparkdemoapp\_2.10-1.0.jar** contient le code source Scala qui copie les données d'un conteneur d'objets blob Azure vers un autre. Vous pouvez remplacer le fichier jar demoapp par n'importe quel autre jar contenant une tâche que vous voulez exécuter avec Spark.

Pour résumer, l’**activité MapReduce** appelle le programme MapReduce **spark-adf-job-bin.jar** qui appelle le programme Spark **sparkdemoapp\_2.10-1.0.jar**. Pour exécuter votre propre programme Spark, remplacez sparkdemoapp\_2.10-1.0.jar par le vôtre.

> [AZURE.NOTE] Vous devez utiliser votre propre cluster HDInsight Spark avec cette approche pour appeler des programmes Spark avec l'activité MapReduce. L’utilisation d'un cluster HDInsight à la demande n’est pas prise en charge.


## Voir aussi
- [Activité Hive](data-factory-hive-activity.md)
- [Activité pig](data-factory-pig-activity.md)
- [Activité MapReduce](data-factory-map-reduce.md)
- [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
- [Appeler des scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->