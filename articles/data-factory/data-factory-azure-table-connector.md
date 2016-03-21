<properties 
	pageTitle="Déplacer des données vers/depuis Azure Table | Microsoft Azure" 
	description="Découvrez comment déplacer des données depuis et vers le stockage Azure Table à l’aide d’Azure Data Factory." 
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
	ms.date="02/24/2016" 
	ms.author="spelluru"/>

# Déplacer des données vers et depuis Azure Table à l’aide d’Azure Data Factory

Cet article décrit comment vous pouvez utiliser l’activité de copie dans une fabrique Azure Data Factory pour déplacer des données à partir d’un autre magasin de données vers Table Azure et vice versa. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Les exemples suivants indiquent comment copier des données vers et depuis Azure Table Storage et Azure Blob Storage. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.


## Exemple : copie de données à partir de Table Azure vers un objet Blob Azure

L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table et l’objet blob).
2.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureTable](#azure-table-dataset-type-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.	Le [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [AzureTableSource](#azure-table-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

L'exemple copie des données appartenant à la partition par défaut dans une Table Azure vers un objet Blob, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory prend en charge deux types de service lié Azure Storage : **AzureStorage** et **AzureStorageSas**. Pour le premier, vous spécifiez la chaîne de connexion qui inclut la clé de compte, et pour le second, vous spécifiez l'Uri de signature d’accès partagé (SAP). Pour plus d’informations, consultez la section [Services liés](#linked-services).

**Jeu de données d'entrée Table Azure :**

L'exemple suppose que vous avez créé une table « MyTable » dans la Table Azure.
 
La définition de « external » : « true » et la spécification de la stratégie externalData informent Data Factory qu'il s'agit d'une table externe à Data Factory et non produite par une activité dans Data Factory.

	{
	  "name": "AzureTableInput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
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

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
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

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **AzureTableSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée avec la propriété **AzureTableSourceQuery** sélectionne les données à copier de la partition par défaut toutes les heures.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
		    "end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
				{
	        		"name": "AzureTabletoBlob",
	        		"description": "copy activity",
	        		"type": "Copy",
	        		"inputs": [
	          			{
		            		"name": "AzureTableInput"
		        		}
	        		],
	        		"outputs": [
	          			{
	          	  			"name": "AzureBlobOutput"
		          		}
		        	],
		        	"typeProperties": {
		          		"source": {
		            		"type": "AzureTableSource",
				            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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

## Exemple : copie de données à partir d'un objet Blob Azure vers Table Azure

L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilisé pour la table et l’objet blob).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureTable](#azure-table-dataset-type-properties). 
4.	Le [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [AzureTableSink](#azure-table-copy-activity-type-properties). 


L'exemple copie des données appartenant à une série horaire à partir d'un objet Blob Azure vers une table dans une base de données Table Azure, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure Storage (pour Table Azure et objet Blob Azure) :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Azure Data Factory prend en charge deux types de service lié Azure Storage : **AzureStorage** et **AzureStorageSas**. Pour le premier, vous spécifiez la chaîne de connexion qui inclut la clé de compte, et pour le second, vous spécifiez l'Uri de signature d’accès partagé (SAP). Pour plus d’informations, consultez la section [Services liés](#linked-services).

**Jeu de données d'entrée d'objet Blob Azure :**

Les données sont récupérées à partir d'un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d’accès et du fichier de dossier pour l’objet Blob sont évalués dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois et le jour de l'heure de début et le nom de fichier utilise la partie heure de l'heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
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

**Jeu de données de sortie Table Azure :**

L'exemple copie les données dans une table nommée « MyTable » dans Table Azure. Vous devez créer la table dans Table Azure avec le même nombre de colonnes que le fichier CSV d'objets Blob doit contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline avec l'activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **AzureTableSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoTable",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureTableOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "AzureTableSink",
	            "writeBatchSize": 100,
	            "writeBatchTimeout": "01:00:00"
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

## Services liés
Il existe deux types de services liés que vous pouvez utiliser pour lier un stockage d'objets blob Azure à une fabrique de données Azure. Il s’agit des services liés **AzureStorage** et **AzureStorageSas**. Le service lié Azure Storage fournit à la fabrique de données un accès global à Azure Storage. Tandis que le service lié Azure de stockage SAP (signature d'accès partagé) fournit à la fabrique de données un accès restreint/limité dans le temps à Azure Storage. Il n'existe aucune différence entre ces deux services liés. Choisissez le service lié qui répond à vos besoins. Les sections suivantes expliquent plus en détail ces deux services liés.

[AZURE.INCLUDE [données-fabrique-azure-storage-liés-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Propriétés de type du jeu de données Table Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section **typeProperties** pour le jeu de données de type **AzureTable** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l'instance de base de données Table Azure à laquelle le service lié fait référence. | Oui

### Schéma par Data Factory
Pour les magasins de données sans schéma comme Azure Table, le service Data Factory déduit le schéma de l’une des manières suivantes :

1.	Si vous spécifiez la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory respecte cette structure en tant que schéma. Dans ce cas, si une ligne ne contient pas de valeur pour une colonne, une valeur null est fournie pour celle-ci.
2.	Si vous ne spécifiez pas la structure des données à l’aide de la propriété **structure** dans la définition du jeu de données, le service Data Factory déduit le schéma à l’aide de la première ligne dans les données. Dans ce cas, si la première ligne ne contient pas le schéma complet, certaines colonnes ne seront pas incluses dans le résultat de l’opération de copie.

Par conséquent, pour les sources de données sans schéma, la meilleure pratique consiste à définir la structure des données à l’aide de la propriété **structure**.

## Propriétés de type de l'activité de copie Table Azure

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d’activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

**AzureTableSource** prend en charge les propriétés suivantes dans la section typeProperties :

Propriété | Description | Valeurs autorisées | Requis
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête de table Azure. Voir les exemples ci-dessous. | Non
azureTableSourceIgnoreTableNotFound | Indiquer si l'exception de la table n'existe pas. | TRUE<br/>FALSE | Non |

### Exemples azureTableSourceQuery

Si la colonne de table Azure est de type chaîne :

	azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Si la colonne de table Azure est de type datetime:

	"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** prend en charge les propriétés suivantes dans la section typeProperties :


Propriété | Description | Valeurs autorisées | Requis  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Valeur de clé de partition par défaut qui peut être utilisée par le récepteur. | Valeur de chaîne. | Non 
azureTablePartitionKeyName | Nom de colonne spécifié par l'utilisateur, dont les valeurs de colonne sont utilisées comme clé de partition. Si aucune valeur n'est spécifiée, AzureTableDefaultPartitionKeyValue est utilisée comme clé de partition. | Nom de colonne. | Non |
azureTableRowKeyName | Nom de colonne spécifié par l'utilisateur, dont les valeurs de colonne sont utilisées comme clé de ligne. Si aucune valeur n'est spécifiée, un GUID est utilisé pour chaque ligne. | Nom de colonne. | Non  
azureTableInsertType | Mode d'insertion des données dans une table Azure. | merge<br/>replace | Non 
writeBatchSize | Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte. | Entier compris entre 1 et 100 (unité = nombre de lignes) | Non (Valeur par défaut = 100) 
writeBatchTimeout | Insère des données dans la table Azure lorsque la valeur de writeBatchSize ou writeBatchTimeout est atteinte | (Unité = intervalle de temps) Exemple : « 00:20:00 » (20 minutes) | Non (Valeur par défaut du délai d'attente du stockage client par défaut : 90 secondes)

### azureTablePartitionKeyName
Vous devez mapper une colonne source sur une colonne de destination à l’aide de la propriété JSON translator pour pouvoir utiliser la colonne de destination comme azureTablePartitionKeyName.

Dans l’exemple suivant, la colonne source DivisionID est mappée sur la colonne de destination DivisionID.

	"translator": {
		"type": "TabularTranslator",
		"columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
	} 

EmpID est spécifie en tant que clé de partition.

	"sink": {
		"type": "AzureTableSink",
		"azureTablePartitionKeyName": "DivisionID",
		"writeBatchSize": 100,
		"writeBatchTimeout": "01:00:00"
	}


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type de Table Azure

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante.

1. Conversion à partir de types de source natifs en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Pendant le déplacement de données à partir de et vers Table Azure, les [mappages suivants définis par le service de Table Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) sont utilisés à partir des types OData Table Azure vers le type .NET et vice versa.

| Type de données OData | Type .NET | Détails |
| --------------- | --------- | ------- |
| Edm.Binary | byte | Tableau d'octets jusqu'à 64 Ko. |
| Edm.Boolean | valeur booléenne | Valeur booléenne. |
| Edm.DateTime | DateTime | Valeur de 64 bits exprimée en temps universel coordonné (UTC). La plage DateHeure prise en charge commence à partir de 12:00 minuit, le 1er janvier 1601 apr. J.C. (NOTRE ÈRE), UTC. La plage se termine le 31 décembre 9999. |
| Edm.Double | double | Valeur à virgule flottante de 64 bits. |
| Edm.Guid | Guid | Identificateur global unique de 128 bits. |
| Edm.Int32 | Int32 ou int | Nombre entier 32 bits. |
| Edm.Int64 | Int64 ou long | Nombre entier 64 bits. |
| Edm.String | String | Valeur encodée en UTF-16. La taille des valeurs de chaîne peut aller jusqu'à 64 Ko. |

### Exemple de conversion de type

L'exemple suivant montre la copie de données à partir d'un objet Blob Azure vers Table Azure avec des conversions de type.

Supposons que le jeu de données Blob soit au format CSV et contienne trois colonnes. L'une d'elles est une colonne datetime avec un format de date et d'heure personnalisé comprenant un nom abrégé en français pour le jour de la semaine.

Vous allez définir le jeu de données d'objets blob source comme suit, ainsi que des définitions de type pour les colonnes.
	
	{
	    "name": " AzureBlobInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
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

Étant donné le mappage de type OData Table Azure vers le type .NET ci-dessus, vous devez définir la table dans Table Azure avec le schéma suivant.

**Schéma de Table Azure :**

Nom de la colonne | Type
----------- | --------
userid | Edm.Int64
name | Edm.String 
lastlogindate | Edm.DateTime

Ensuite, vous allez définir le jeu de données Table Azure comme suit. Il est inutile de spécifier la section « structure » à l'aide des informations de type, car celles-ci sont déjà spécifiées dans le magasin de données sous-jacent.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Dans ce cas, Data Factory effectuera automatiquement les conversions de type, y compris pour le champ Datetime avec son format date/heure personnalisé, en utilisant la culture fr-fr lors du déplacement des données à partir de l'objet blob vers Table Azure.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0309_2016-->