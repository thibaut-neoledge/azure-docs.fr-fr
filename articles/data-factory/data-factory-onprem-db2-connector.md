<properties 
	pageTitle="Déplacer des données depuis DB2 | Azure Data Factory" 
	description="Découvrez comment déplacer des données depuis une base de données DB2 à l’aide d’Azure Data Factory." 
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
	ms.date="06/16/2016" 
	ms.author="spelluru"/>

# Déplacer des données depuis DB2 à l’aide d’Azure Data Factory
Cet article explique comment utiliser l'activité de copie d’une fabrique de données Azure pour déplacer des données de DB2 vers un autre magasin de données. Cet article s'appuie sur l'article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d'ensemble du déplacement des données avec l'activité de copie et les combinaisons de magasins de données prises en charge.

Data Factory prend en charge la connexion à des sources DB2 locales à l'aide de la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle.

**Remarque :** vous devez utiliser la passerelle pour vous connecter à DB2 même si elle est hébergée sur des machines virtuelles IaaS Azure. Si vous essayez de vous connecter à une instance DB2 hébergée dans le cloud, vous pouvez également installer l'instance de la passerelle dans la machine virtuelle IaaS.

Actuellement, Data Factory prend uniquement en charge le déplacement de données de DB2 vers d’autres magasins de données, mais pas l’inverse.

## Installation 

Pour connecter la passerelle de gestion des données à la base de données DB2, à partir de la version 2.1, Azure Data Factory fournit un pilote intégré avec prise en charge de DB2 (SQLAM 9 / 10 / 11), notamment DB2 pour LUW (Linux, Unix, Windows), DB2 pour z/OS et DB2 pour i (ou AS/400). Par conséquent, vous n’avez plus besoin d’installer manuellement les pilotes lors de la copie de données à partir de DB2.

> [AZURE.NOTE] Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.


## Exemple : copie de données de DB2 vers Azure Blob

Cet exemple indique comment copier des données à partir d’une base de données DB2 locale vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie de Microsoft Azure Data Factory.
 
L’exemple contient les entités de fabrique de données suivantes :

1.	Un service lié de type [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5.	Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'exemple copie toutes les heures les données de résultat d’une requête de base de données DB2 vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données en suivant les instructions de l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Service lié DB2 :**

	{
	    "name": "OnPremDb2LinkedService",
	    "properties": {
	        "type": "OnPremisesDb2",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
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

**Jeu de données d’entrée DB2 :**

L'exemple suppose que vous avez créé une table « MyTable » dans DB2 et qu'elle contient une colonne appelée « timestamp » pour les données de série chronologique.

La définition de « external » : true et la spécification de la stratégie externalData informent Data Factory qu'il s'agit d'une table externe à Data Factory et non produite par une activité dans Data Factory. Notez que le **type** est défini sur **RelationalTable**.

	{
	    "name": "Db2DataSet",
	    "properties": {
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremDb2LinkedService",
	        "typeProperties": {},
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


**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

	{
	    "name": "AzureBlobDb2DataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie ci-dessus, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la table Orders.

	{
	    "name": "CopyDb2ToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "select * from "Orders""
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Db2DataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobDb2DataSet"
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
	                "name": "Db2ToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}


## Propriétés du service lié DB2

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié DB2.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| type | Le type de propriété doit être défini sur : **OnPremisesDB2** | Oui |
| server | Nom du serveur DB2. | Oui |
| database | Nom de la base de données DB2. | Oui |
| schema | Nom du schéma dans la base de données. Le nom du schéma respecte la casse. | Non |
| authenticationType | Type d'authentification utilisé pour se connecter à la base de données DB2. Les valeurs possibles sont : Anonyme, De base et Windows. | Oui |
| username | Spécifiez le nom d'utilisateur si vous utilisez l'authentification de base ou Windows. | Non |
| password | Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. | Non |
| gatewayName | Nom de la passerelle que le service Data Factory doit utiliser pour se connecter à la base de données DB2 locale. | Oui |

Pour plus d’informations sur la définition des informations d’identification pour une source de données DB2 locale, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)


## Propriétés de type du jeu de données DB2

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type RelationalTable (qui inclut le jeu de données DB2) a les propriétés suivantes.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| TableName | Nom de la table dans l'instance de base de données DB2 à laquelle le service lié fait référence. Le nom de la table respecte la casse. | Non (si la **requête** de **RelationalSource** est spécifiée) |

## Propriétés de type de l’activité de copie DB2.

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés telles que le nom, la description, les tables d'entrée et de sortie, les différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, quand la source est de type **RelationalSource** (qui inclut DB2), les propriétés suivantes sont disponibles dans la section typeProperties :


| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------- | -------------- |
| query | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : "query": "select * from "MySchema"."MyTable"". | Non (si **tableName** de **dataset** est spécifiée)|

> [AZURE.NOTE] Le nom du schéma et le nom de la table respectent la casse et doivent être entre "" (guillemets doubles) dans la requête.

**Exemple :**

 "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mappage de type pour DB2
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md), l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données vers DB2, les mappages suivants seront utilisés pour passer du type DB2 au type .NET.

Type de base de données DB2 | Type de .NET Framework 
----------------- | ------------------- 
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Décimal | Décimal
DecimalFloat | Décimal
Chiffre | Décimal
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String
VarChar | String
LongVarChar | String
DB2DynArray | String
Fichier binaire | Byte
VarBinary | Byte
LongVarBinary | Byte
Graphic | String
VarGraphic | String
LongVarGraphic | String
Clob | String
Blob | Byte
DbClob | String
SmallInt | Int16
Integer | Int32
BigInt | Int64
Real | Single
Double | Double
Float | Double
Décimal | Décimal
DecimalFloat | Décimal
Chiffre | Décimal
Date | Datetime
Time | TimeSpan
Timestamp | DateTime
Xml | Byte
Char | String


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!---HONumber=AcomDC_0803_2016-->