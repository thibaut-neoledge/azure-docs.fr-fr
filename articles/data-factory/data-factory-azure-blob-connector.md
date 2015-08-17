<properties 
	pageTitle="Connecteur d'objet Blob Azure - Déplacer des données vers et à partir d'un objet Blob Azure" 
	description="En savoir plus sur le connecteur d'objets Blob Azure pour le service Data Factory qui vous permet de déplacer des données vers/depuis le stockage des objets blobs Azure" 
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
	ms.date="07/29/2015" 
	ms.author="spelluru"/>


# Connecteur d'objet Blob Azure - Déplacer des données vers et à partir d'un objet Blob Azure
Cet article décrit comment vous pouvez utiliser l'activité de copie dans Azure Data Factory pour déplacer des données vers un objet Blob Azure à partir d'un magasin de données et vice versa. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasin de données prises en charge.

## Exemple : copie de données à partir d'un objet Blob Azure vers SQL Azure
L'exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type [AzureSqlDatabase](data-factory-azure-sql-connector.md).
2.	Un service lié de type [AzureStorage](#LinkedService).
3.	Un jeu de données d'entrée de type [AzureBlob](#Dataset).
4.	Un jeu de données de sortie de type [AzureSqlTable](data-factory-azure-sql-connector.md).
4.	Un pipeline avec une activité de copie qui utilise [BlobSource](#CopyActivity) et [SqlSink](data-factory-azure-sql-connector.md).

L'exemple copie des données appartenant à une série horaire à partir d'un objet Blob Azure vers une table dans une base de données SQL Azure, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié SQL Azure :**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

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

**Jeu de données d'entrée d'objet Blob Azure :**

Les données sont récupérées à partir d'un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le nom du chemin d'accès et du fichier de dossier pour l'objet Blob sont évalués dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois et le jour de l'heure de début et le nom de fichier utilise la partie heure de l'heure de début. Le paramètre « external » : « true » informe le service Data Factory que cette table est externe à la Data Factory et non produite par une activité dans la Data Factory.

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

**Jeu de données de sortie SQL Azure :**

L'exemple copie les données dans une table nommée « MyTable » dans une base de données SQL Azure. Vous devez créer la table dans votre base de données SQL Azure avec le même nombre de colonnes que le fichier CSV d'objet Blob doit contenir. De nouvelles lignes sont ajoutées à la table toutes les heures.

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

**Pipeline avec une activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **BlobSource** et le type **sink** est défini sur **SqlSink**.

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

## Exemple : copie de données à partir de SQL Azure vers un objet Blob Azure
L'exemple ci-dessous présente les éléments suivants :

1.	Un service lié de type AzureSqlDatabase.
2.	Un service lié de type AzureStorage.
3.	Un jeu de données d'entrée de type AzureSqlTable.
4.	Un jeu de données de sortie de type AzureBlob.
4.	Un pipeline avec une activité de copie qui utilise SqlSource et BlobSink.

L'exemple copie des données appartenant à une série horaire à partir d'une table dans une base de données SQL Azure vers un objet Blob, toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié SQL Azure :**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

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

**Jeu de données d'entrée SQL Azure :**

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

**Jeu de données de sortie d'objet Blob Azure :**

Les données sont écrites dans un nouvel objet Blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d'accès du dossier pour l'objet Blob est évalué dynamiquement en fonction de l'heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.
	
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

**Pipeline avec l'activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **SqlSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.


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

## <a name="LinkedService"></a> Propriétés du service lié Azure Storage

Vous pouvez lier un compte de stockage Azure à une Azure Data Factory à l'aide d'un service lié Azure Storage. Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Azure Storage.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété de type doit être définie sur : **AzureStorage** | Oui |
| connectionString | Spécifier les informations requises pour la connexion au stockage Azure pour la propriété connectionString. Vous pouvez obtenir la valeur connectionString pour le stockage Azure à partir du portail Azure. | Oui |

## <a name="Dataset"></a> Propriétés de type du jeu de données d'objet Blob Azure

Pour obtenir une liste complète des sections et propriétés JSON disponibles pour la définition de jeux de données, consultez l'article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement, le format, etc. des données dans le magasin de données. La section typeProperties pour le jeu de données de type **AzureBlob** a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| folderPath | Chemin d'accès au conteneur et au dossier dans le stockage des objets Blobs. Exemple : monconteneurblob\\mondossierblob\\ | Oui |
| filename | <p>Nom de l'objet blob. fileName est facultatif. </p><p>Si vous spécifiez un nom de fichier, l'activité (y compris la copie) fonctionne sur l'objet Blob spécifique.</p><p>Lorsque fileName n'est pas spécifié, la copie inclut tous les objets Blob dans folderPath pour le jeu de données d'entrée.</p><p>Lorsque fileName n'est pas spécifié pour un jeu de données de sortie, le nom du fichier généré est au format suivant : Data.<Guid>. txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)</p> | Non |
| partitionedBy | partitionedBy est une propriété facultative. Vous pouvez l'utiliser pour spécifier un folderPath dynamique et le nom de fichier pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. Consultez la section Utilisation de la propriété partitionedBy ci-dessous pour obtenir plus d'informations et des exemples. | Non
| format | Deux types de formats sont pris en charge : **TextFormat**, **AvroFormat**. Vous devez définir la propriété de type sous format sur l'une de ces valeurs. Lorsque le format est TextFormat, vous pouvez spécifier des propriétés facultatives supplémentaires pour le format. Consultez la section [Définition de TextFormat](#specifying-textformat) ci-dessous pour plus de détails. | Non

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

### Définition de TextFormat

Si le format est défini sur **TextFormat**, vous pouvez spécifier les propriétés **facultatives** suivantes dans la section **Format**.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| columnDelimiter | Caractère(s) utilisé(s) comme séparateur de colonnes dans un fichier. Cette balise est facultative. La valeur par défaut est la virgule (,). | Non |
| rowDelimiter | Caractère(s) utilisé(s) comme séparateur de lignes dans un fichier. Cette balise est facultative. La valeur par défaut est : [« \\r\\n », « \\r », « \\n »\]. | Non |
| escapeChar | <p>Caractère spécial utilisé pour échapper au délimiteur de colonnes indiqué dans le contenu. Cette balise est facultative. Aucune valeur par défaut. Vous ne devez pas spécifier plusieurs caractères pour cette propriété.</p><p>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes mais que vous voulez avoir le caractère virgule dans le texte (exemple : « Hello, world »), vous pouvez définir « $ » comme caractère d'échappement et utiliser la chaîne « $Hello, world » dans la source.</p><p>Notez que vous ne pouvez pas spécifier escapeChar et quoteChar pour une table.</p> | Non | 
| quoteChar | <p>Caractère spécial utilisé pour entourer de guillemets la valeur de la chaîne. Les séparateurs de colonnes et de lignes à l'intérieur des caractères de guillemets sont considérés comme faisant partie de la valeur de la chaîne. Cette balise est facultative. Aucune valeur par défaut. Vous ne devez pas spécifier plusieurs caractères pour cette propriété.</p><p>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes mais que vous voulez avoir le caractère virgule dans le texte (exemple : <Hello  world>), vous pouvez définir « " » comme caractère de guillemet et utiliser la chaîne <"Hello, world"> dans la source. Cette propriété s'applique aux tables d'entrée et de sortie.</p><p>Notez que vous ne pouvez pas spécifier escapeChar et quoteChar pour une table.</p> | Non |
| nullValue | <p>Caractère(s) utilisé(s) pour représenter la valeur null dans le contenu du fichier blob. Cette balise est facultative. La valeur par défaut est « \\N ».</p><p>Par exemple, selon l'exemple ci-dessus, « NaN » dans l'objet blob sera converti en tant que valeur null lors de la copie vers, par exemple, SQL Server.</p> | Non |
| encodingName | Spécifier le nom d'encodage. Pour obtenir la liste des noms d'encodage valides, consultez : [Propriété Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Par exemple : windows-1250 ou shift\_jis. La valeur par défaut est : UTF-8. | Non | 

#### Exemples
L'exemple suivant illustre certaines des propriétés de format pour TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Pour utiliser escapeChar à la place de quoteChar, remplacez la ligne contenant quoteChar par ce qui suit :

	"escapeChar": "$",

### Définition d'AvroFormat
Si le format est défini sur AvroFormat, il est inutile de spécifier des propriétés dans la section Format de la section typeProperties. Exemple :

	"format":
	{
	    "type": "AvroFormat",
	}

Pour utiliser AvroFormat dans une table Hive, vous pouvez faire référence au [didacticiel Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

## <a name="CopyActivity"></a> Propriétés de type de l'activité de copie d'objet Blob Azure  
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

**BlobSource** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Spécifie s'il faut traiter une chaîne null ou vide en tant que valeur null. | TRUE<br/>
FALSE | Non |
| skipHeaderLineCount | Indique le nombre de lignes à ignorer. Applicable uniquement lorsque le jeu de données d'entrée utilise **TextFormat**. | Entier compris entre 0 et Max. | Non | 


**BlobSink** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Spécifie s'il faut ajouter un en-tête de définitions de colonne. | TRUE<br/>
FALSE (valeur par défaut) | Non |

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=August15_HO6-->