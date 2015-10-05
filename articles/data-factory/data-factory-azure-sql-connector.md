<properties 
	pageTitle="Déplacer des données vers et depuis Azure SQL | Azure Data Factory" 
	description="Découvrez comment déplacer des données depuis et vers Azure SQL Database à l’aide d’Azure Data Factory." 
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

# Déplacer des données vers et depuis Azure SQL à l’aide d’Azure Data Factory

Cet article décrit comment vous pouvez utiliser l'activité de copie dans une fabrique Azure Data Factory pour déplacer des données vers SQL Azure à partir d'un magasin de données et vice versa. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasin de données prises en charge.

## Exemple : copie de données à partir de SQL Azure vers un objet Blob Azure

L’exemple ci-dessous présente les éléments suivants :

1. Un service lié de type [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties). 
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie des données appartenant à une série horaire à partir d'une table dans une base de données SQL Azure vers un objet Blob, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Service lié Azure Blob Storage**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d'entrée SQL Azure**

L'exemple suppose que vous avez créé une table « MyTable » dans SQL Azure et qu'elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.

La définition de « external » : « true » et la spécification de la stratégie externalData informent le service Azure Data Factory qu'il s'agit d'une table qui est externe à la Data Factory et non produite par une activité dans la Data Factory.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

**Jeu de données de sortie d'objet Blob Azure**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

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

**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **SqlSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.

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
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Exemple : copie de données à partir d'un objet Blob Azure vers SQL Azure

L'exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

L'exemple copie des données appartenant à une série horaire à partir d'un objet Blob Azure vers une table dans une base de données SQL Azure, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.


**Service lié SQL Azure**
	
	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Service lié Azure Blob Storage**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d'entrée d'objet Blob Azure**

Les données sont récupérées à partir d’un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d’accès et du fichier de dossier pour l’objet Blob sont évalués dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois et le jour de l'heure de début et le nom de fichier utilise la partie heure de l'heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

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

**Jeu de données de sortie SQL Azure**

L'exemple copie les données dans une table nommée « MyTable » dans SQL Azure. Vous devez créer la table dans SQL Azure avec le même nombre de colonnes que le fichier CSV d'objets Blob doit contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
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

## Propriétés du service lié SQL Azure

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié SQL Azure.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété de type doit être définie sur : AzureSqlDatabase | Oui |
| connectionString | Spécifier les informations requises pour la connexion à l’instance de base de données SQL Azure pour la propriété connectionString. | Oui |

**Remarque :** vous devez configurer le [pare-feu Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Vous devez configurer le serveur de base de données pour [autoriser les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). En outre, si vous copiez des données vers SQL Azure à partir d'un emplacement situé en dehors d'Azure, y compris à partir de sources de données sur site avec la passerelle Data Factory, vous devez configurer la plage d'adresses IP appropriée pour l'ordinateur qui envoie des données à SQL Azure.

## Propriétés de type du jeu de données SQL Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section **typeProperties** pour le jeu de données de type **AzureSqlTable** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l'instance de base de données SQL Azure à laquelle le service lié fait référence. | Oui |

## Propriétés de type de l'activité de copie SQL Azure

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

> [AZURE.NOTE]L'activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, quand la source est de type **SqlSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable. S’il n’est pas spécifié, l’instruction SQL est exécutée : select from MyTable. | Non |

**SqlSink** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| sqlWriterStoredProcedureName | Nom de procédure stockée spécifié par l’utilisateur pour mettre à jour/insérer des données dans la table cible. | Nom de la procédure stockée. | Non |
| sqlWriterTableType | Nom du type de table spécifié par l’utilisateur à utiliser dans la procédure stockée qui précède. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. | Nom de type de table. | Non |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. | (Unité = intervalle de temps) Exemple : « 00:30:00 » (30 minutes). | Non | 
| sqlWriterCleanupScript | Requête spécifiée par l'utilisateur pour exécuter l'activité de copie de sorte que les données d'un segment spécifique seront nettoyées. Consultez la section de répétition ci-dessous pour plus de détails. | Une instruction de requête. | Non |
| sliceIdentifierColumnName | Nom de colonne spécifié par l'utilisateur que l'activité de copie doit remplir avec l'identificateur de segment généré automatiquement, qui sera utilisé pour nettoyer les données d'un segment spécifique lors de la réexécution. Consultez la section de répétition ci-dessous pour plus de détails. | Nom d'une colonne avec le type de données binary(32). | Non |

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type pour SQL Server et SQL Azure

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion à partir de types de source natifs en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Lors du déplacement de données vers et à partir de SQL Azure, SQL Server, Sybase, les mappages suivants seront utilisés à partir du type SQL en type .NET et vice versa.

Le mappage est identique au mappage du type de données SQL Server pour ADO.NET.

| Type de moteur de base de données SQL Server | Type de .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binaire | Byte |
| bit | Boolean |
| char | String, Char |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DatetimeOffset |
| Décimal | Décimal |
| Attribut FILESTREAM (varbinary(max)) | Byte |
| Float | Double |
| image | Byte | 
| int | Int32 | 
| money | Décimal |
| nchar | String, Char |
| ntext | String, Char |
| numérique | Décimal |
| nvarchar | String, Char |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Décimal | 
| sql\_variant | Objet * |
| texte | String, Char |
| time | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | String, Char |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Envoyer des commentaires
Nous souhaiterions vraiment obtenir vos commentaires sur cet article. Prenez quelques minutes pour nous envoyer vos commentaires par [courrier électronique](mailto:adfdocfeedback@microsoft.com?subject=data-factory-azure-sql-connector.md).



	 

<!---HONumber=Sept15_HO4-->