<properties 
	pageTitle="Exemples d'utilisation des activités de copie dans Azure Data Factory" 
	description="Fournit des exemples pour utilisant une activité de copie dans une fabrique de données Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Exemples d'utilisation des activités de copie dans Azure Data Factory
Vous pouvez utiliser l'**activité de copie** dans un pipeline pour copier les données d'une source vers un récepteur (destination) au sein d'un lot. Cette rubrique fournit quelques exemples d'utilisation de l'activité de copie dans un pipeline de fabrique de données. Pour une présentation détaillée de l'activité de copie et des principaux scénarios pris en charge, consultez la rubrique [Copier des données avec Azure Data Factory][adf-copyactivity].

## Copier des données à partir d'une base de données SQL Server sur site vers un objet blob Azure
Dans cet exemple, une table d'entrée et une table de sortie sont définies puis utilisées dans une activité de copie au sein d'un pipeline copiant des données à partir d'une base de données SQL Server locale vers un objet blob Azure.

### Hypothèses
Cet exemple suppose que vous avez déjà les artefacts suivants de la fabrique de données Azure :

* Groupe de ressources nommé **ADF**.
* Fabrique de données Azure nommée **CopyFactory**.
* Une passerelle de gestion de données nommée **mygateway** est créé et est en ligne.  

### Créer un service lié pour la base de données de SQL Server source local
Dans cette étape, vous créez un service lié nommé **MyOnPremisesSQLDB** qui pointe vers une base de données SQL Server sur site.

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

- **type** a **OnPremisesSqlLinkedService**.
- **connectionString** est défini sur la chaîne de connexion pour une base de données SQL Server. 
- **gatewayName** est définie sur le nom de la passerelle de gestion des données vous avez installé sur votre ordinateur local et inscrit dans le portail de service Factory de données Azure. 

