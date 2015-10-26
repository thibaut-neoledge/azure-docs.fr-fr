<properties 
	pageTitle="Déplacer des données à partir d’Oracle | Azure Data Factory" 
	description="Découvrez comment déplacer des données vers et depuis une base de données Oracle locale à l’aide d’Azure Data Factory." 
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
	ms.date="08/26/2015" 
	ms.author="spelluru"/>

# Déplacer des données à partir d’Oracle en local à l’aide d’Azure Data Factory 

Cet article explique comment utiliser l’activité de copie Data factory pour déplacer des données depuis Oracle vers un autre magasin de données. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasins de données prises en charge.

## Exemple : copie de données d’Oracle vers Azure Blob

L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties). 
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) comme source et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) comme récepteur.

L'exemple copie toutes les heures les données d’une table d’une base de données Oracle locale vers un objet blob. Pour plus d'informations sur les diverses propriétés utilisées dans l'exemple ci-dessous, consultez la documentation sur ces différentes propriétés dans les sections qui suivent les exemples.

**Service lié Oracle :**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Jeu de données d’entrée Oracle :**

L'exemple suppose que vous avez créé une table « MyTable » dans Oracle et qu'elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de « external » : « true » et la spécification de la stratégie externalData informent Data Factory qu'il s'agit d'une table externe à Data Factory et non produite par une activité dans Data Factory.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
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

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.
	
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


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **oracleReaderQuery** sélectionne les données de la dernière heure à copier.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Propriétés du service lié Oracle

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Oracle.

Propriété | Description | Requis
-------- | ----------- | --------
type | Le type de propriété doit être défini sur : **OnPremisesOracle** | Oui
connectionString | Spécifier les informations requises pour la connexion à l’instance de base de données Oracle pour la propriété connectionString. | Oui 
gatewayName | Nom de la passerelle qui sera utilisée pour se connecter au serveur Oracle local | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Oracle locale, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)
## Propriétés de type du jeu de données Oracle

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (Oracle, Azure Blob, Azure Table, etc.).
 
La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type OracleTable a les propriétés suivantes.

Propriété | Description | Requis
-------- | ----------- | --------
TableName | Nom de la table dans la base de données Oracle à laquelle le service lié fait référence. | Oui

## Propriétés de type de l'activité de copie Oracle

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

**Remarque :** l’activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d'une activité de copie, lorsque la source est de type SqlSource, les propriétés suivantes sont disponibles dans la section typeProperties :

Propriété | Description |Valeurs autorisées | Requis
-------- | ----------- | ------------- | --------
oracleReaderQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. 
Par exemple : select * from MyTable <p>Si non spécifié, l’instruction SQL exécutée : select from MyTable.</p> | Non

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type pour Oracle

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion à partir de types de source natifs en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données à partir d’Oracle, les mappages suivants seront utilisés pour convertir le type de données Oracle en type .NET et vice versa.

Type de données Oracle | Type de données .NET Framework.
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Décimal
INTEGER | Décimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Décimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Oct15_HO3-->