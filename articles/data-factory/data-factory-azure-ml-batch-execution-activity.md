<properties 
	pageTitle="Utiliser des activités Machine Learning | Microsoft Azure" 
	description="Décrit comment créer des pipelines prédictifs à l’aide d’Azure Data Factory et d’Azure Machine Learning." 
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
	ms.date="06/20/2016" 
	ms.author="spelluru"/>

# Créer des pipelines prédictifs à l’aide des activités Azure Machine Learning   
## Vue d'ensemble

> [AZURE.NOTE] Pour prendre en main le service Azure Data Factory rapidement, consultez les articles [Présentation d’Azure Data Factory](data-factory-introduction.md) et [Concevez votre premier pipeline](data-factory-build-your-first-pipeline.md).

## Introduction

[AZURE MACHINE LEARNING](https://azure.microsoft.com/documentation/services/machine-learning/) vous permet de générer, tester et déployer des solutions d’analyse prédictive. D’un point de vue très général, cela s’effectue en trois étapes :

1. **Créez une expérience de formation**. Pour ce faire, utilisez Azure ML Studio, un environnement de développement visuel collaboratif qui vous permet de former et de tester un modèle d’analyse prédictive à l’aide de données de formation que vous fournissez.
2. **Convertissez-la en une expérience prédictive**. Une fois que votre modèle a été formé avec des données existantes et que vous êtes prêt à l’utiliser pour la notation de nouvelles données, vous préparez et simplifiez votre expérience de notation.
3. **Déployez-la en tant que service web**. Vous pouvez publier votre expérience de notation comme un service web Azure. Les utilisateurs peuvent envoyer des données à votre modèle via ce point de terminaison de service web et recevoir des prédictions de résultats du modèle.

Azure Data Factory vous permet de créer facilement des pipelines qui s’appuient sur un service web [Azure Machine Learning][azure-machine-learning] publié pour l’analyse prédictive. À l’aide de l’**activité d’exécution par lots** dans un pipeline Azure Data Factory, vous pouvez appeler un service web Azure ML pour effectuer des prédictions sur les données par lots. Consultez la section [Appeler un service web Azure ML à l’aide de l’activité d’exécution par lots](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) pour plus d’informations.

Au fil du temps, les modèles prédictifs dans les expériences de notation Azure ML doivent être reformés à l’aide de nouveaux jeux de données d’entrée. Vous pouvez reformer un modèle Azure ML à partir d’un pipeline Data Factory en procédant comme suit :

1. Publiez l’expérience de formation (et non l’expérience prédictive) comme un service web. Vous pouvez effectuer cette tâche dans Azure ML Studio comme vous l’avez fait pour exposer l’expérience prédictive comme un service web dans le scénario précédent.
2. Utilisez l’activité d’exécution par lots Azure ML pour appeler le service web pour l’expérience de formation. En fait, vous pouvez utiliser l’activité d’exécution par lots Azure ML pour appeler à la fois le service web de formation et le service web de notation.
  
Une fois que vous avez fini la reformation, vous souhaitez mettre à jour le service web de notation (expérience prédictive exposée comme un service web) avec le modèle qui vient d’être formé. Pour ce faire, procédez comme suit :

1. Ajoutez un point de terminaison autre que par défaut au service web de notation. Le point de terminaison par défaut du service web ne peut pas être mis à jour ; vous devrez donc créer un point de terminaison autre que par défaut à l’aide du portail Azure. Consultez l’article [Créer des points de terminaison](../machine-learning/machine-learning-create-endpoint.md) pour obtenir des informations conceptuelles et procédurales.
2. Mettez à jour des services liés Azure ML existants pour que la notation utilise le point de terminaison autre que par défaut. Vous devez utiliser le nouveau point de terminaison pour utiliser le service web mis à jour.
3. Utilisez l’**activité des ressources de mise à jour Azure ML** pour mettre à jour le service web avec le modèle qui vient d’être formé.

Consultez la section [Mettre à jour les modèles Azure ML à l’aide de l’activité des ressources de mise à jour](#updating-azure-ml-models-using-the-update-resource-activity) pour plus d’informations.

## Appeler un service web Azure ML à l’aide de l’activité d’exécution par lots

Vous utilisez Azure Data Factory pour orchestrer le déplacement et le traitement des données, puis pour effectuer une exécution par lot à l’aide d’Azure Machine Learning. Pour ce faire, vous devez effectuer les opérations suivantes :

1. Créer un service lié Azure Machine Learning. Les éléments suivants seront nécessaires :
	1. L’**URI DE DEMANDE** pour l’API d’exécution de lot. Pour trouver l’URI de demande, cliquez sur le lien **BATCH EXECUTION** dans la page des services web (voir ci-dessous).
	1. La **CLÉ API** pour le service web Azure Machine Learning publié. Vous trouverez la clé API en cliquant sur le service web que vous avez publié.
 2. L’activité **AzureMLBatchExecution**.

	![Tableau de bord Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![URI de lot](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### Scénario : utilisation d’entrées/sorties de service web qui font référence à des données du stockage d’objets blob Azure
Dans ce scénario, le service web Azure Machine Learning effectue des prédictions à l’aide des données d’un fichier dans un stockage d’objets blob Azure et stocke les résultats des prédictions dans le stockage d’objets blob. Le code JSON suivant définit un pipeline Azure Data Factory avec une activité AzureMLBatchExecution. L’activité a le jeu de données **DecisionTreeInputBlob** comme entrée et **DecisionTreeResultBlob** comme sortie. **DecisionTreeInputBlob** est transmis comme entrée du service web à l’aide de la propriété JSON **webServiceInput**, tandis que **DecisionTreeResultBlob** est transmis à l’aide de la propriété JSON **webServiceOutputs** en tant que sortie.

> [AZURE.NOTE] Les jeux de données référencés par les propriétés **webServiceInput** et **webServiceOutputs** (dans **typeProperties**) doivent également être inclus dans les **entrées** et **sorties** de l’activité.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob"
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE] Seules les entrées et sorties de l’activité AzureMLBatchExecution peuvent être transmises en tant que paramètres au service web. Par exemple, dans l’extrait de code JSON ci-dessus, DecisionTreeInputBlob est une entrée de l’activité AzureMLBatchExecution, qui est transmise comme entrée au service web via le paramètre webServiceInput.

### Exemple

Cet exemple utilise Azure Storage pour stocker les données d'entrée et de sortie.

Nous vous recommandons de passer en revue le didacticiel [Concevoir votre premier pipeline avec Azure Data Factory][adf-build-1st-pipeline] avant de lire cet exemple et d’utiliser Data Factory Editor pour créer des artefacts Data Factory (services liés, jeux de données, pipeline).
 

1. Créez un **service lié** pour votre service **Azure Storage**. Si les fichiers d’entrée et de sortie sont dans des comptes de stockage distincts, vous aurez besoin de deux services liés. Voici un exemple JSON :

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Créez le **jeu de données** d’**entrée** Azure Data Factory. Notez que, contrairement à d’autres jeux de données Data Factory, celui-ci doit contenir les valeurs **folderPath** et **fileName**. Vous pouvez utiliser le partitionnement et provoquer l'exécution de chaque lot (chaque tranche de données) pour traiter ou produire des fichiers d'entrée et de sortie uniques. Vous aurez probablement besoin d'inclure une activité en amont pour convertir l'entrée au format de fichier CSV, et la placer dans le compte de stockage pour chaque tranche. Dans ce cas, n’incluez pas les paramètres **external** et **externalData** indiqués dans l’exemple ci-dessous. Par ailleurs, DecisionTreeInputBlob représente le jeu de données de sortie d’une autre activité.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	Votre fichier csv d’entrée doit disposer d’une ligne d’en-tête de colonnes. Si vous utilisez l'**activité de copie** pour créer/déplacer le fichier .csv dans le stockage d'objets blob, vous devez définir la propriété du récepteur **blobWriterAddHeader** sur **true**. Par exemple :
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Si le fichier csv ne dispose pas de ligne d'en-tête, l'erreur suivante risque de se produire : **Erreur pendant l'activité : erreur de lecture de la chaîne. Jeton inattendu : StartObject. Chemin d’accès ’’, ligne 1, position 1**.
3. Créez le **jeu de données** de **sortie** Azure Data Factory. Cet exemple utilise le partitionnement pour créer un chemin de sortie unique à chaque exécution de tranche. Sinon, l'activité remplace le fichier.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Créez un **service lié** de type **AzureMLLinkedService** en fournissant la clé API et l’URL d’exécution par lots du modèle.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Enfin, créez un pipeline contenant une activité **AzureMLBatchExecution**. Il obtient l’emplacement du fichier d’entrée de vos jeux de données d’entrée, appelle l’API d’exécution par lots Azure Machine Learning, puis copie la sortie d’exécution par lots dans l’objet blob spécifié dans votre jeu de données de sortie.

	> [AZURE.NOTE] L’activité AzureMLBatchExecution peut avoir zéro ou plusieurs entrées et une ou plusieurs sorties.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Les dates/heures de **début** et de **fin** doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative. Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**. Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE] Définir une entrée pour l’activité AzureMLBatchExecution est facultatif.

### Scénario : utilisation de modules lecteur/enregistreur pour faire référence à des données dans différents stockages

Un autre scénario courant pour créer des expériences Azure ML consiste à utiliser des modules lecteur et enregistreur. Le module lecteur permet de charger des données dans une expérience, tandis que le module enregistreur sert à enregistrer les données issues de cette expérience. Pour plus d’informations sur les modules Lecteur et Enregistreur, consultez les rubriques [Lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et [Enregistreur](https://msdn.microsoft.com/library/azure/dn905984.aspx) dans la bibliothèque MSDN.

Quand vous utilisez les modules lecteur et enregistreur, nous vous recommandons de recourir à un paramètre de service web pour chaque propriété de ces modules. Ces paramètres web permettent de configurer les valeurs pendant l’exécution. Par exemple, vous pouvez créer une expérience avec un module lecteur qui utilise une base de données Azure SQL Database : XXX.database.windows.net. Une fois le service web déployé, vous pouvez autoriser les consommateurs du service web à spécifier un autre serveur Azure SQL Server appelé YYY.database.windows.net. Vous pouvez utiliser un paramètre de service web pour permettre à cette valeur d’être configurée.

> [AZURE.NOTE] L’entrée et la sortie du service web diffèrent des paramètres de service web. Dans le premier scénario, vous avez vu comment une entrée et une sortie peuvent être spécifiées pour un service web Azure ML. Dans ce scénario, vous allez passer des paramètres pour un service web qui correspondent aux propriétés des modules lecteur/enregistreur.

Examinons un scénario d’utilisation de paramètres de service web. Vous disposez d’un service web Azure Machine Learning déployé qui utilise un module lecteur pour lire les données d’une des sources de données Azure Machine Learning prises en charge (par exemple : Azure SQL Database). Après l’exécution par lots, les résultats sont écrits à l’aide d’un module enregistreur (Azure SQL Database). Aucune entrée ou sortie de service web n’est définie dans les expériences. Dans ce cas, nous vous recommandons de configurer les paramètres de service web appropriés pour les modules lecteur et enregistreur. Vous pouvez ainsi configurer les modules lecteur/enregistreur quand vous utilisez l’activité AzureMLBatchExecution. Spécifiez les paramètres de service web dans la section **globalParameters** du code JSON de l’activité comme suit.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

Vous pouvez également utiliser les [fonctions de Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) pour transmettre les valeurs aux paramètres de service web, comme indiqué dans l'exemple suivant :

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Les paramètres de service web respectent la casse ; veillez donc à ce que les noms que vous indiquez dans le script JSON de l'activité correspondent à ceux exposés par le service web.

### Utilisation d’un module lecteur pour lire les données de plusieurs fichiers dans le stockage d’objets blob Azure
Les pipelines Big Data (Pig, Hive, etc.) peuvent produire un ou plusieurs fichiers de sortie sans extensions. Par exemple, quand vous spécifiez une table Hive externe, les données de cette table peuvent être stockées dans le stockage d’objets blob Azure avec le nom 000000\_0 suivant. Vous pouvez utiliser le module lecteur dans une expérience pour lire plusieurs fichiers et les utiliser pour les prédictions.

Quand vous utilisez le module lecteur dans une expérience Azure Machine Learning, vous pouvez spécifier un objet blob Azure comme entrée. Les fichiers du stockage blob Azure peuvent être les fichiers de sortie (par exemple, 000000\_0) qui sont générés par un script Pig et Hive exécuté sur HDInsight. Le module Lecteur vous permet de lire des fichiers (sans extension) en configurant la propriété **Chemin d’accès au conteneur, répertoire ou blob** du module lecteur pour pointer vers le dossier/conteneur où figurent les fichiers, comme indiqué ci-dessous. Notez que l’astérisque (*) **spécifie que tous les fichiers du dossier/conteneur (par exemple, data/aggregateddata/year=2014/month-6/*)** sont lus dans le cadre de l’expérience.

![Propriétés des objets blob Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Exemple 
#### Pipeline avec l’activité AzureMLBatchExecution avec les paramètres de service web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
Dans l'exemple JSON ci-dessus :

- Le service web Azure Machine Learning déployé utilise un module lecteur et un module enregistreur pour lire/écrire des données depuis/vers une base de données Azure SQL Database. Ce service web expose les quatre paramètres suivants : Database server name, Database name, Server user account name et Server user account password.
- Les dates/heures de **début** et de **fin** doivent toutes deux être au [format ISO](http://en.wikipedia.org/wiki/ISO_8601). Par exemple : 2014-10-14T16:32:41Z. L’heure de **fin** est facultative. Si vous ne spécifiez aucune valeur pour la propriété **end**, cette dernière est calculée comme suit : « **start + 48 heures** ». Pour exécuter le pipeline indéfiniment, spécifiez **9999-09-09** comme valeur pour la propriété **end**. Pour en savoir plus sur les propriétés JSON, voir [Référence de script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

### Autres scénarios

#### Le service web ne nécessite pas d’entrée

Les services web d’exécution par lot Azure ML peuvent servir à exécuter n’importe quel flux de travail (par exemple des scripts R ou Python) qui ne nécessite pas d’entrées. Ou bien, l’expérience peut être configurée avec un module Lecteur qui n’expose pas de paramètres GlobalParameters. Dans ce cas, l’activité AzureMLBatchExecution doit être configurée comme suit :

	{
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### Le service web ne nécessite pas d’entrée/sortie
Il se peut qu’aucune sortie de service web ne soit configurée pour le service web d’exécution par lot Azure ML. Dans cet exemple, aucune entrée ou sortie ni aucun paramètre GlobalParameters n’est configuré. Notez qu’il existe toujours une sortie configurée sur l’activité elle-même, mais elle n’est pas donnée comme sortie webServiceOutput.

	{
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### Le service web utilise des lecteurs et enregistreurs et l’activité s’exécute uniquement lorsque d’autres activités ont réussi

Les modules de lecteur et d’enregistreur du service web Azure ML peuvent être configurés pour s’exécuter avec ou sans paramètres GlobalParameters. Vous pouvez toutefois incorporer les appels de service dans un pipeline de traitement qui utilise des dépendances de jeu de données pour appeler le service uniquement lorsqu’un traitement en amont est terminé, puis déclencher une autre action après l’exécution par lot. Dans ce cas, vous pouvez exprimer les dépendances à l’aide d’entrées et de sorties d’activité, sans les nommer en tant qu’entrées ou sorties de service web.

	{
	    "name": "retraining",
	    "type": "AzureMLBatchExecution",
	    "inputs": [
	        {
	            "name": "upstreamData1"
	        },
	        {
	            "name": "upstreamData2"
	        }
	    ],
	    "outputs": [
	        {
	            "name": "downstreamData"
	        }
	    ],
	    "typeProperties": {
	     },
	    "linkedServiceName": "mlEndpoint",
	    "policy": {
	        "concurrency": 1,
	        "executionPriorityOrder": "NewestFirst",
	        "retry": 1,
	        "timeout": "02:00:00"
	    }
	},

Les **principes** sont les suivants :

-   Si le point de terminaison de votre expérience utilise un élément webServiceInput, il est représenté par un jeu de données d’objets blob et il est inclus dans les entrées de l’activité, ainsi que la propriété webServiceInput. Sinon, la propriété webServiceInput est omise.
-   Si le point de terminaison de votre expérience utilise des éléments webServiceOutput, ils sont représentés par des jeux de données d’objets blob et sont inclus dans les sorties de l’activité, ainsi que la propriété webServicepOutputs (mappée au nom de chaque sortie de l’expérience). Sinon, la propriété webServiceOutputs est omise.
-   Si le point de terminaison de votre expérience expose des éléments globalParameters, ceux-ci figurent dans la propriété globalParameters de l’activité sous forme de paires clé/valeur. Sinon, la propriété globalParameters est omise. Les clés sont sensibles à la casse. Des [fonctions Azure Data Factory](data-factory-scheduling-and-execution.md#data-factory-functions-reference) peuvent être utilisées dans les valeurs.
- Des jeux de données supplémentaires peuvent être inclus dans les propriétés d’entrée et de sortie de l’activité, sans être référencés dans l’activité typeProperties. Ceux-ci contrôlent l’exécution à l’aide de dépendances des tranches. Sinon, ils sont ignorés par l’activité AzureMLBatchExecution.


## Mettre à jour les modèles Azure ML à l’aide de l’activité des ressources de mise à jour
Au fil du temps, les modèles prédictifs dans les expériences de notation Azure ML doivent être reformés à l’aide de nouveaux jeux de données d’entrée. Une fois que vous avez fini la reformation, vous souhaitez mettre à jour le service web de notation avec le modèle ML reformé. Les étapes classiques pour activer la reformation et la mise à jour des modèles Azure ML via les services web sont les suivantes :

1. Créez une expérience dans [Azure ML Studio](https://studio.azureml.net).
2. Lorsque vous êtes satisfait du modèle, utilisez Azure ML Studio pour publier des services web à la fois pour **l’expérience de formation** et l’expérience de notation/**prédictive**.

Le tableau suivant décrit les services web utilisés dans cet exemple. Pour plus d’informations, consultez [Reformation des modèles Machine Learning par programme](../machine-learning/machine-learning-retrain-models-programmatically.md).

| Type du service web | description 
| :------------------ | :---------- 
| **Service web de formation** | Reçoit les données de formation et produit le ou les modèles formés. La sortie de la reformation est un fichier .ilearner dans un Blob Storage Azure. Le **point de terminaison par défaut** est automatiquement créé pour vous lorsque vous publiez l’expérience de formation en tant que service web. Vous pouvez créer d’autres points de terminaison, mais l’exemple utilise uniquement le point de terminaison par défaut. |
| **Service web de notation** | Reçoit des exemples de données sans étiquette et effectue des prédictions. La sortie de la prédiction peut prendre plusieurs formes, comme un fichier .csv ou des lignes dans une base de données SQL Azure, selon la configuration de l’expérience. Le point de terminaison par défaut est automatiquement créé pour vous lorsque vous publiez l’expérience prédictive comme un service web. Vous devrez créer le deuxième **point de terminaison (qui n’est pas le point de terminaison par défaut) pouvant être mis à jour** à l’aide du [portail Azure](https://manage.windowsazure.com). Vous pouvez créer d’autres points de terminaison, mais cet exemple utilise uniquement un point de terminaison autre que par défaut pouvant être mis à jour. Pour connaître les étapes, consultez l’article [Créer des points de terminaison](../machine-learning/machine-learning-create-endpoint.md).       
 
Le schéma suivant illustre la relation entre points de terminaison de formation et de notation dans Azure ML.

![Services web](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Vous pouvez appeler le **service web de formation** à l’aide de l’**activité d’exécution par lot Azure ML**. Cette opération est similaire à l’appel d’un service web Azure ML (service web de notation) pour les données de notation. Les sections ci-dessus expliquent de manière détaillée comment appeler un service web Azure ML à partir d’un pipeline Azure Data Factory.
  
Vous pouvez appeler le **service web de notation** à l’aide de **l’activité des ressources de mise à jour Azure ML** pour mettre à jour le service web avec le modèle qui vient d’être formé. Comme indiqué dans le tableau ci-dessus, vous devez créer et utiliser le point de terminaison autre que par défaut pouvant être mis à jour. Vous devez également mettre à jour tous les services liés existants dans votre Data Factory pour utiliser le point de terminaison autre que par défaut, afin qu’ils utilisent toujours le dernier modèle reformé.

Le scénario suivant fournit des explications plus approfondies avec un exemple pour la reformation et la mise à jour de modèles Azure ML à partir d’un pipeline Azure Data Factory.
 
### Scénario : reformation et mise à jour d’un modèle Azure ML
Cette section fournit un exemple de pipeline qui utilise l’**activité d’exécution par lots Azure ML** pour reformer un modèle et l’**activité des ressources de mise à jour Azure ML** pour mettre à jour le modèle dans le service web de notation. Il fournit également des extraits de code JSON pour tous les services liés, jeux de données et éléments de pipeline dans l’exemple.

Voici la vue schématique de l’exemple de pipeline. Comme vous pouvez le voir, l’activité d’exécution par lots Azure ML prend l’entrée de formation et génère une sortie de formation (fichier iLearner). L’activité des ressources de mise à jour Azure ML prend cette sortie de formation et met à jour le modèle dans le point de terminaison de service web de notation. L’activité des ressources de mise à jour ne génère aucune sortie. placeholderBlob est simplement un jeu de données de sortie factice requis par le service Azure Data Factory pour exécuter le pipeline.

![schéma du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### Service lié Azure Blob Storage :
Azure Storage contient les données suivantes :

- Données de formation. Il s’agit des données d’entrée pour le service web de formation Azure ML.
- Fichier iLearner. Il s’agit des données de sortie issues du service web de formation Azure ML. C’est également l’entrée de l’activité des ressources de mise à jour.
   
Voici la définition d’exemple JSON du service lié :

	{
		"name": "StorageLinkedService",
	  	"properties": {
	    	"type": "AzureStorage",
			"typeProperties": {
	    		"connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
			}
		}
	}


#### Jeu de données d’entrée de formation
Le jeu de données suivant représente les données de formation d’entrée pour le service web de formation Azure ML. L’activité d’exécution par lots Azure ML prend ce jeu de données comme entrée.

	{
	    "name": "trainingData",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "labeledexamples",
	            "fileName": "labeledexamples.arff",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "policy": {          
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

#### Jeu de données de sortie de formation :
Le jeu de données suivant représente le fichier iLearner de sortie issu du service web de formation Azure ML. L’activité d’exécution par lots Azure ML génère ce jeu de données. Ce jeu de données est également l’entrée de l’activité des ressources de mise à jour Azure ML.

	{
	    "name": "trainedModelBlob",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "trainingoutput",
	            "fileName": "model.ilearner",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        }
	    }
	}

#### Service lié pour le point de terminaison de formation Azure ML 
L’extrait de code JSON suivant définit un service lié Azure Machine Learning qui pointe vers le point de terminaison par défaut du service web de formation.

	{	
		"name": "trainingEndpoint",
	  	"properties": {
	    	"type": "AzureML",
	    	"typeProperties": {
	    		"mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
	      		"apiKey": "myKey"
	    	}
	  	}
	}

Dans **Azure ML Studio**, procédez comme suit pour obtenir les valeurs pour **mlEndpoint** et **apiKey** :

1. Cliquez sur **SERVICES WEB** dans le menu de gauche.
2. Cliquez sur le **service web de formation** dans la liste des services web.
3. Cliquez sur Copier en regard de la zone de texte **Clé API** pour copier la clé API dans le Presse-papiers. Collez la clé dans l’éditeur JSON Data Factory.
4. Dans **Azure ML Studio**, cliquez sur le lien **EXÉCUTION PAR LOT**, copiez l’**URI DE DEMANDE** à partir de la section **DEMANDE** et collez-le dans l’éditeur JSON Data Factory.


#### Service lié pour le point de terminaison de notation pouvant être mis à jour Azure ML :
L’extrait de code JSON suivant définit un service lié Azure Machine Learning qui pointe vers le point de terminaison autre que par défaut pouvant être mis à jour du service web de notation.

	{
	    "name": "updatableScoringEndpoint2",
	    "properties": {
	        "type": "AzureML",
	        "typeProperties": {
	            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
	            "apiKey": "endpoint2Key",
	            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
	        }
	    }
	}


Avant de créer et de déployer un service lié Azure ML, suivez les étapes de l’article [Créer des points de terminaison](../machine-learning/machine-learning-create-endpoint.md) pour créer un deuxième point de terminaison (autre que celui par défaut et pouvant être mis à jour) pour le service web de notation.

Après avoir créé le point de terminaison autre que celui par défaut et pouvant être mis à jour, cliquez sur **EXÉCUTION PAR LOT** pour obtenir la valeur de l’URI pour la propriété JSON **mlEndpoint** et cliquez sur le lien **METTRE À JOUR LA RESSOURCE** pour obtenir la valeur de l’URI pour la propriété JSON **updateResourceEndpoint**. La clé API est sur la page du point de terminaison même (dans le coin inférieur droit).

![point de terminaison pouvant être mis à jour](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### Jeu de données de sortie de l’espace réservé
L’activité des ressources de mise à jour Azure ML ne génère pas de sortie mais, dans Azure Data Factory, un jeu de données de sortie est requis pour la planification de pipeline. Par conséquent, un jeu de données factice/espace réservé est utilisé dans cet exemple.

	{
	    "name": "placeholderBlob",
	    "properties": {
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "any",
	            "format": {
	                "type": "TextFormat"
	            }
	        }
	    }
	}


#### Pipeline
Le pipeline a deux activités : **AzureMLBatchExecution** et **AzureMLUpdateResource**. L’activité d’exécution par lots Azure ML prend les données de formation comme entrée et génère le fichier .iLearner comme sortie. L’activité appelle le service web de formation (expérience de formation exposée comme un service web) avec les données de formation d’entrée et reçoit le fichier iLearner du service web. placeholderBlob est simplement un jeu de données de sortie factice requis par le service Azure Data Factory pour exécuter le pipeline.

![schéma du pipeline](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


	{
	    "name": "pipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "retraining",
	                "type": "AzureMLBatchExecution",
	                "inputs": [
	                    {
	                        "name": "trainingData"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "typeProperties": {
	                    "webServiceInput": "trainingData",
	                    "webServiceOutputs": {
	                        "output1": "trainedModelBlob"
	                    }              
	                 },
	                "linkedServiceName": "trainingEndpoint",
	                "policy": {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "02:00:00"
	                }
	            },
	            {
	                "type": "AzureMLUpdateResource",
	                "typeProperties": {
	                    "trainedModelName": "Training Exp for ADF ML [trained model]",
	                    "trainedModelDatasetName" :  "trainedModelBlob"
	                },
	                "inputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "placeholderBlob"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "name": "AzureML Update Resource",
	                "linkedServiceName": "updatableScoringEndpoint2"
	            }
	        ],
	    	"start": "2015-02-13T00:00:00Z",
	   		"end": "2015-02-14T00:00:00Z"
	    }
	}


### Modules Lecteur et Enregistreur

L'utilisation des paramètres de service web passe par un scénario commun : l'utilisation des lecteurs et enregistreurs SQL Azure. Le module Lecteur est utilisé pour charger des données dans une expérience, à partir des services de gestion des données, en dehors d'Azure Machine Learning Studio. Le module Enregistreur sauvegarde quant à lui les données de vos expériences dans les services de gestion des données, en dehors d'Azure Machine Learning Studio.

Pour plus d'informations concernant les modules enregistreur/lecteur Azure Blob/SQL Azure, consultez les rubriques [Lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et [Enregistreur](https://msdn.microsoft.com/library/azure/dn905984.aspx) dans la bibliothèque MSDN. L'exemple de la section précédente utilisait le lecteur et l'enregistreur d'objets blob Azure. Cette section décrit leur utilisation.


## Forum Aux Questions

**Q :** J’ai plusieurs fichiers qui sont générés par mes pipelines Big Data. L’activité AzureMLBatchExecution peut-elle fonctionner sur tous les fichiers ?

**R :** Oui. Pour plus d’informations, consultez la section **Utilisation d’un module lecteur pour lire les données de plusieurs fichiers dans le stockage d’objets blob Azure**.

## Activité de notation par lots Azure ML
Si vous utilisez l’activité **AzureMLBatchScoring** en vue d’une intégration avec Azure Machine Learning, nous vous recommandons d’utiliser la dernière activité **AzureMLBatchExecution**.

L’activité AzureMLBatchExecution est introduite dans la version d’août 2015 du Kit de développement logiciel (SDK) Azure et d’Azure PowerShell.

Si vous souhaitez continuer à utiliser l’activité AzureMLBatchScoring, poursuivez la lecture de cette section.

### Activité de notation par lots Azure ML utilisant Azure Storage pour l’entrée/la sortie 

	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchScoring",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "ScoringInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "ScoringResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

### Paramètres de service web
Ajoutez une section **typeProperties** à la section **AzureMLBatchScoringActivty** dans le script JSON du pipeline pour spécifier les valeurs des paramètres de service web dans cette section, comme indiqué dans l’exemple suivant :

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Vous pouvez également utiliser les [fonctions de Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) pour transmettre les valeurs aux paramètres de service web, comme indiqué dans l'exemple suivant :

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Les paramètres de service web respectent la casse ; veillez donc à ce que les noms que vous indiquez dans le script JSON de l'activité correspondent à ceux exposés par le service web.

## Voir aussi

- [Article de blog Azure : prise en main d’Azure Data Factory et d’Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=AcomDC_0713_2016-->