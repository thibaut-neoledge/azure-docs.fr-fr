<properties 
	pageTitle="Créer des pipelines prédictifs à l’aide de l’activité Azure Machine Learning Batch Execution | Microsoft Azure" 
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
	ms.date="08/24/2015" 
	ms.author="spelluru"/>

# Créer des pipelines prédictifs à l’aide de l’activité Azure Machine Learning Batch Execution   
## Vue d’ensemble

> [AZURE.NOTE]Pour prendre en main le service Azure Data Factory rapidement, consultez les articles [Présentation d’Azure Data Factory](data-factory-introduction.md) et [Concevez votre premier pipeline](data-factory-build-your-first-pipeline.md).

Azure Data Factory vous permet de créer facilement des pipelines qui s’appuient sur un service web [Azure Machine Learning][azure-machine-learning] publié pour l’analyse prédictive. À l’aide d’Azure Data Factory, vous pouvez utiliser des pipelines Big Data (par exemple, Pig et Hive) pour traiter les données provenant de diverses sources de données et utiliser les services web Azure Machine Learning pour effectuer des prédictions sur les données d’un lot.

Vous utilisez Azure Data Factory pour orchestrer le déplacement et le traitement des données, puis pour effectuer une exécution par lot à l’aide d’Azure Machine Learning. Pour ce faire, vous devez effectuer les opérations suivantes :

