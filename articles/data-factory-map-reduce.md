<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Appeler le programme MapReduce à partir d'Azure Data Factory" description="Découvrez comment traiter des données en exécutant des programmes MapReduce sur un cluster Azure HDInsight à partir d'une fabrique de données Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Appeler des programmes MapReduce à partir de Data Factory
Cet article décrit comment appeler un programme **MapReduce** à partir d'un pipeline Azure Data Factory à l'aide de l'**activité HDInsight** et de la **transformation MapReduce**. 

## Introduction 
Un pipeline dans une fabrique de données Azure traite les données dans les services de stockage liés à l'aide des services de calcul liés. Il contient une séquence d'activités dans laquelle chaque activité effectue une opération de traitement spécifique. 

- L'**activité de copie** se charge de copier les données d'un stockage source vers un stockage de destination. Pour plus d'informations sur l'activité de copie, consultez [Copie de données avec Data Factory][data-factory-copy-activity]. 
- L'**activité HDInsight** traite les données en exécutant des scripts Hive/Pig ou des programmes MapReduce sur un cluster HDInsight. L'activité HDInsight prend en charge trois transformations : **Hive**, **Pig** et **MapReduce**. L'activité HDInsight peut consommer une ou plusieurs entrées, et produire une ou plusieurs sorties.
 
Consultez [Utilisation de Pig et Hive avec Data Factory][data-factory-pig-hive-activities] pour plus d'informations sur l'exécution de scripts Pig/Hive sur un cluster HDInsight à partir d'un pipeline de fabrique de données Azure à l'aide des transformations Pig/Hive de l'activité HDInsight. Cet article décrit l'utilisation de la transformation MapReduce de l'activité HDInsight.

## Pipeline JSON
Dans le fichier JSON d'un pipeline :
 
1. Affectez au **type** de l'**activité** la valeur **HDInsightActivity**.
2. Affectez au **type** de la **transformation** la valeur **MapReduce**.
3. Spécifiez le nom de la classe pour la propriété **className**.
4. Spécifiez le chemin d'accès du fichier JAR, ainsi que le nom de fichier, pour la propriété **jarFilePath**.
5. Spécifiez le service lié qui fait référence au stockage d'objets blob Azure contenant le fichier JAR pour la propriété **jarLinkedService**.   
6. Spécifiez les arguments du programme MapReduce dans la section **arguments**. 

Vous pouvez utiliser la transformation MapReduce pour exécuter un fichier jar MapReduce dans un cluster HDInsight. Dans l'exemple suivant de définition JSON d'un pipeline, l'activité HDInsight est configurée pour exécuter un fichier JAR Mahout.   
 

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

## Exemple
Vous pouvez télécharger un exemple d'utilisation de l'activité HDInsight avec une transformation MapReduce à l'emplacement suivant : [exemples Data Factory sur GitHub][data-factory-samples].  

## Voir aussi

Article | Description
------ | ---------------
[Présentation d'Azure Data Factory][data-factory-introduction] | Cet article présente le service Azure Data Factory, les concepts associés, sa valeur ajoutée et les scénarios qu'il prend en charge.
[Prise en main d'Azure Data Factory][adf-getstarted] | Cet article propose un didacticiel pas à pas qui permet de créer un exemple de fabrique de données Azure qui copie les données d'un objet blob Azure vers une base de données SQL Azure.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui permet de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas qui permet de créer une activité personnalisée et de l'utiliser dans un pipeline.
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory.
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
