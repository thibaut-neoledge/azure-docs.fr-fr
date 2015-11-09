<properties
	pageTitle="Déplacer des données vers et depuis Azure Data Lake Store | Azure Data Factory"
	description="Découvrez comment déplacer des données depuis et vers le stockage Azure Data Lake Store à l’aide d’Azure Data Factory."
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
	ms.date="10/13/2015"
	ms.author="spelluru"/>

# Déplacer des données vers et depuis Azure Data Lake Store à l’aide d’Azure Data Factory
Cet article explique comment vous pouvez utiliser l’activité de copie dans la fabrique Azure Data pour déplacer des données vers Azure Data Lake Store depuis autre un magasin de données et déplacer des données depuis un autre magasin de données et vice-versa. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasin de données prises en charge.

> [AZURE.NOTE]Vous devez créer un compte Azure Data Lake Store avant de créer un pipeline avec l’activité de copie pour déplacer des données vers/depuis Azure Data Lake Store. Pour plus d’informations sur Azure Data Lake Store, consultez [Prise en main d’Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Veuillez consulter le [didacticiel Générer votre premier pipeline](data-factory-build-your-first-pipeline.md) pour connaître les étapes détaillées de la création d’une fabrique de données, de services liés, de jeux de données et d’un pipeline. Utilisez les extraits de code JSON avec Data Factory Editor, Visual Studio ou Azure PowerShell pour créer les entités Data Factory.

## Exemple : copie de données depuis un objet Blob Azure vers Azure Data Lake Store
L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureStorage](#azure-storage-linked-service-properties).
2.	Un service lié de type [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](#azure-blob-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](#azure-blob-copy-activity-type-properties) et [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

L’exemple copie des données appartenant à une série horaire depuis un stockage d’objets blobs vers Azure Data Lake Store toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.


**Service lié Azure Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Service lié Azure Data Lake :**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Pour créer un service lié Azure Data Lake lié à l’aide de Data Factory Editor
La procédure qui suit décrit les étapes nécessaires à la création d’un service lié Azure Data Lake Store à l’aide de Data Factory Editor.

1. Cliquez sur **Nouvelle banque de données** dans la barre de commandes et sélectionnez **Azure Data Lake Store**.
2. Dans l’éditeur JSON, pour la propriété **datalakeUri**, saisissez l’URI correspondant au lac de données.
3. Cliquez sur le bouton **Autoriser** de la barre de commandes. Une fenêtre contextuelle doit apparaître.

	![Bouton Autoriser](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. Utilisez vos informations d’identification pour vous connecter et la propriété **autorisation** de JSON doit se voir affecter une valeur maintenant.
5. (facultatif) Spécifiez les valeurs des paramètres facultatifs tels que **accountName**, **subscriptionID** et **resourceGroupName** dans JSON (ou) supprimer ces propriétés à partir de JSON.
6. Cliquez sur l’option **Déployer** de la barre de commandes pour déployer le service lié.


**Jeu de données d'entrée d'objet Blob Azure :**

Les données sont récupérées à partir d'un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d’accès et du fichier de dossier pour l’objet Blob sont évalués dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois et le jour de l’heure de début et le nom de fichier utilise la partie heure de l’heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ]
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
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


**Jeu de données de sortie Azure Data Lake :**

L’exemple copie des données dans un magasin Azure Data Lake. De nouvelles données sont copiées dans le magasin Data Lake toutes les heures.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": " AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Pipeline avec une activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **AzureDataLakeStoreSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
    		]
		}
	}

## Exemple : copie des données depuis Azure Data Lake Store vers un objet Blob Azure
L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.	Un service lié de type [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](#azure-blob-dataset-type-properties).
5.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) et [BlobSink](#azure-blob-copy-activity-type-properties).

L’exemple copie des données appartenant à une série horaire depuis un magasin Azure Data Lake vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Data Lake Store :**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE]Consultez les étapes de l’exemple précédent pour obtenir l’URL d’autorisation.

**Service lié Azure Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données Azure Data Lake de sortie :**

