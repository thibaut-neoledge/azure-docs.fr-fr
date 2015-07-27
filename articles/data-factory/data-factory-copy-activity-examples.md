<properties 
	pageTitle="Exemples d’utilisation de l’activité de copie dans Azure Data Factory" 
	description="Fournit des exemples pour l’utilisation d’une activité de copie dans Azure Data Factory." 
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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Exemples d'utilisation de l’activité de copie dans Azure Data Factory
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier les données d'une source vers un récepteur (destination) au sein d'un lot. Cette rubrique fournit quelques exemples d'utilisation de l'activité de copie dans un pipeline Data Factory. Pour une présentation détaillée de l'activité de copie et des principaux scénarios pris en charge, consultez la rubrique [Copier des données avec Azure Data Factory][adf-copyactivity].

## Copie de données depuis une base de données SQL Server locale vers un objet blob Azure
Dans cet exemple, une table d'entrée et une table de sortie sont définies puis utilisées dans une activité de copie au sein d'un pipeline copiant des données à partir d'une base de données SQL Server locale vers un objet blob Azure.

### Hypothèses
Cet exemple suppose que vous disposez déjà des artefacts Azure Data Factory suivants :

* Groupe de ressources nommé **ADF**.
* Fabrique de données Azure nommée **CopyFactory**.
* Une passerelle de gestion des données nommée **mygateway** créée et mise en ligne.  

### Création d’un service lié pour la base de données de SQL Server locale source
Au cours de cette étape, vous créez un service lié nommé **MyOnPremisesSQLDB** qui pointe vers une base de données SQL Server locale.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Notez les points suivants :

- **type** est défini sur **OnPremisesSqlLinkedService**.
- **connectionString** est défini sur la chaîne de connexion pour une base de données SQL Server. 
- **gatewayName** est défini sur le nom de la passerelle de gestion des données vous avez installée sur votre ordinateur local et que vous avez inscrite dans le portail de service Azure Data Factory. 

