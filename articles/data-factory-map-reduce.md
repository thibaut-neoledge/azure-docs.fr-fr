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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Appeler des programmes MapReduce à partir de Data Factory
Cet article décrit comment appeler un **MapReduce** programme à partir d'un pipeline de fabrique de données Azure à l'aide de la **HDInsight activité** avec **transformation MapReduce**.

## Introduction 
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. Cet article décrit l'utilisation de la transformation MapReduce de l'activité HDInsight.
 
Voir [Utilisez Pig et Hive avec Data Factory][data-factory-pig-hive-activities] pour plus d'informations sur l'exécution de Pig/ruche de scripts sur un HDInsight cluster à partir d'un pipeline de fabrique de données Azure à l'aide de transformations Pig/ruche de l'activité de HDInsight.

## JSON pour l'activité de HDInsight à l'aide de la transformation MapReduce 

Dans la définition de JSON pour l'activité de HDInsight :
 
1. Définir le **type** de la **activité** à **HDInsightActivity**.
2. Définir le **type** de la **transformation** à **MapReduce**.
3. Spécifiez le nom de la classe pour **className** propriété.
4. Spécifiez le chemin d'accès au fichier JAR, notamment le nom de fichier **jarFilePath** propriété.
5. Spécifier le service lié qui fait référence au stockage Blob Azure qui contient le fichier JAR pour **jarLinkedService** propriété.   
6. Spécifiez les arguments du programme MapReduce dans le **arguments** section. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

Vous pouvez utiliser la transformation MapReduce pour exécuter un fichier jar MapReduce dans un cluster HDInsight. Dans l'exemple suivant de définition JSON d'un pipeline, l'activité HDInsight est configurée pour exécuter un fichier JAR Mahout.

## Exemple
Vous pouvez télécharger un exemple d'utilisation de l'activité de HDInsight avec Transformation MapReduce à partir de : [données fabrique exemples sur GitHub][data-factory-samples].

## Voir aussi

Article | Description
------ | ---------------
[Didacticiel : Déplacer et traiter des fichiers journaux à l'aide de la fabrique de données][adf-tutorial] | Cet article fournit une procédure pas à pas bout en bout qui montre comment implémenter une véritable proche à l'aide de la fabrique de données Azure pour transformer les données des fichiers journaux en insights le cas. Dans ce didacticiel, vous allez utiliser les deux transformations Pig et Hive pour traiter les données. 
[Référence du développeur fabrique données Azure][developer-reference] | La référence du développeur possède le contenu de référence complet pour les applets de commande, script JSON, fonctions, etc.... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->