<properties 
	pageTitle="Déplacer des données depuis Teradata | Azure Data Factory" 
	description="En savoir plus sur le connecteur Teradata pour le service Data Factory qui vous permet de déplacer des données depuis une base de données Teradata" 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Déplacer des données depuis Teradata à l’aide d’Azure Data Factory

Cet article explique comment utiliser l'activité de copie d’une fabrique de données Azure pour déplacer des données de Teradata vers un autre magasin de données. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasins de données prises en charge.

Data Factory prend en charge la connexion à des sources Teradata locales via la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle.

**Remarque :** vous devez utiliser la passerelle pour vous connecter à Teradata même si elle est hébergée sur des machines virtuelles IaaS Azure. Si vous essayez de vous connecter à une instance Teradata hébergée dans le cloud, vous pouvez également installer l'instance de la passerelle dans la machine virtuelle IaaS.

Data Factory prend uniquement en charge le déplacement de données de Teradata vers d’autres magasins de données, mais pas l’inverse.

## Installation 

Pour que la passerelle de gestion des données puisse se connecter à la base de données Teradata, vous devez installer le [fournisseur de données .NET pour Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) sur le même système que la passerelle de gestion des données.

> [AZURE.NOTE] Consultez la page [Résolution des problèmes de passerelle](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.

### Exemple : copie de données de Teradata vers Azure Blob

Cet exemple indique comment copier des données à partir d’une base de données Teradata locale vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie de Microsoft Azure Data Factory.
 
L’exemple contient les entités de fabrique de données suivantes :

1.	Un service lié de type [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.	Le [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie toutes les heures les données de résultat d’une requête de base de données Teradata vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données en suivant les instructions de l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Service lié Teradata :**

	{
	    "name": "OnPremTeradataLinkedService",
	    "properties": {
	        "type": "OnPremisesTeradata",
	        "typeProperties": {
	            "server": "<server>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Service lié Azure Blob Storage :**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}


**Jeu de données d’entrée Teradata :**

L'exemple suppose que vous avez créé une table « MyTable » dans Teradata et qu'elle contient une colonne appelée « timestamp » pour les données de série chronologique.

La définition de « external » : true et la spécification de la stratégie externalData informent Data Factory qu'il s'agit d'une table externe à Data Factory et non produite par une activité dans Data Factory.

	{
	    "name": "TeradataDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremTeradataLinkedService",
	        "typeProperties": {
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}


**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

	{
	    "name": "AzureBlobTeradataDataSet",
	    "properties": {
	        "published": false,
	        "location": {
	            "type": "AzureBlobLocation",
	            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	            "linkedServiceName": "AzureStorageLinkedService"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.

	{
	    "name": "CopyTeradataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "TeradataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobTeradataDataSet"
	                    }
	                ],					
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "TeradataToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z",
	        "isPaused": false
	    }
	}


## Propriétés du service lié Teradata

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Teradata.

Propriété | Description | Requis
-------- | ----------- | --------
type | Le type de propriété doit être défini sur : **OnPremisesTeradata** | Oui
server | Nom du serveur Teradata. | Oui
authenticationType | Type d'authentification utilisé pour se connecter à la base de données Teradata. Les valeurs possibles sont : Anonyme, De base et Windows. | Oui
username | Spécifiez le nom d'utilisateur si vous utilisez l'authentification de base ou Windows. | Non 
password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | Non 
gatewayName | Nom de la passerelle que le service Data Factory doit utiliser pour se connecter à la base de données Teradata locale. | Oui

Pour plus d’informations sur la définition des informations d’identification pour une source de données Teradata locale, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security)

## Propriétés de type du jeu de données Teradata

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. Il n’existe aucune propriété de type prise en charge pour le jeu de données Teradata pour l’instant.


## Propriétés de type de l’activité de copie Teradata

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés telles que le nom, la description, les tables d'entrée et de sortie, les différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, lorsque la source est de type **RelationalSource** (qui inclut Teradata), les propriétés suivantes sont disponibles dans la section **typeProperties**:

Propriété | Description | Valeurs autorisées | Requis
-------- | ----------- | -------------- | --------
query | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable. | Oui

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mappage de type pour Teradata

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données vers Teradata, les mappages suivants seront utilisés pour passer du type Teradata au type .NET.

Type de base de données Teradata | Type de .NET Framework
----------------- | ---------------------------
Char | String
Clob | String
Graphic | String
VarChar | String
VarGraphic | String
Blob | Byte
Byte | Byte
VarByte | Byte
BigInt | Int64
ByteInt | Int16
Décimal | Décimal
Double | Double
Integer | Int32
Number | Double
SmallInt | Int16
Date | DateTime
Time | TimeSpan
Time With Time Zone | String
Timestamp | DateTime
Timestamp With Time Zone | DateTimeOffset
Interval Day | TimeSpan
Interval Day To Hour | TimeSpan
Interval Day To Minute | TimeSpan
Interval Day To Second | TimeSpan
Interval Hour | TimeSpan
Interval Hour To Minute | TimeSpan
Interval Hour To Second | TimeSpan
Interval Minute | TimeSpan
Interval Minute To Second | TimeSpan
Interval Second | TimeSpan
Interval Year | String
Interval Year To Month | String
Interval Month | String
Period(Date) | String
Period(Time) | String
Period(Time With Time Zone) | String
Period(Timestamp) | String
Period(Timestamp With Time Zone) | String
Xml | String

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0224_2016-->