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
	ms.date="01/19/2016"
	ms.author="spelluru"/>

# Déplacer des données vers et depuis Azure Data Lake Store à l’aide d’Azure Data Factory
Cet article explique comment vous pouvez utiliser l’activité de copie dans la fabrique de données Azure pour déplacer des données vers Azure Data Lake Store depuis autre un magasin de données et déplacer des données depuis un magasin Azure Data Lake Store vers un autre magasin de données. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasin de données prises en charge.

> [AZURE.NOTE]
Vous devez créer un compte Azure Data Lake Store avant de créer un pipeline avec l’activité de copie pour déplacer des données vers/depuis Azure Data Lake Store. Pour plus d’informations sur Azure Data Lake Store, consultez [Prise en main d’Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
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
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
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

> [AZURE.IMPORTANT] Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Vous devrez **accorder une nouvelle autorisation** à l’aide du bouton **Autoriser** lors de l’**expiration du jeton**, puis redéployer le service lié. Pour plus d’informations, consultez la section [Service lié Azure Data Lake Store](#azure-data-lake-store-linked-service-properties).



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
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Consultez les étapes de l’exemple précédent pour obtenir l’URL d’autorisation.

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
| :-------- | :----------- | :-------- |
| type | La propriété type doit être définie sur : **AzureDataLakeStore** | Oui |
| dataLakeUri | Spécifiez des informations à propos du compte Azure Data Lake Store. Il est au format suivant : https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1 | Oui |
| autorisation | Cliquez sur le bouton **Autoriser** dans **Data Factory Editor** et saisissez vos informations d’identification, ce qui affecte l’URL d’autorisation générée automatiquement à cette propriété. | Oui |
| sessionId | ID de session OAuth issu de la session d’autorisation oauth. Chaque ID de session est unique et ne peut être utilisé qu’une seule fois. Il est généré automatiquement lorsque vous utilisez Data Factory Editor. | Oui |  
| accountName | Nom du compte de lac de données | Non |
| subscriptionId | ID d’abonnement Azure | Non (si non spécifié, l’abonnement de la fabrique de données est utilisé). |
| nom\_groupe\_ressources | Nom du groupe de ressources Azure | Non (si non spécifié, le groupe de ressources de la fabrique de données est utilisé). |

Le code d’autorisation que vous avez généré à l’aide du bouton **Autoriser** expire au bout d’un certain temps. Consultez le tableau suivant pour connaître les délais d’expiration associés aux différents types de comptes d’utilisateur. Vous pouvez rencontrer le message d’erreur suivant lors de l’**expiration du jeton** d’authentification : « Credential operation error: invalid\_grant - AADSTS70002: Error validating credentials. AADSTS70008: The provided access grant is expired or revoked. Trace ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Correlation ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z ».


| Type d’utilisateur | Expire après |
| :-------- | :----------- | 
| Utilisateur non AAD (@hotmail.com, @live.com, etc.). | 12 heures |
| L’utilisateur AAD et la source OAuth se trouvent dans un autre [client](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) que le client de la fabrique de données. | 12 heures |
| L’utilisateur AAD et la source OAuth se trouvent dans le même client que le client de la fabrique de données. | 14 jours |

Pour éviter ou résoudre cette erreur, vous devrez accorder une nouvelle autorisation à l’aide du bouton **Autoriser** lors de l’**expiration du jeton**, puis redéployer le service lié. Vous pouvez également générer des valeurs pour les propriétés **sessionId** et **authorization** à l’aide du code fourni dans la section suivante.

### Pour générer les valeurs des propriétés sessionId et authorization au moyen d’un programme 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Consultez les rubriques [AzureDataLakeStoreLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) et [AuthorizationSessionGetResponse classe](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) pour plus d’informations sur les classes Data Factory utilisées dans le code. Vous devez ajouter une référence à Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll pour la classe WindowsFormsWebAuthenticationDialog.
 

## Propriétés de type du jeu de données Azure Data Lake

Pour obtenir une liste complète des sections et propriétés JSON disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, objet Blob Azure, table Azure, etc...).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement, le format, etc. des données dans le magasin de données. La section typeProperties correspondant au jeu de données de type **AzureDataLakeStore** a les propriétés suivantes.

| Propriété | Description | Requis |
| :-------- | :----------- | :-------- |
| folderPath | Chemin d’accès au conteneur et au dossier dans le magasin Azure Data Lake | Oui |
| fileName | <p>Le nom du fichier dans le magasin Azure Data Lake. fileName est facultative. </p><p>Si vous spécifiez un nom de fichier, l’activité (y compris la copie) fonctionne sur le fichier spécifique.</p><p>Quand fileName n’est pas spécifié, la copie inclut tous le fichier dans folderPath pour le jeu de données d’entrée.</p><p>Quand fileName n’est pas spécifié pour un jeu de données de sortie, le nom du fichier généré est au format suivant : Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)</p> | Non |
| partitionedBy | partitionedBy est une propriété facultative. Vous pouvez l'utiliser pour spécifier un folderPath dynamique et le nom de fichier pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. Consultez la section propriété Leveraging partitionedBy ci-dessous pour obtenir plus d’informations et des exemples. | Non |
| format | Deux types de formats sont pris en charge : **TextFormat**, **AvroFormat**. Vous devez définir la propriété de type sous format sur l'une de ces valeurs. Lorsque le format est TextFormat, vous pouvez spécifier des propriétés facultatives supplémentaires pour le format. Consultez la section [Définition de TextFormat](#specifying-textformat) ci-dessous pour plus de détails. | Non |
| compression | Spécifiez le type et le niveau de compression pour les données. Types pris en charge : GZip, Deflate et BZip2 ; niveaux pris en charge : Optimal et Fastest (le plus rapide). Pour plus d’informations, consultez la section [Prise en charge de la compression](#compression-support). | Non |

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
| columnDelimiter | Caractère utilisé comme séparateur de colonnes dans un fichier. Un seul caractère est autorisé pour le moment. Cette balise est facultative. La valeur par défaut est virgule (,). | Non |
| rowDelimiter | Caractère utilisé comme séparateur de lignes dans un fichier. Un seul caractère est autorisé pour le moment. Cette balise est facultative. La valeur par défaut est : [« \\r\\n », « \\r », « \\n »]. | Non |
| escapeChar | <p>Caractère spécial utilisé pour échapper au délimiteur de colonnes indiqué dans le contenu. Cette balise est facultative. Aucune valeur par défaut. Vous ne devez pas spécifier plusieurs caractères pour cette propriété.</p><p>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes, mais que vous voulez avoir le caractère virgule dans le texte (exemple : « Hello, world »), vous pouvez définir « $ » comme caractère d'échappement et utiliser la chaîne « Hello$, world » dans la source.</p><p>Notez que vous ne pouvez pas spécifier escapeChar et quoteChar pour une table.</p> | Non | 
| quoteChar | <p>Caractère spécial utilisé pour entourer de guillemets la valeur de la chaîne. Les séparateurs de colonnes et de lignes à l'intérieur des caractères de guillemets sont considérés comme faisant partie de la valeur de la chaîne. Cette balise est facultative. Aucune valeur par défaut. Vous ne devez pas spécifier plusieurs caractères pour cette propriété.</p><p>Par exemple, si vous avez une virgule (,) comme séparateur de colonnes mais que vous voulez avoir le caractère virgule dans le texte (exemple : <Hello  world>), vous pouvez définir « " » comme caractère de guillemet et utiliser la chaîne <"Hello, world"> dans la source. Cette propriété s’applique aux tables d’entrée et de sortie.</p><p>Notez que vous ne pouvez pas spécifier escapeChar et quoteChar pour une table.</p> | Non |
| nullValue | <p>Caractère(s) utilisé(s) pour représenter la valeur null dans le contenu du fichier blob. Cette balise est facultative. La valeur par défaut est « \\N ».</p><p>Par exemple, selon l’exemple ci-dessus, « NaN » dans l’objet blob sera converti en tant que valeur null au moment de la copie vers, par exemple, SQL Server.</p> | Non |
| encodingName | Spécifier le nom d'encodage. Pour obtenir une liste des noms de d’encodage valides, consultez : [Propriété Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Par exemple : windows-1250 ou shift\_jis. La valeur par défaut est : UTF-8. | Non | 

#### Exemples
L'exemple suivant illustre certaines des propriétés de format pour TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myfilename"
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

Pour utiliser le format Avro dans une table Hive, vous pouvez vous référer au [didacticiel Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).


### Prise en charge de la compression  
Le traitement de jeux de données de grande taille peut provoquer des goulots d’étranglement des E/S et du réseau. Par conséquent, les données compressées dans les magasins peuvent non seulement accélérer le transfert des données sur le réseau et économiser l’espace disque, mais également apporter des améliorations significatives des performances du traitement du Big Data. À ce stade, la compression est prise en charge pour les magasins de données de fichiers, comme les objets blob Azure ou un système de fichiers local.

Pour spécifier la compression pour un jeu de données, utilisez la propriété **compression** du jeu de données JSON, comme dans l'exemple suivant :

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Notez que la section **compression** a deux propriétés :
  
- **Type** : le codec de compression, qui peut être **GZIP**, **Deflate** ou **BZIP2**.  
- **Level** : le taux de compression, qui peut être **Optimal** ou **Fastest**. 
	- **Fastest** : l'opération de compression doit se terminer le plus rapidement possible, même si le fichier résultant n'est pas compressé de façon optimale. 
	- **Optimal** : l'opération de compression doit aboutir à une compression optimale, même si l'opération prend plus de temps. 
	
	Pour plus d'informations, consultez la rubrique [Niveau de compression](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Supposons que l'exemple de jeu de données ci-dessus est utilisé comme sortie d'une activité de copie. L'activité de copie compresse les données de sortie avec le codec GZIP en utilisant le taux optimal, puis va écrit les données compressées dans un fichier nommé pagecounts.csv.gz dans l'Azure Data Lake Store.

Quand vous spécifiez la propriété compression dans un jeu de données JSON d’entrée, le pipeline peut lire les données compressées à partir de la source et, quand vous spécifiez la propriété dans un jeu de données JSON de sortie, l’activité de copie peut écrire les données compressées dans la destination. Voici quelques exemples de scénarios :

- Lisez les données GZIP compressées à partir de l'Azure Data Lake Store, décompressez-les et écrivez les données résultantes dans une base de données SQL Azure. Dans ce cas, vous définissez le jeu de données d'entrée de l'Azure Data Lake Store avec la propriété de compression JSON. 
- Lisez les données d'un fichier de texte brut dans le système de fichiers local, compressez-les en utilisant le format GZip et écrivez les données compressées dans un Azure Data Lake Store. Dans ce cas, vous définissez le jeu de données Azure Data Lake Store de sortie avec la propriété de compression JSON.  
- Lisez les données compressées au format GZIP à partir d'un compression, décompressez-les, compressez-les en utilisant le format BZIP2 et écrivez les données résultantes dans un Azure Data Lake Store. Dans ce cas, vous définissez le jeu de données Azure Data Lake Store d'entrée avec le type de compression défini sur GZIP et le jeu de données de sortie avec le type de compression défini sur BZIP2.   


## Propriétés de type activité de copie Azure Data Lake  
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l'article [Création de pipelines](data-factory-create-pipelines.md). Des propriétés telles que le nom, la description, les tables d’entrée et de sortie, différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

**AzureDataLakeStoreSource** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| recursive | Indique si les données sont lues de manière récursive à partir des sous-dossiers ou uniquement du dossier spécifié. | True (valeur par défaut), False | Non |



**AzureDataLakeStoreSink** prend en charge les propriétés suivantes dans la section **typeProperties** :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Spécifie le comportement de copie. | <p>**PreserveHierarchy :** conserve la hiérarchie des fichiers dans le dossier cible, par exemple : le chemin d'accès relatif du fichier source vers le dossier source est identique au chemin d'accès relatif du fichier cible vers le dossier cible.</p><p>**FlattenHierarchy :** tous les fichiers du dossier source sont dans le premier niveau du dossier cible. Les fichiers cible auront un nom généré automatiquement.</p><p>**MergeFiles :** fusionne tous les fichiers du dossier source dans un seul fichier. Si le nom de fichier ou d'objet blob est spécifié, le nom de fichier fusionné est le nom spécifié. Dans le cas contraire, le nom de fichier est généré automatiquement.</p> | Non |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0128_2016-->