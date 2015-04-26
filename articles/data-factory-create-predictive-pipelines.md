<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data Factory - Créer des pipelines prédictifs à l'aide de Data Factory et Machine Learning | Azure" description="Décrit comment créer des pipelines de prédiction au moyen d'Azure Data Factory et d'Azure Machine Learning." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Créer des pipelines prédictifs à l'aide d'Azure Data Factory et Azure Machine Learning 
Vous pouvez rendre opérationnels des modèles [Azure Machine Learning][azure-machine-learning] publiés dans les pipelines Azure Data Factory. Ces derniers sont appelés pipelines prédictifs. Pour créer un pipeline prédictif, vous avez besoin des éléments suivants :

-	clé API et URL de notation par lots du modèle d'espace de travail publié (voir l'image ci-dessous) ;
-	stockage d'objets blob Azure contenant le fichier CSV d'entrée à noter ;
-	stockage d'objets blob Azure contenant le fichier CSV des résultats de notation.

	![Machine Learning Dashboard][machine-learning-dashboard]

	L'URL de notation par lots d'AzureMLLinkedService est obtenue comme cela est indiqué dans l'image ci-dessus, en enlevant " **help** ":  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Un **pipeline prédictif** comporte ce qui suit :

-	tables d'entrée et de sortie ;
-	services liés Azure Storage et Azure ML ;
-	pipeline avec activité de notation par lots Azure ML.

## Exemple



1. Créez un service lié pour votre service Azure Storage. Si les fichiers d'entrée et de sortie de notation sont dans des comptes de stockage distincts, vous aurez besoin de deux services liés. Voici un exemple JSON :

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Créez les tables d'entrée et de sortie Azure Data Factory. Notez que contrairement à d'autres tables Data Factory, celles-ci doivent contenir les valeurs **folderPath** et **fileName**. Vous pouvez utiliser le partitionnement et provoquer l'exécution de chaque lot (chaque tranche de données) pour traiter ou produire des fichiers d'entrée et de sortie uniques. Vous aurez probablement besoin d'inclure une activité en amont pour convertir l'entrée au format de fichier CSV, et la placer dans le compte de stockage pour chaque tranche. Dans ce cas, n'incluez pas les paramètres " waitOnExternal " indiqués dans l'exemple ci-dessous. Par ailleurs, ScoringInputBlob représente la table de sortie d'une autre activité.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. Cet exemple de sortie utilise le partitionnement pour créer un chemin de sortie unique à chaque exécution de tranche. Sinon, l'activité remplace le fichier.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Créez un service lié de type **AzureMLLinkedService**, en fournissant la clé API et l'URL de notation par lots du modèle.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Enfin, créez un pipeline contenant **AzureMLBatchScoringActivity**. Il obtient l'emplacement du fichier d'entrée de vos tables d'entrée, appelle l'API de notation par lots AzureML, puis copie la sortie de notation par lots dans l'objet blob spécifié de votre table de sortie. Contrairement à d'autres activités Data Factory, AzureMLBatchScoringActivity ne peut avoir qu'une seule table d'entrée et de sortie.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## Voir aussi

Article | Description
------ | ---------------
[Présentation d'Azure Data Factory][adf-introduction] | Cet article fournit une vue d'ensemble des services Azure Data Factory et des scénarios pris en charge. 
[Prise en main d'Azure Data Factory][adf-getstarted] | Cet article fournit un didacticiel de base qui donne des instructions étape par étape sur la création et la surveillance d'un exemple de fabrique de données.
[Activation de vos pipelines pour les utiliser avec des données locales][use-onpremises-datasources] | Cet article contient une procédure pas à pas qui permet de copier les données d'une base de données SQL Server locale vers un objet blob Azure.
[Utilisation de Pig et Hive avec Data Factory][use-pig-and-hive-with-data-factory] | Cet article contient une procédure pas à pas qui permet d'exécuter un script hive/pig à l'aide de l'activité HDInsight pour traiter des données d'entrée et produire des données de sortie.
[Didacticiel : Déplacement et traitement des fichiers journaux à l'aide de Data Factory][adf-tutorial] | Cet article contient une procédure pas à pas permettant d'implémenter un scénario proche du monde réel à l'aide d'Azure Data Factory pour transformer les données des fichiers journaux en données détaillées.
[Utilisation des activités personnalisées de Data Factory][use-custom-activities] | Cet article contient une procédure pas à pas qui permet de créer une activité personnalisée et de l'utiliser dans un pipeline.
[Résolution des problèmes liés à Data Factory][troubleshoot] | Cet article explique comment résoudre des problèmes liés à Azure Data Factory. Vous pouvez essayer la procédure pas à pas de cet article portant sur ADFTutorialDataFactory en introduisant une erreur (en supprimant la table dans la base de données SQL Azure). 
[Référence du développeur Azure Data Factory][developer-reference] | Ces informations de référence du développeur comportent des informations complètes sur les applets de commande, le script JSON, les fonctions, etc. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/fr-fr/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
