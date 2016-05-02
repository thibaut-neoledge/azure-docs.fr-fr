<properties 
	pageTitle="Déplacer des données vers/depuis Azure SQL Database | Microsoft Azure" 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Déplacer des données vers et depuis Base de données SQL Azure à l’aide d’Azure Data Factory

Cet article décrit comment vous pouvez utiliser l'activité de copie dans une fabrique Azure Data Factory pour déplacer des données vers SQL Azure à partir d'un magasin de données et vice versa. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Les exemples suivants indiquent comment copier des données vers et depuis Base de données SQL Azure et Azure Blob Storage. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.


## Exemple : Copie de données depuis Base de données SQL Azure vers un objet blob Azure

L’exemple ci-dessous présente les éléments suivants :

1. Un service lié de type [AzureSqlDatabase](#azure-sql-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [SqlSource](#azure-sql-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

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

Consultez la section [Service lié SQL Azure](#azure-sql-linked-service-properties) pour obtenir la liste des propriétés prises en charge par ce service lié.

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

Consultez l’article [Objets blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) pour obtenir la liste des propriétés prises en charge par ce service lié.

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

Consultez la section [Propriétés de type du jeu de données SQL Azure](#azure-sql-dataset-type-properties) pour obtenir la liste des propriétés prises en charge par ce type de jeu de données.

**Jeu de données de sortie d'objet Blob Azure**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

Consultez la section [Propriétés de type du jeu de données d’objets blob Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) pour obtenir la liste des propriétés prises en charge par ce type de jeu de données.

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

Dans l'exemple ci-dessus, **sqlReaderQuery** est spécifié pour SqlSource. L'activité de copie exécute cette requête sur la source Azure SQL Database pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).

Si vous ne spécifiez pas sqlReaderQuery ou sqlReaderStoredProcedureName, les colonnes définies dans la section structure du code JSON du jeu de données sont utilisées pour créer une requête (select column1, column2 from mytable) à exécuter sur Azure SQL Database. Si la définition du jeu de données ne possède pas de structure, toutes les colonnes de la table sont sélectionnées.


Consultez la section [Sql Source](#sqlsource) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) pour obtenir la liste des propriétés prises en charge par SqlSource et BlobSink.


## Exemple : Copie de données d’un objet blob Azure vers Base de données SQL Azure

L’exemple ci-dessous présente les éléments suivants :

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

Consultez la section [Service lié SQL Azure](#azure-sql-linked-service-properties) pour obtenir la liste des propriétés prises en charge par ce service lié.

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

Consultez l’article [Objets blob Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) pour obtenir la liste des propriétés prises en charge par ce service lié.

**Jeu de données d'entrée d'objet Blob Azure**

Les données sont récupérées à partir d’un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d’accès et du fichier de dossier pour l’objet Blob sont évalués dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois et le jour de l'heure de début et le nom de fichier utilise la partie heure de l'heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

Consultez la section [Propriétés de type du jeu de données d’objets blob Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) pour obtenir la liste des propriétés prises en charge par ce type de jeu de données.

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

Consultez la section [Propriétés de type du jeu de données SQL Azure](#azure-sql-dataset-type-properties) pour obtenir la liste des propriétés prises en charge par ce type de jeu de données.

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

Consultez la section [Sql Sink](#sqlsink) et [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) pour obtenir la liste des propriétés prises en charge par SqlSink et BlobSource.


## Propriétés du service lié SQL Azure

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié SQL Azure.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété de type doit être définie sur : AzureSqlDatabase | Oui |
| connectionString | Spécifier les informations requises pour la connexion à l’instance de base de données SQL Azure pour la propriété connectionString. | Oui |

**Remarque :** vous devez configurer le pare-feu [Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Vous devez configurer le serveur de base de données pour [autoriser les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). En outre, si vous copiez des données vers SQL Azure à partir d'un emplacement situé en dehors d'Azure, y compris à partir de sources de données sur site avec la passerelle Data Factory, vous devez configurer la plage d'adresses IP appropriée pour l'ordinateur qui envoie des données à SQL Azure.

## Propriétés de type du jeu de données SQL Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l'article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section **typeProperties** pour le jeu de données de type **AzureSqlTable** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans l'instance de base de données SQL Azure à laquelle le service lié fait référence. | Oui |

## Propriétés de type d’activité de copie SQL Azure

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

> [AZURE.NOTE] L'activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

### SqlSource

Dans le cas d'une activité de copie, quand la source est de type **SqlSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable. S’il n’est pas spécifié, l’instruction SQL est exécutée : select from MyTable. | Non |
| sqlReaderStoredProcedureName | Nom de la procédure stockée qui lit les données de la table source. | Nom de la procédure stockée. | Non |
| storedProcedureParameters | Paramètres de la procédure stockée. | Paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non |

Si **sqlReaderQuery** est spécifié pour SqlSource, l'activité de copie exécute cette requête sur la source Azure SQL Database pour obtenir les données. Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).

Si vous ne spécifiez pas sqlReaderQuery ou sqlReaderStoredProcedureName, les colonnes définies dans la section structure du code JSON du jeu de données sont utilisées pour créer une requête (select column1, column2 from mytable) à exécuter sur Azure SQL Database. Si la définition du jeu de données ne possède pas de structure, toutes les colonnes de la table sont sélectionnées.

> [AZURE.NOTE] Lorsque vous utilisez **sqlReaderStoredProcedureName**, vous devez toujours spécifier une valeur pour la propriété **tableName** dans le jeu de données JSON. Il s’agit d’une limitation au niveau du produit pour l’instant. Cependant, il n’existe aucune validation effectuée pour cette table.

### Exemple SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**Définition de la procédure stockée :**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO


### SqlSink 

**SqlSink** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. | (Unité = intervalle de temps) Exemple : « 00:30:00 » (30 minutes). | Non | 
| writeBatchSize | Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize | Nombre entier. (unité = nombre de lignes) | Non (Valeur par défaut = 10000)
| sqlWriterCleanupScript | Requête spécifiée par l'utilisateur pour exécuter l'activité de copie de sorte que les données d'un segment spécifique seront nettoyées. Consultez la section de répétition ci-dessous pour plus de détails. | Une instruction de requête. | Non |
| sliceIdentifierColumnName | Nom de colonne spécifié par l'utilisateur que l'activité de copie doit remplir avec l'identificateur de segment généré automatiquement, qui sera utilisé pour nettoyer les données d'un segment spécifique lors de la réexécution. Consultez la section de répétition ci-dessous pour plus de détails. | Nom d'une colonne avec le type de données binary(32). | Non |
| sqlWriterStoredProcedureName | Nom de la procédure stockée qui met à jour/insère les données dans la table cible. | Nom de la procédure stockée. | Non |
| storedProcedureParameters | Paramètres de la procédure stockée. | Paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non | 
| sqlWriterTableType | Nom du type de table spécifié par l’utilisateur à utiliser dans la procédure stockée qui précède. L’activité de copie place les données déplacées disponibles dans une table temporaire avec ce type de table. Le code de procédure stockée peut ensuite fusionner les données copiées avec les données existantes. | Nom de type de table. | Non |

#### Exemple SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## Colonnes d’identité dans la base de données cible
Cette section fournit un exemple pour copier des données d’une table source sans colonne d’identité vers une table de destination avec une colonne d’identité.

**Table source :**

	create table dbo.SourceTbl
	(
	       name varchar(100),
	       age int
	)

**Table de destination :**

	create table dbo.TargetTbl
	(
	       id int identity(1,1),
	       name varchar(100),
	       age int
	)


Notez que la table cible possède une colonne d’identité.

**Définition du jeu de données JSON source**

	{
	    "name": "SampleSource",
	    "properties": {
	        "published": false,
	        "type": " SqlServerTable",
	        "linkedServiceName": "TestIdentitySQL",
	        "typeProperties": {
	            "tableName": "SourceTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}

**Définition de jeu de données JSON de destination**

	{
	    "name": "SampleTarget",
	    "properties": {
	        "structure": [
	            { "name": "name" },
	            { "name": "age" }
	        ],
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "TestIdentitySQLSource",
	        "typeProperties": {
	            "tableName": "TargetTbl"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": false,
	        "policy": {}
	    }	
	}


Notez que vos tables source et cible ont des schémas différents (la cible possède une colonne supplémentaire avec identité). Dans ce scénario, vous devez spécifier la propriété **structure** dans la définition du jeu de données cible, qui n’inclut pas la colonne d’identité.

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type pour SQL Server et Base de données SQL Azure

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

## Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!---HONumber=AcomDC_0420_2016-->