1. Créer un service lié Azure Machine Learning. Les éléments suivants seront nécessaires :
	1. L’**URI de demande** pour l’API d’exécution de lot. Vous trouverez l’URI de demande en cliquant sur le lien **BATCH EXECUTION** dans la page des services web (voir ci-dessous).
	1. La **clé API** pour le service web Azure Machine Learning publié. Vous trouverez la clé API en cliquant sur le service web que vous avez publié. 
 2. L’activité **AzureMLBatchExecution**.

	![Tableau de bord Machine Learning](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![URI de lot](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Scénario : utilisation d’entrées/sorties de service web qui font référence à des données du stockage d’objets blob Azure
Dans ce scénario, le service web Azure Machine Learning effectue des prédictions à l’aide des données d’un fichier dans un stockage d’objets blob Azure et stocke les résultats des prédictions dans le stockage d’objets blob. Le code JSON suivant définit un pipeline Azure Data Factory avec une activité AzureMLBatchExecution. L’activité a le jeu de données **DecisionTreeInputBlob** comme entrée et **DecisionTreeResultBlob** comme sortie. **DecisionTreeInputBlob** est transmis comme entrée du service web à l’aide de la propriété JSON **webServiceInput**, tandis que **DecisionTreeResultBlob** est transmis à l’aide de la propriété JSON **webServiceOutputs** en tant que sortie. Seuls les jeux de données qui sont des entrées/sorties de l’activité peuvent être passés en tant que sorties et entrées de service web.


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
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
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

> [AZURE.NOTE]Seules les entrées et sorties de l’activité AzureMLBatchExecution peuvent être transmises en tant que paramètres au service web. Par exemple, dans l’extrait de code JSON ci-dessus, DecisionTreeInputBlob est une entrée de l’activité AzureMLBatchExecution, qui est transmise comme entrée au service web via le paramètre webServiceInput.

### Exemple

Cet exemple utilise Azure Storage pour stocker les données d'entrée et de sortie.

Nous vous recommandons de passer en revue le didacticiel [Concevez votre premier pipeline en utilisant Azure Data Factory][adf-build-1st-pipeline] avant de lire cet exemple et d’utiliser Data Factory Editor pour créer des artefacts Data Factory (services liés, jeux de données, pipeline).
 

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

2. Créez le **jeu de données** d’**entrée** Azure Data Factory. Notez que contrairement à d’autres jeux de données Data Factory, celle-ci doit contenir les valeurs **folderPath** et **fileName**. Vous pouvez utiliser le partitionnement et provoquer l'exécution de chaque lot (chaque tranche de données) pour traiter ou produire des fichiers d'entrée et de sortie uniques. Vous aurez probablement besoin d'inclure une activité en amont pour convertir l'entrée au format de fichier CSV, et la placer dans le compte de stockage pour chaque tranche. Dans ce cas, n’incluez pas les paramètres **external** et **externalData** indiqués dans l’exemple ci-dessous. Par ailleurs, DecisionTreeInputBlob représente le jeu de données de sortie d’une autre activité.

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

	> [AZURE.NOTE]L’activité AzureMLBatchExecution peut avoir zéro ou plusieurs entrées et une ou plusieurs sorties.

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
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
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

	> [AZURE.NOTE]Définir une entrée pour l’activité AzureMLBatchExecution est facultatif.

## Scénario : utilisation de modules lecteur/enregistreur pour faire référence à des données dans différents stockages

Un autre scénario courant pour créer des expériences Azure ML consiste à utiliser des modules lecteur et enregistreur. Le module lecteur permet de charger des données dans une expérience, tandis que le module enregistreur sert à enregistrer les données issues de cette expérience. Pour plus d’informations sur les modules lecteur et enregistreur, consultez les rubriques [Lecteur](https://msdn.microsoft.com/library/azure/dn905997.aspx) et [Enregistreur](https://msdn.microsoft.com/library/azure/dn905984.aspx) dans la bibliothèque MSDN.

Quand vous utilisez les modules lecteur et enregistreur, nous vous recommandons de recourir à un paramètre de service web pour chaque propriété de ces modules. Ces paramètres web permettent de configurer les valeurs pendant l’exécution. Par exemple, vous pouvez créer une expérience avec un module lecteur qui utilise une base de données Azure SQL Database : XXX.database.windows.net. Une fois le service web déployé, vous pouvez autoriser les consommateurs du service web à spécifier un autre serveur Azure SQL Server appelé YYY.database.windows.net. Vous pouvez utiliser un paramètre de service web pour permettre à cette valeur d’être configurée.

> [AZURE.NOTE]L’entrée et la sortie du service web diffèrent des paramètres de service web. Dans le premier scénario, vous avez vu comment une entrée et une sortie peuvent être spécifiées pour un service web Azure ML. Dans ce scénario, vous allez passer des paramètres pour un service web qui correspondent aux propriétés des modules lecteur/enregistreur.

Examinons un scénario d’utilisation de paramètres de service web. Vous disposez d’un service web Azure Machine Learning déployé qui utilise un module lecteur pour lire les données d’une des sources de données Azure Machine Learning prises en charge (par exemple : Azure SQL Database). Après l’exécution par lots, les résultats sont écrits à l’aide d’un module enregistreur (Azure SQL Database). Aucune entrée ou sortie de service web n’est définie dans les expériences. Dans ce cas, nous vous recommandons de configurer les paramètres de service web appropriés pour les modules lecteur et enregistreur. Vous pouvez ainsi configurer les modules lecteur/enregistreur quand vous utilisez l’activité AzureMLBatchExecution. Vous spécifiez les paramètres de service web dans la section **globalParameters** du code JSON de l’activité comme suit.


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
 
> [AZURE.NOTE]Les paramètres de service web respectent la casse ; veillez donc à ce que les noms que vous indiquez dans le script JSON de l'activité correspondent à ceux exposés par le service web.

### Utilisation d’un module lecteur pour lire les données de plusieurs fichiers dans le stockage d’objets blob Azure
Les pipelines Big Data (Pig, Hive, etc.) peuvent produire un ou plusieurs fichiers de sortie sans extensions. Par exemple, quand vous spécifiez une table Hive externe, les données de cette table peuvent être stockées dans le stockage d’objets blob Azure avec le nom 000000\_0 suivant. Vous pouvez utiliser le module lecteur dans une expérience pour lire plusieurs fichiers et les utiliser pour les prédictions.

Quand vous utilisez le module lecteur dans une expérience Azure Machine Learning, vous pouvez spécifier un objet blob Azure comme entrée. Les fichiers du stockage blob Azure peuvent être les fichiers de sortie (par exemple, 000000\_0) qui sont générés par un script Pig et Hive exécuté sur HDInsight. Le module lecteur vous permet de lire des fichiers (sans extension) en configurant la propriété **Chemin d’accès au conteneur, répertoire ou blob** du module lecteur pour pointer vers le dossier/conteneur où figurent les fichiers, comme indiqué ci-dessous. Notez que l’astérisque (*) **spécifie que tous les fichiers du dossier/conteneur (par exemple, data/aggregateddata/year=2014/month-6/*)** sont lus dans le cadre de l’expérience.

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
 

## Forum Aux Questions

**Q :** j’utilise l’activité AzureMLBatchScoring. Dois-je utiliser l’activité AzureMLBatchExecution à la place ?

**R :** oui. Si vous utilisez l’activité AzureMLBatchScoring en vue d’une intégration Azure Machine Learning, nous vous recommandons d’utiliser la dernière activité AzureMLBatchExecution. Nous déconseillons l’activité AzureMLBatchScoring, qui sera supprimée dans une version ultérieure.

L’activité AzureMLBatchExecution est introduite dans la version d’août 2015 du Kit de développement logiciel (SDK) Azure et d’Azure PowerShell.

L’activité AzureMLBatchExecution ne nécessite pas d’entrée (si des dépendances d’entrée ne sont pas nécessaires). Elle vous permet également d’indiquer de manière explicite si vous souhaitez utiliser l’entrée et la sortie de service web. Si vous choisissez d’utiliser les entrée et sortie de service web, vous pouvez spécifier les jeux de données d’objets blob Azure appropriés à utiliser. En outre, vous pouvez spécifier clairement les valeurs des paramètres de service web qui sont fournies par le service web.

Si vous souhaitez continuer à utiliser l’activité AzureMLBatchScoring, consultez l’article [Activité de notation par lots Azure ML](data-factory-create-predictive-pipelines.md) pour plus d’informations.


**Q :** j’ai plusieurs fichiers qui sont générés par mes pipelines Big Data. L’activité AzureMLBatchExecution peut-elle fonctionner sur tous les fichiers ?

**R :** oui. Pour plus d’informations, consultez la section **Utilisation d’un module lecteur pour lire les données de plusieurs fichiers dans le stockage d’objets blob Azure**.


## Voir aussi

- [Article de blog Azure : prise en main d’Azure Data Factory et d’Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=Oct15_HO3-->