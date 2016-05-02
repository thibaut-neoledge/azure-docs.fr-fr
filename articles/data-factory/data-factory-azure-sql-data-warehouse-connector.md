<properties 
	pageTitle="Déplacer des données vers/depuis Azure SQL Data Warehouse | Microsoft Azure" 
	description="Découvrez comment déplacer des données depuis et vers Azure SQL Data Warehouse à l’aide d’Azure Data Factory." 
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
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Déplacer des données vers et depuis Azure SQL Data Warehouse à l’aide d’Azure Data Factory

Cet article décrit comment vous pouvez utiliser l’activité de copie dans Azure Data Factory pour déplacer des données depuis Azure SQL Data Warehouse vers un autre magasin de données et vice versa. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les sources et récepteurs de données pris en charge.

Les exemples suivants indiquent comment copier des données vers et depuis Azure SQL Data Warehouse et Azure Blob Storage. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie d’Azure Data Factory.

> [AZURE.NOTE] 
Pour obtenir une vue d’ensemble du service Azure Data Factory, consultez [Présentation d’Azure Data Factory](data-factory-introduction.md).
> 
> Cet article fournit des exemples JSON, mais ne fournit pas d’instructions détaillées pour la création une fabrique de données. Consultez [Didacticiel : Copie de données d’un objet blob Azure vers une base de données SQL Azure](data-factory-get-started.md) pour une brève procédure pas à pas sur l’utilisation de l’activité de copie dans Azure Data Factory.


## Exemple : copie de données depuis Azure SQL Data Warehouse vers un objet Blob Azure

L’exemple ci-dessous présente les éléments suivants :

1. Un service lié de type [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties). 
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie des données appartenant à une série horaire à partir d'une table dans une base de données Azure SQL Data Warehouse vers un objet Blob, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure SQL Data Warehouse :**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d'entrée Azure SQL Data Warehouse :**

L'exemple suppose que vous avez créé une table « MyTable » dans Azure SQL Data Warehouse et qu'elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique.
 
La définition de « external » : « true » et la spécification de la stratégie externalData informent le service Data Factory qu'il s'agit d'une table qui est externe à la Data Factory et non produite par une activité dans la Data Factory.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
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

**Jeu de données de sortie d'objet Blob Azure :**

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

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **SqlDWSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [AZURE.NOTE] Dans l’exemple ci-dessus, **sqlReaderQuery** est spécifié pour SqlDWSource. L’activité de copie exécute cette requête sur la source Azure SQL Data Warehouse pour obtenir les données.
>  
> Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).
>  
> Si vous ne spécifiez pas sqlReaderQuery ou sqlReaderStoredProcedureName, les colonnes définies dans la section structure du code JSON du jeu de données sont utilisées pour créer une requête (select column1, column2 from mytable) à exécuter sur Azure SQL Data Warehouse. Si la définition du jeu de données ne possède pas de structure, toutes les colonnes de la table sont sélectionnées.

## Exemple : copie de données à partir d'un objet Blob Azure vers Azure SQL Data Warehouse