Consultez [Service lié SQL local](https://msdn.microsoft.com/library/dn893523.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié SQL local.
 
### Création d'un service lié pour un objet blob de destination Azure
Au cours de cette étape, vous créez un service lié nommé **MyAzureStorage** qui pointe vers un stockage d'objets blob Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Notez les points suivants :

- **type** est défini sur **AzureStorageLinkedService**.
- **connectionString** : indique le nom du compte et la clé du compte pour le stockage Azure.

Consultez [Service lié Azure Storage](https://msdn.microsoft.com/library/dn893522.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié Azure Storage.

### Table d'entrée JSON
Le script JSON suivant définit une table d'entrée qui fait référence à la table SQL **MyTable**, qui se trouve dans une base de données SQL Server locale définie par le service lié **MyOnPremisesSQLDB**. Notez que **name** correspond au nom de la table Azure Data Factory et **tableName** au nom de la table SQL dans une base de données SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Notez les points suivants :

- **type** est défini sur **OnPremisesSqlServerTableLocation**.
- **tableName** est défini sur **MyTable**, qui contient les données sources. 
- **linkedServiceName** est défini sur **MyOnPremisesSQLDB**, le service lié que vous avez créé pour la base de données SQL locale.

Consultez [Propriétés de l'emplacement du SQL local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à une table SQL Server.

### Table de sortie JSON
Le script JSON suivant définit une table de sortie : **MyAzureBlob**, qui fait référence à un objet blob Azure : **MyBlob** situé dans le dossier d’objets blob : **MySubFolder**, lui-même situé dans le conteneur d’objets blob : **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Notez les points suivants :
 
- **type** est défini sur **AzureBlobLocation**.
- **folderPath** est défini sur **MyContainer/MySubFolder**, qui contient l'objet blob contenant les données copiées. 
- **fileName** est défini sur **MyBlob**, l'objet blob qui contient les données de sortie.
- **linkedServiceName** est défini sur **MyAzureStorge**, le service lié que vous avez créé pour le stockage Azure.    

Consultez la rubrique [Propriétés de l’emplacement des objets blob Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à un objet blob Azure.

### Pipeline (avec activité de copie) JSON
Dans cet exemple, le pipeline **CopyActivityPipeline** est défini à l'aide des propriétés suivantes :

- La propriété **type** est définie sur **CopyActivity**.
- **MyOnPremTable** est spécifié en tant que balise d'entrée (**inputs**).
- **MyAzureBlob** est spécifié en tant que balise de sortie (**outputs**)
- La section **Transformation** contient deux sections secondaires : **source** et **récepteur**. Le type de la source est défini sur **SqlSource**, tandis que celui du récepteur est défini sur **BlobSink**. **sqlReaderQuery** définit la transformation (projection) qui doit être effectuée sur la source. Pour plus d'informations sur toutes les propriétés, consultez [Référence de script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Consultez la rubrique [Référence du pipeline JSON](https://msdn.microsoft.com/library/dn834988.aspx) pour en savoir plus sur les éléments JSON permettant de définir un pipeline Data Factory et la rubrique [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés de SqlSource (par exemple : **sqlReaderQuery **dans l'exemple) et BlobSink. 


## Copie de données depuis un système de fichiers local vers un objet blob Azure
Vous pouvez utiliser l'activité de copie pour copier des fichiers à partir d'un système de fichiers local (partages de réseau Windows/Linux ou hôte local Windows) sur un objet blob Azure. L'hôte peut être Windows ou Linux avec Samba configuré. La passerelle de gestion des données doit être installée sur un ordinateur Windows qui peut se connecter à l'hôte.

### Hypothèses
Cet exemple part des principes suivants :

- **Hôte** : le nom du serveur qui héberge le système de fichiers est : **\contoso**.
- **Dossier** : le nom du dossier qui contient les fichiers d’entrée est : **marketingcampaign\regionaldata\{tranche}, où les fichiers sont partitionnés dans un dossier nommé {tranche}, comme 2014121112 (année 2014, 12ème mois, 11ème jour, midi). 

### Création d’un service lié du système de fichiers local
L'exemple JSON suivant peut être utilisé pour créer un service lié nommé **FolderDataStore** de type **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\\\\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]N'oubliez pas d'utiliser le caractère d'échappement '' pour les noms de l'hôte et les dossiers dans les fichiers JSON. Pour **\Contoso**, utilisez **\\Contoso**.

Consultez [Service lié du système de fichiers local](https://msdn.microsoft.com/library/dn930836.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié du système de fichiers local.

### Création d’un service lié pour un objet blob de destination Azure
L'exemple JSON suivant peut être utilisé pour créer un service lié nommé **MyAzureStorage** de type **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consultez [Service lié Azure Storage](https://msdn.microsoft.com/library/dn893522.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié Azure Storage.

### Création de la table d'entrée
Le script JSON suivant définit une table d'entrée qui fait référence à un service lié du système de fichiers local créé précédemment.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

Consultez la rubrique [Propriétés de l'emplacement du système de fichiers local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à un système de fichiers local.

### Création de la table de sortie
Le script JSON suivant définit une table de sortie : **AzureBlobDest**, qui fait référence à un objet blob Azure : **MyBlob** situé dans le dossier d'objets blob : **MySubFolder**, lui-même situé dans le conteneur d'objets blob : **MyContainer**.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consultez la rubrique [Propriétés de l’emplacement des objets blob Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à un objet blob Azure.

### Création du pipeline
Le pipeline JSON suivant définit un pipeline avec une activité de copie qui copie les données du système de fichiers local vers l’objet blob de destination Azure.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

Dans cet exemple, le pipeline copie le contenu sous forme binaire, sans effectuer d’analyse ou de transformation. Notez que vous pouvez exploiter la fonction d’**accès concurrentiel** pour copier des tranches de fichiers en parallèle. Cela est utile lorsque vous souhaitez déplacer des tranches qui ont déjà eu lieu auparavant.

> [AZURE.NOTE]Les activités de copie simultanées avec le même hôte, via le chemin d'accès UNC et avec différents comptes d'utilisateurs peuvent entraîner des erreurs telles que « les connexions multiples à un serveur ou à une ressource partagée par le même utilisateur, à l'aide de plusieurs noms d'utilisateur, ne sont pas autorisées ». Il s'agit de la restriction du système d'exploitation pour des raisons de sécurité. Vous pouvez planifier les activités de copie avec différentes passerelles ou installer la passerelle au sein de l'hôte et utiliser « localhost » ou « local » au lieu du chemin d'accès UNC.

Consultez la rubrique [Référence JSON du pipeline](https://msdn.microsoft.com/library/dn834988.aspx) pour en savoir plus sur les éléments JSON permettant de définir un pipeline Data Factory et la rubrique [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés de FileSystemSource et de BlobSink.

### Scénarios supplémentaires pour l'utilisation de tables de système de fichiers

#### Copie de tous les fichiers dans un dossier spécifique
Notez que seul **folderPath** est spécifié dans l'exemple JSON.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Copie de tous les fichiers CSV dans le dossier spécifique
Notez que le **fileFilter** est défini sur ***.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Copie d’un fichier spécifique
Notez que le **fileFiter** est défini dans un fichier spécifique : **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Copie de données depuis une base de données Oracle locale vers un objet blob Azure
Vous pouvez utiliser l'activité de copie pour copier les fichiers d’une base de données Oracle locale vers un objet blob Azure.

### Création d’un service lié pour une base de données Oracle locale
Le JSON suivant peut être utilisé pour créer un service lié qui pointe vers une base de données Oracle locale. Notez que le **type** est défini sur **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Consultez [Service lié Oracle local](https://msdn.microsoft.com/library/dn948537.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié Oracle local.

### Création d’un service lié pour un objet blob de destination Azure
L'exemple JSON suivant peut être utilisé pour créer un service lié nommé **MyAzureStorage** de type **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consultez [Service lié Azure Storage](https://msdn.microsoft.com/library/dn893522.aspx) pour en savoir plus sur les éléments JSON permettant de définir un service lié Azure Storage.

### Création de la table d’entrée
L'exemple JSON suivant peut être utilisé pour créer une table Azure Data Factory faisant référence à une table dans une base de données Oracle locale. Notez que le **type d'emplacement** est défini sur **OnPremisesOracleTableLocation**.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

Consultez la rubrique [Propriétés de l'emplacement de la base de données Oracle locale](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à une table dans une base de données Oracle locale.

### Création de la table de sortie
Le script JSON suivant définit une table de sortie : **MyAzureBlob**, qui fait référence à un objet blob Azure : **MyBlob** situé dans le dossier d’objets blob : **MySubFolder**, lui-même situé dans le conteneur d'objets blob : **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consultez la rubrique [Propriétés de l’emplacement des objets blob Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour en savoir plus sur les éléments JSON permettant de définir une table Data Factory faisant référence à un objet blob Azure.

### Création du pipeline
L'activité de copie de l’exemple de pipeline suivant copie les données à partir d'une table de la base de données Oracle vers un objet blob Azure Storage.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

Consultez la rubrique [Référence JSON du pipeline](https://msdn.microsoft.com/library/dn834988.aspx) pour en savoir plus sur les éléments JSON permettant de définir un pipeline Data Factory et la rubrique [Sources et récepteurs pris en charge](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés d’OracleSource et de BlobSink.

## Voir aussi

- [Copie de données avec Azure Data Factory][adf-copyactivity]
- [Activité de copie : informations de référence sur la création de scripts JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vidéo : Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!----HONumber=July15_HO3-->