La définition de **« external » : true** et la spécification de la stratégie **externalData** informe le service Azure Data Factory qu’il s’agit d’une table externe à la fabrique de données et non produite par une activité dans la fabrique de données.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
		    },
		    "external": true,
		    "availability": {
		    	"frequency": "Hour",
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

**Jeu de données de sortie d'objet Blob Azure :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline avec l'activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **AzureDataLakeStoreSource** et le type **sink** est défini sur **BlobSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}


## Propriétés de service lié Azure Data Lake Store

Vous pouvez lier un compte de stockage Azure à une Azure Data Factory à l'aide d'un service lié Azure Storage. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété type doit être définie sur : **AzureDataLakeStore** | Oui |
| dataLakeUri | Spécifiez des informations à propos du compte Azure Data Lake Store. Il est au format suivant : https://<Azure Data Lake account name>.azuredatalake.net/webhdfs/v1 | Oui |
| autorisation | Cliquez sur le bouton **Autoriser** dans **Data Factory Editor** et saisissez vos informations d’identification, ce qui affecte l’URL d’autorisation générée automatiquement à cette propriété. | Oui |
| sessionId | ID de session OAuth issu de la session d’autorisation oauth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Il est généré automatiquement lorsque vous utilisez Data Factory Editor. | Oui |  
| accountName | Nom du compte de lac de données | Non |
| subscriptionId | ID d’abonnement Azure | Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). |
| nom\_groupe\_ressources | Nom du groupe de ressources Azure | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé). |


## Propriétés de type du jeu de données Azure Data Lake

Pour obtenir une liste complète des sections et propriétés JSON disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement, le format, etc. des données dans le magasin de données. La section typeProperties correspondant au jeu de données de type **AzureDataLakeStore** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| folderPath | Chemin d’accès au conteneur et au dossier dans le magasin Azure Data Lake | Oui |
| fileName | <p>Le nom du fichier dans le magasin Azure Data Lake. fileName est facultative. </p><p>Si vous spécifiez un nom de fichier, l’activité (y compris la copie) fonctionne sur le fichier spécifique.</p><p>Quand fileName n’est pas spécifié, la copie inclut tous le fichier dans folderPath pour le jeu de données d’entrée.</p><p>Quand fileName n’est pas spécifié pour un jeu de données de sortie, le nom du fichier généré est au format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)</p> | Non |
| partitionedBy | partitionedBy est une propriété facultative. Vous pouvez l'utiliser pour spécifier un folderPath dynamique et le nom de fichier pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. Consultez la section propriété Leveraging partitionedBy ci-dessous pour obtenir plus d’informations et des exemples. | Non |

### Utilisation de la propriété partitionedBy
Comme mentionné ci-dessus, vous pouvez spécifier des valeurs folderPath et filename dynamiques pour les données de série chronologique avec la section **partitionedBy**, les macros Data Factory et les variables système : SliceStart et SliceEnd, qui indiquent les heures de début et de fin pour un segment spécifique de données.

Consultez les articles [Création de jeux de données](data-factory-create-datasets.md) et [Planification et exécution](data-factory-scheduling-and-execution.md) pour mieux comprendre les jeux de données de série chronologique, la planification et les segments.

#### Exemple 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy":
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Dans l'exemple ci-dessus {Slice} est remplacé par la valeur de la variable système Data Factory SliceStart au format (AAAAMMJJHH) spécifié. SliceStart fait référence à l'heure de début du segment. folderPath est différent pour chaque segment. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### Exemple 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy":
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
	],

Dans l'exemple ci-dessus, l'année, le mois, le jour et l'heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et fileName.

## Propriétés de type activité de copie Azure Data Lake  
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

**AzureDataLakeStoreSource** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. | True (valeur par défaut), False | Non |



**AzureDataLakeStoreSink** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Spécifie le comportement de copie. | <p>**PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible, par exemple : le chemin d’accès relatif du fichier source vers le dossier source est identique au chemin d’accès relatif du fichier cible vers le dossier cible.</p><p>**FlattenHierarchy :** tous les fichiers du dossier source sont dans le premier niveau du dossier cible. Les fichiers cible auront un nom généré automatiquement.</p><p>**MergeFiles :** fusionne tous les fichiers du dossier source vers un seul fichier. Si le nom de fichier/d’objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement.</p> | Non |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Nov15_HO1-->