L’exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de type [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) et [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


L'exemple copie des données appartenant à une série horaire à partir d'un objet Blob Azure vers une table dans une base de données Azure SQL Data Warehouse, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Azure SQL Data Warehouse :**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Service lié Azure Blob Storage :**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d'entrée d'objet Blob Azure :**

Les données sont récupérées à partir d'un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d’accès et du fichier de dossier pour l’objet Blob sont évalués dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois et le jour de l'heure de début et le nom de fichier utilise la partie heure de l'heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

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

**Jeu de données de sortie Azure SQL Data Warehouse :**

L'exemple copie les données dans une table nommée « MyTable » dans Azure SQL Data Warehouse. Vous devez créer la table dans Azure SQL Data Warehouse avec le même nombre de colonnes que le fichier CSV d'objets Blob doit contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
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

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **SqlDWSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
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

Vous trouverez une procédure pas à pas dans l'article [Téléchargement de données avec Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) dans la documentation Azure SQL Data Warehouse.

## Propriétés du service lié Azure SQL Data Warehouse

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure SQL Data Warehouse.

Propriété | Description | Requis
-------- | ----------- | --------
type | La propriété de type doit être définie sur **AzureSqlDW**. | Oui
**connectionString** | Spécifier les informations requises pour la connexion à l’instance Azure SQL Data Warehouse pour la propriété connectionString. | Oui

Remarque : vous devez configurer le [pare-feu Azure SQL Database](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Vous devez configurer le serveur de base de données pour [autoriser les services Azure à accéder au serveur](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). En outre, si vous copiez des données vers Azure SQL Data Warehouse à partir d'un emplacement situé en dehors d'Azure, y compris à partir de sources de données sur site avec la passerelle Data Factory, vous devez configurer la plage d'adresses IP appropriée pour l'ordinateur qui envoie des données à Azure SQL Data Warehouse.

## Propriétés de type du jeu de données Azure SQL Data Warehouse

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l'article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section **typeProperties** du jeu de données de type **AzureSqlDWTable** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| tableName | Nom de la table dans la base de données Azure SQL Data Warehouse à laquelle le service lié fait référence. | Oui |

## Propriétés de type d’activité de copie Azure SQL Data Warehouse

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

**Remarque :** l’activité de copie accepte uniquement une entrée et produit une seule sortie.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

### SqlDWSource

Dans le cas d’une activité de copie, quand la source est de type **SqlDWSource**, les propriétés suivantes sont disponibles dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable. | Non |
| sqlReaderStoredProcedureName | Nom de la procédure stockée qui lit les données de la table source. | Nom de la procédure stockée. | Non |
| storedProcedureParameters | Paramètres de la procédure stockée. | Paires nom/valeur. Les noms et la casse des paramètres doivent correspondre aux noms et à la casse des paramètres de la procédure stockée. | Non |

Si **sqlReaderQuery** est spécifié pour SqlDWSource, l'activité de copie exécute cette requête en fonction de la source Azure SQL Data Warehouse pour obtenir les données.

Vous pouvez également spécifier une procédure stockée en indiquant **sqlReaderStoredProcedureName** et **storedProcedureParameters** (si la procédure stockée accepte des paramètres).

Si vous ne spécifiez pas sqlReaderQuery ou sqlReaderStoredProcedureName, les colonnes définies dans la section structure du code JSON du jeu de données sont utilisées pour créer une requête (select column1, column2 from mytable) à exécuter sur Azure SQL Data Warehouse. Si la définition du jeu de données ne possède pas de structure, toutes les colonnes de la table sont sélectionnées.

#### Exemple SqlDWSource

    "source": {
        "type": "SqlDWSource",
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
 

### SqlDWSink
**SqlDWSink** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Insère des données dans la table SQL lorsque la taille du tampon atteint writeBatchSize | Nombre entier. (unité = nombre de lignes) | Non (Valeur par défaut = 10000) |
| writeBatchTimeout | Temps d’attente pour que l’opération d’insertion de lot soit terminée avant d’expirer. | (Unité = intervalle de temps) Exemple : « 00:30:00 » (30 minutes). | Non | 
| sqlWriterCleanupScript | Requête spécifiée par l'utilisateur pour exécuter l'activité de copie de sorte que les données d'un segment spécifique seront nettoyées. Consultez la section de répétition ci-dessous pour plus de détails. | Une instruction de requête. | Non |
| allowPolyBase | Indique s’il faut utiliser PolyBase (le cas échéant) au lieu du mécanisme BULKINSERT pour charger des données dans Azure SQL Data Warehouse. <br/><br/>Notez que seul le jeu de données **objet blob Azure** dont le **format** est défini sur **TextFormat** comme jeu de données source est pris en charge pour le moment. La prise en charge des autres types de source sera bientôt disponible. <br/><br/>Reportez-vous à la section [Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) pour connaître les contraintes et les détails. | True <br/>False (par défaut) | Non |  
| polyBaseSettings | Groupe de propriétés pouvant être spécifié lorsque la propriété **allowPolybase** est définie sur **true**. | &nbsp; | Non |  
| rejectValue | Spécifie le nombre ou le pourcentage de lignes pouvant être rejetées avant l’échec de la requête. <br/><br/>Pour en savoir plus sur les options de rejet de PolyBase dans la section **Arguments** de la rubrique [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) (Créer une table externe (Transact-SQL)). | 0 (par défaut), 1, 2, … | Non |  
| rejectType | Spécifie si l’option rejectValue est spécifiée comme une valeur littérale ou un pourcentage. | Value (par défaut), Percentage | Non |   
| rejectSampleValue | Détermine le nombre de lignes à extraire avant que PolyBase recalcule le pourcentage de lignes rejetées. | 1, 2, … | Oui, si **rejectType** vaut **percentage** |  
| useTypeDefault | Spécifie comment gérer les valeurs manquantes dans les fichiers texte délimités lorsque PolyBase extrait des données à partir du fichier texte.<br/><br/>Pour plus d’informations sur cette propriété, consultez la section Arguments dans [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) (Créer un format de fichier externe (Transact-SQL)). | True, False (par défaut) | Non | 


#### Exemple SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Utiliser PolyBase pour charger des données dans Azure SQL Data Warehouse.
**PolyBase** est une solution efficace de chargement de grandes quantités de données à partir du stockage d’objets blob Azure vers Azure SQL Data Warehouse avec un débit élevé. Vous pouvez profiter d’un gain important de débit en utilisant PolyBase au lieu du mécanisme BULKINSERT par défaut.

Si votre banque de données source n’est pas le stockage d’objets blob Azure, vous pouvez dans un premier temps copier les données à partir de la banque de données source vers le stockage d’objets Blob Azure en tant que banque intermédiaire, puis utiliser PolyBase pour charger les données dans Azure SQL Data Warehouse à partir de la banque intermédiaire. Dans ce scénario, vous allez utiliser deux activités de copie : la première activité de copie sera configurée pour copier des données à partir de la banque de données source pour le stockage d’objets Blob Azure et la deuxième activité de copie sera de copier des données à partir du stockage d’objets Blob Azure vers Azure SQL Data Warehouse à l’aide de PolyBase.

Définissez la propriété **allowPolyBase** sur **true** comme indiqué dans l’exemple suivant pour Azure Data Factory où PolyBase est utilisé pour copier les données à partir du stockage d’objets Blob Azure vers Azure SQL Data Warehouse. Lorsque vous définissez allowPolyBase sur true, vous pouvez spécifier des propriétés PolyBase spécifiques à l’aide du groupe de propriétés **polyBaseSettings**. Reportez-vous à la section [SqlDWSink](#SqlDWSink) ci-dessus pour plus d’informations sur les propriétés que vous pouvez utiliser avec polyBaseSettings.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

Azure Data Factory vérifie que les données satisfont les exigences suivantes avant d’utiliser PolyBase pour copier des données vers Azure SQL Data Warehouse. Si les exigences ne sont pas satisfaites, le mécanisme BULKINSERT est automatiquement rétabli pour le déplacement des données.

1.	Le **service lié source** est de type : **Azure Storage** et il n’est pas configuré pour utiliser l’authentification SAP (signature d’accès partagé). Pour plus d’informations, consultez [Service lié Azure Storage](data-factory-azure-blob-connector.md#azure-storage-linked-service).  
2. Le **jeu de données d’entrée** est de type : **objet blob Azure** et les propriétés du type de jeu de données répondent aux critères suivants : 
	1. **Type** doit être **TextFormat**. 
	2. **rowDelimiter** doit être **\\n**. 
	3. **nullValue** est défini sur **une chaîne vide** (""). 
	4. **encodingName** est défini sur **utf-8**, qui est la valeur **par défaut**. Ne la définissez pas sur une autre valeur. 
	5. **escapeChar** et **quoteChar** ne sont pas spécifiés. 
	6. **Compression** ne vaut pas **BZIP2**.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	Il n’y a aucun paramètre **skipHeaderLineCount** sous **BlobSource** pour l’activité de copie dans le pipeline. 
4.	Il n’y a aucun paramètre **sliceIdentifierColumnName** sous **SqlDWSink** pour l’activité de copie dans le pipeline. (PolyBase garantit que toutes les données sont mises à jour ou que rien n’est mis à jour en une seule exécution. Pour obtenir la **répétabilité**, vous pouvez utiliser **sqlWriterCleanupScript**.
5.	Il n’y a pas de **columnMapping** utilisé dans l’activité de copie associée. 

### Meilleures pratiques lors de l’utilisation de PolyBase

#### Limite de taille de ligne
Polybase ne prend pas en charge les lignes d’une taille supérieure à 32 Ko. Toute tentative de chargement d’une table avec des lignes supérieures à 32 Ko génère l’erreur suivante :

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Si les données source dont vous disposez ont des lignes d’une taille supérieure à 32 Ko, vous pouvez fractionner verticalement les tables source en plusieurs tables plus petites dans lesquelles la taille de ligne maximale ne dépasse pas la limite. Vous pouvez ensuite charger les tables plus petites à l’aide de PolyBase et les fusionner dans Azure SQL Data Warehouse.

#### tableName dans Azure SQL Data Warehouse
Le tableau suivant fournit des exemples sur la façon de spécifier la propriété **tableName** dans le jeu de données JSON pour différentes combinaisons de schémas et noms de table.

| Schéma BD | Nom de la table | Propriété JSON tableName |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable ou [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] ou [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Si vous voyez une erreur comme celle indiquée ci-dessous, il peut s’agir d’un problème avec la valeur spécifiée pour la propriété tableName. Consultez le tableau ci-dessus pour savoir comment spécifier des valeurs pour la propriété JSON tableName.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Colonnes avec des valeurs par défaut
Actuellement, la fonctionnalité PolyBase dans Data Factory accepte seulement le même nombre de colonnes que la table cible. Par exemple, si vous avez une table comportant 4 colonnes et que l’une d’entre elles est définie avec une valeur par défaut, les données d’entrée doivent toujours contenir 4 colonnes. La fourniture d’un jeu de données d’entrée de 3 colonnes produirait une erreur telle que celle indiquée ci-dessous :

	All columns of the table must be specified in the INSERT BULK statement.

La valeur NULL est une forme spéciale de valeur par défaut. Si la colonne accepte la valeur Null, les données d’entrée (dans l’objet blob) de cette colonne peuvent être vides (ne peuvent pas être absentes du jeu de données d’entrée). PolyBase insère NULL pour ces données dans Azure SQL Data Warehouse.

#### Tirer parti de la copie en deux étapes pour utiliser PolyBase
PolyBase comporte des limitations concernant les banques de données et les formats qu’il peut utiliser. Si votre scénario ne remplit pas les conditions requises, vous pouvez utiliser l’activité de copie pour copier des données vers une banque de données prise en charge par PolyBase et/ou convertir les données dans un format pris en charge par PolyBase. Voici des exemples de transformations réalisables :

-	Convertir des fichiers source dans d’autres encodages en objets blob Azure au format UTF-8
-	Sérialisez les données dans la base de données SQL Server/SQL Azure en objets blob Azure BLOB format CSV.
-	Modifiez l’ordre des colonnes en spécifiant la propriété columnMapping.

Voici quelques conseils à suivre lorsque vous effectuez des transformations :

- Sélection d’un délimiteur approprié lors de la conversion de données tabulaires en fichiers CSV.

	Il est recommandé d’utiliser des caractères qui ont très peu de chances d’apparaître dans les données comme délimiteur de colonne. Les délimiteurs communs incluent la virgule (,), le tilde (~), la barre verticale (|) et la tabulation (\\t). Si vos données les contiennent, vous pouvez définir le délimiteur de colonne sur des caractères non imprimables tels que « \\u0001 ». Polybase accepte les délimiteurs de colonne à plusieurs caractères qui vous permettent de construire des délimiteurs de colonne plus complexes.	
- Format des objets datetime

	Lorsque les objets datetime sont sérialisés, l’activité de copie, par défaut, utilise le format « yyyy-MM-dd HH:mm:ss.fffffff » qui, par défaut, n’est pas pris en charge par PolyBase. Les formats datetime pris en charge sont indiqués dans [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) (Créer un format de fichier externe (Transact-SQL)). Le non respect des conditions de format datetime génère une erreur comme indiqué ci-dessous :

		Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 1 rows processed.
		(/AccountDimension)Column ordinal: 97, Expected data type: DATETIME NOT NULL, Offending value: 2010-12-17 00:00:00.0000000  (Column Conversion Error), Error: Conversion failed when converting the NVARCHAR value '2010-12-17 00:00:00.0000000' to data type DATETIME.

	Pour résoudre cette erreur, spécifiez le format datetime comme indiqué dans l’exemple suivant :
	
		"structure": [
    		{ "name" : "column", "type" : "int", "format": "yyyy-MM-dd HH:mm:ss" }
		]


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mappage de type pour Azure SQL Data Warehouse

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion à partir de types de source natifs en types .NET
2. Conversion à partir du type .NET en type de récepteur natif

Lors du déplacement de données vers et à partir de SQL Azure, SQL Server, Sybase, les mappages suivants seront utilisés à partir du type SQL en type .NET et vice versa.

Le mappage est identique au [mappage du type de données SQL Server pour ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx) (article en anglais).

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