Voir [local SQL Service lié](https://msdn.microsoft.com/library/dn893523.aspx) pour plus d'informations sur JSON éléments permettant de définir une SQL sur site lié service.
 
### Création d'un service lié à la destination blob Azure
Dans cette étape, vous créez un service lié nommé **MyAzureStorage** qui pointe un stockage d'objets blob Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Notez les points suivants :

- **type** a **AzureStorageLinkedService**.
- **connectionString** - spécifier le nom du compte et le compte de clé pour le stockage Azure.

Voir [Service lié de stockage Azure](https://msdn.microsoft.com/library/dn893522.aspx) pour plus d'informations sur les éléments JSON pour définir un stockage Azure lié au service.

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

- **type** a **OnPremisesSqlServerTableLocation**.
- **tableName** est définie sur **MyTable**, qui contient la source de données. 
- **linkedServiceName** a **MyOnPremisesSQLDB**, le liés de service que vous avez créé pour la base de données SQL sur site.

Voir [Propriétés de l'emplacement local SQL](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à une table SQL Server.

### Table de sortie JSON
Le script JSON suivant définit une table de sortie : **MyAzureBlob**, qui fait référence à un objet blob Azure : **MyBlob** dans le dossier d'objet blob : **MySubFolder** dans le conteneur d'objets blob : **MyContainer**.
         
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
 
- **type** a **AzureBlobLocation**.
- **folderPath** est définie sur **MyContainer/MySubFolder**, qui contient l'objet blob qui conserve les données copiées. 
- **nom de fichier** est définie sur **MyBlob**, l'objet blob qui contiendra les données de sortie.
- **linkedServiceName** a **MyAzureStorge**, le liés de service que vous avez créé pour le stockage Azure.    

Voir [Propriétés de l'objet blob Azure emplacement](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à un objet blob Azure.

### Pipeline (avec activité de copie) JSON
Dans cet exemple, le pipeline **CopyActivityPipeline** est défini à l'aide des propriétés suivantes :

- La propriété **type** est définie sur **CopyActivity**.
- **MyOnPremTable** est spécifié en tant que balise d'entrée (**inputs**).
- **MyAzureBlob** est spécifié en tant que balise de sortie (**outputs**) 
- La section **Transformation** contient deux sections secondaires : **source** et **récepteur**. Le type de la source est défini sur **SqlSource**, tandis que celui du récepteur est défini sur **BlobSink**. **sqlReaderQuery** définit la transformation (projection) qui doit être effectuée sur la source. Pour plus d'informations sur toutes les propriétés, consultez [Référence de script JSON][json-script-reference].

         
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

Voir [référence JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) pour plus d'informations sur les éléments JSON pour définir un pipeline de données fabrique et [prise en charge des Sources et récepteurs](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés de SqlSource (par exemple : **sqlReaderQuery **dans l'exemple) et BlobSink. 


## Copier des données d'un système de fichiers local à un objet blob Azure
Vous pouvez utiliser l'activité de copie pour copier des fichiers à partir d'un système de fichiers local (partages de réseau Windows/Linux ou hôte local Windows) sur un objet Blob Azure. L'hôte peut être Windows ou Linux avec Samba configuré. Passerelle de gestion des données doit être installé sur un ordinateur Windows qui peut se connecter à l'hôte.

### Hypothèses
Cet exemple suppose que les éléments suivants :

- **Hôte** -nom du serveur qui héberge le système de fichiers est : **\\contoso**.
- **Dossier** -nom du dossier qui contient les fichiers d'entrée est : **marketingcampaign\\regionaldata\\ {section}, où les fichiers sont partitionnés dans un dossier nommé {section}, telles que 2014121112 (année 2014, 12 mois, jour de 11, les heures de 12). 
### Créer un service local système lié
L'exemple suivant JSON peut être utilisé pour créer un service lié nommé **FolderDataStore** de type **OnPremisesFileSystemLinkedService**.

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

> [AZURE.NOTE]N'oubliez pas d'utiliser le caractère d'échappement '' pour les noms de l'hôte et les dossiers de fichiers JSON. Pour **\\Contoso**, utilisez **\\\\Contoso**.

Voir [Service lié du système de fichiers local](https://msdn.microsoft.com/library/dn930836.aspx) pour plus d'informations sur les éléments JSON pour définir un système de fichiers local lié au service.

### Création d'un service lié à la destination blob Azure
L'exemple suivant JSON peut être utilisé pour créer un service lié nommé **MyAzureStorage** de type **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Voir [Service lié de stockage Azure](https://msdn.microsoft.com/library/dn893522.aspx) pour plus d'informations sur les éléments JSON pour définir un stockage Azure lié au service.

### Créer la table d'entrée
Le script JSON suivant définit une table d'entrée qui fait référence à un service du système lié fichier local créé précédemment.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\\regionaldata\\{Slice}",
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

Voir [Propriétés d'emplacement de système de fichiers local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à un système de fichiers local.

### Créer la sortie en mesure
Le script JSON suivant définit une table de sortie : **AzureBlobDest**, qui fait référence à un objet blob Azure : **MyBlob** dans le dossier d'objet blob : **MySubFolder** dans le conteneur d'objets blob : **MyContainer**.
         
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

Voir [Propriétés de l'objet blob Azure emplacement](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à un objet blob Azure.

### Créer le pipeline
Le pipeline suivant JSON définit un pipeline avec une activité de copie qui copie les données du système de fichiers local vers la destination blob Azure.
 
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

Le pipeline dans cet exemple copie le contenu sous forme binaire, sans une analyse ou d'effectuer de transformations. Notez que vous pouvez exploiter **concurrence** pour copier des sections des fichiers en parallèle. Cela est utile lorsque vous souhaitez déplacer que les tranches déjà eu lieu dans le passé.

> [AZURE.NOTE]Activités de copie simultanées avec le même hôte via le chemin d'accès UNC avec différents comptes d'utilisateurs peuvent entraîner des erreurs telles que « plusieurs connexions à un serveur ou d'une ressource partagée par le même utilisateur, à l'aide de plusieurs noms d'utilisateur, ne sont pas autorisées ». Il s'agit de la restriction du système d'exploitation pour des raisons de sécurité. Soit planifier les activités de copie avec différentes passerelles, ou d'installer la passerelle au sein de l'hôte et utilisez « localhost » ou « local » au lieu du chemin d'accès UNC.

Voir [référence JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) pour plus d'informations sur les éléments JSON pour définir un pipeline de données fabrique et [prise en charge des Sources et récepteurs](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés de FileSystemSource et BlobSink.

### Scénarios supplémentaires pour l'utilisation de tables de système de fichiers

#### Copiez tous les fichiers dans un dossier spécifique
Notez que seul **folderPath** est spécifié dans l'exemple JSON.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\\regionaldata\\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Copiez tous les fichiers CSV sous le dossier spécifique
Notez que le **FiltreFichier** a ***.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\\regionaldata\\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Copier un fichier spécifique
Notez que la **fileFiter** est défini dans un fichier spécifique : **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\\regionaldata\\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Copier des données à partir d'une base de données Oracle sur site vers un objet blob Azure
Vous pouvez utiliser l'activité de copie pour copier les fichiers de base de données Oracle sur site local vers un objet Blob Azure.

### créer un service lié pour une base de données Oracle sur site
Le JSON suivant peut être utilisé pour créer un service lié qui pointe vers une base de données Oracle sur site. Notez que le **type** a **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Voir [local Oracle lié Service](https://msdn.microsoft.com/library/dn948537.aspx) pour plus d'informations sur les éléments JSON pour définir Oracle local lié au service.

### Création d'un service lié à la destination blob Azure
L'exemple suivant JSON peut être utilisé pour créer un service lié nommé **MyAzureStorage** de type **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Voir [Service lié de stockage Azure](https://msdn.microsoft.com/library/dn893522.aspx) pour plus d'informations sur les éléments JSON pour définir un stockage Azure lié au service.

### Créer la table d'entrée
L'exemple suivant JSON peut être utilisé pour créer une table de la fabrique de données Azure qui fait référence à une table dans une base de données Oracle sur site. Notez que le **type d'emplacement** a **OnPremisesOracleTableLocation**.

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

Voir [Propriétés de l'emplacement local Oracle](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à une table dans une base de données Oracle sur site.

### Créer la table de sortie
Le script JSON suivant définit une table de sortie : **MyAzureBlob**, qui fait référence à un objet blob Azure : **MyBlob** dans le dossier d'objet blob : **MySubFolder** dans le conteneur d'objets blob : **MyContainer**.
         
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

Voir [Propriétés de l'objet blob Azure emplacement](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) pour plus d'informations sur les éléments JSON pour définir une table de la fabrique de données qui fait référence à un objet blob Azure.

### Créer le pipeline
L'exemple suivant de pipeline a une activité de copie qui copie les données à partir d'une table de base de données Oracle sur un objet blob de stockage Azure.

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

Voir [référence JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) pour plus d'informations sur les éléments JSON pour définir un pipeline de données fabrique et [prise en charge des Sources et récepteurs](https://msdn.microsoft.com/library/dn894007.aspx) pour les propriétés de OracleSource et BlobSink.

## Voir aussi

- [Copie de données avec Azure Data Factory][adf-copyactivity]
- [Activité de copie : informations de référence sur la création de scripts JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vidéo : Présentation de l'activité de copie dans Azure Data Factory][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir--> 