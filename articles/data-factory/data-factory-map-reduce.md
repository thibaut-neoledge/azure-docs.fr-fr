<properties 
	pageTitle="Appeler le programme MapReduce à partir d'Azure Data Factory" 
	description="Learn how to process data by running MapReduce programs on an Azure HDInsight cluster from an Azure data factory." 
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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Appeler des programmes MapReduce à partir de Data Factory
Cet article explique comment appeler un programme **MapReduce** à partir d’un pipeline Azure Data Factory à l’aide de l’**activité MapReduce HDInsight**.

## Introduction 
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l'utilisation de la transformation MapReduce de l'activité HDInsight.
 
Consultez les articles [Pig](data-factory-pig-activity) et [Hive](data-factory-hive-activity.md) pour plus d’informations sur l’exécution de scripts Pig/Hive sur un cluster HDInsight à partir d’un pipeline de fabrique de données Azure à l’aide des transformations Pig/Hive de l’activité HDInsight.

## JSON pour l’activité HDInsight à l’aide de la transformation MapReduce 

Dans la définition JSON de l’activité HDInsight :
 
1. Affectez au **type** de l’**activité** la valeur **HDInsight**.
3. Spécifiez le nom de la classe pour la propriété **className**.
4. Spécifiez le chemin d’accès du fichier JAR, ainsi que le nom de fichier, pour la propriété **jarFilePath**.
5. Spécifiez le service lié qui fait référence au stockage d’objets blob Azure contenant le fichier JAR pour la propriété **jarLinkedService**.   
6. Spécifiez les arguments du programme MapReduce dans la section **arguments**. Lors de l'exécution, vous verrez quelques arguments supplémentaires (par exemple : mapreduce.job.tags) à partir de l'infrastructure MapReduce. Pour différencier vos arguments avec les arguments MapReduce, envisagez d'utiliser l’option et la valeur en tant qu'arguments comme indiqué dans l'exemple suivant (- s,--entrée - sortie etc. sont des options immédiatement suivies par leurs valeurs).

 

		{
		  "name": "MahoutMapReduceSamplePipeline",
		  "properties": {
		    "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		    "activities": [
		      {
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

Vous pouvez utiliser la transformation MapReduce pour exécuter un fichier jar MapReduce dans un cluster HDInsight. Dans l'exemple suivant de définition JSON d'un pipeline, l'activité HDInsight est configurée pour exécuter un fichier JAR Mahout.

## Exemple
Vous pouvez télécharger un exemple d’utilisation de l’activité HDInsight avec une transformation MapReduce à l’emplacement suivant : [Exemples Data Factory sur GitHub](data-factory-samples.md).


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=Oct15_HO3-->