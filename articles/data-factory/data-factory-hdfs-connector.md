<properties 
	pageTitle="Transfert de données à partir d’un HDFS en local | Azure Data Factory" 
	description="Découvrez comment transférer des données à partir d’un HDFS en local à l’aide d’Azure Data Factory." 
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
	ms.date="04/05/2016" 
	ms.author="spelluru"/>

# Transfert de données à partir d’un HDFS en local à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie d’une fabrique de données Azure pour déplacer des données entre un HDFS local et un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Actuellement, Data Factory prend uniquement en charge le déplacement de données de HDFS en local vers d’autres magasins de données, mais pas l’inverse.


## Activation de la connectivité
Le service Data Factory prend en charge la connexion à des sources HDFS locales à l’aide de la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle. Vous devez utiliser la passerelle pour vous connecter à HDFS même si elle est hébergée sur des machines virtuelles IaaS Azure.

Bien qu’il soit possible d’installer la passerelle sur le même ordinateur local ou la même machine virtuelle Azure que le HDFS, nous vous recommandons de l’installer sur un ordinateur ou une machine virtuelle IaaS Azure distinct(e) afin d’éviter les conflits de ressources et d’obtenir de meilleures performances. Lorsque vous installez la passerelle sur un ordinateur distinct, l’ordinateur doit être en mesure d’accéder à l’ordinateur qui exécute le système HDFS.

## Exemple : copie de données entre un système HDFS local et un objet blob Azure

Cet exemple indique comment copier des données depuis un système HDFS local vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores), via l’activité de copie de Microsoft Azure Data Factory.
 
L’exemple contient les entités de fabrique de données suivantes :

1.	Un service lié de type [OnPremisesHdfs](#hdfs-linked-service-properties).
2.	Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [FileShare](#hdfs-dataset-type-properties).
4.	Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Un [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [FileSystemSource](#hdfs-copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie toutes les heures les données de résultat d’une requête d’un système HDFS en local vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données en suivant les instructions de l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md).

**Service lié de HDFS** : cet exemple utilise l’authentification Windows. Consultez la section [Service lié HDFS](#hdfs-linked-service-properties) pour connaître les différents types d’authentification que vous pouvez utiliser.

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Service lié Azure Storage**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Jeu de données d’entrée HDFS** : ce jeu de données fait référence au dossier HDFS DataTransfer/UnitTest/. Le pipeline copie tous les fichiers de son dossier vers la destination.

La définition de « external » : « true » et la spécification de la stratégie externalData (facultative) informent le service Data Factory qu’il s’agit d’une table qui est externe à la Data Factory et non produite par une activité dans la Data Factory.
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Jeu de données de sortie d’objet Blob Azure**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d’entrée et de sortie ci-dessus, et qui est planifiée pour s’exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **FileSystemSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Propriétés du service lié HDFS

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié HDFS.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| type | La propriété de type doit être définie sur **Hdfs**. | Oui | 
| Url | URL vers le système HDFS | Oui |
| Encryptedcredential | Sortie [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) des informations d’accès. | Non |
| userName | Nom d’utilisateur de l’authentification Windows | Oui (pour l’authentification Windows)
| password | Mot de passe de l’authentification Windows | Oui (pour l’authentification Windows)
| authenticationType | Windows ou anonyme. | Oui |
| gatewayName | Nom de la passerelle que le service Data Factory doit utiliser pour se connecter au système HDFS. | Oui |   

Pour en savoir plus sur la définition des informations d’identification pour un système HDFS local, consultez [Configuration des informations d’identification et de la sécurité](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security)

### Utilisation de l’authentification anonyme

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Utilisation de l’authentification Windows
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## Propriétés de type du jeu de données HDFS

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du jeu de données de type **FileShare** (comprenant le jeu de données HDFS) présente les propriétés suivantes.

Propriété | Description | Requis
-------- | ----------- | --------
folderPath | Chemin d'accès au dossier. Exemple : myfolder<br/><br/>Utilisez le caractère d’échappement « \\ » pour les caractères spéciaux contenus dans la chaîne. Par exemple : pour folder\\subfolder, spécifiez folder\\\subfolder et pour d:\\samplefolder, spécifiez d:\\\samplefolder.<br/><br/>Vous pouvez combiner ceci avec **partitionBy** pour avoir des chemins de dossiers basés sur un segment heure/date de début/fin. | Oui
fileName | Spécifiez le nom du fichier dans l’élément **folderPath** si vous souhaitez que la table se réfère à un fichier spécifique du dossier. Si vous ne spécifiez aucune valeur pour cette propriété, le tableau pointe vers tous les fichiers du dossier.<br/><br/>Lorsque fileName n'est pas spécifié pour un jeu de données de sortie, le nom du fichier généré aura ce format dans l'exemple suivant : <br/><br/>Data.<Guid>.txt (par exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Non
partitionedBy | partitionedBy peut être utilisé pour spécifier un folderPath dynamique, fileName pour les données de série chronologique. Par exemple, folderPath peut être paramétré pour toutes les heures de données. | Non
fileFilter | Spécifiez un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers. <br/><br/>Les valeurs autorisées sont les suivantes : * (plusieurs caractères) et ? (un seul caractère).<br/><br/>Exemples 1 : "fileFilter": "*.log"<br/>Exemple 2 : "fileFilter": 2014-1-?.txt"<br/><br/>**Remarque** : fileFilter s’applique à un jeu de données FileShare d’entrée | Non
| compression | Spécifiez le type et le niveau de compression pour les données. Types pris en charge : **GZip**, **Deflate** et **BZip2** ; niveaux pris en charge : **Optimal** et **Fastest** (le plus rapide). Notez que, pour l’instant, les paramètres de compression ne sont pas pris en charge pour les données au format **AvroFormat** et **OrcFormat**. Pour plus d’informations, consultez la section [Prise en charge de la compression](#compression-support). | Non |
| format | Les types de formats suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat** et **OrcFormat**. La propriété **type** située sous Format doit être définie sur l’une de ces valeurs. Pour plus d’informations, consultez les sections [Définition de TextFormat](#specifying-textformat), [Définition d’AvroFormat](#specifying-avroformat), [Définition de JsonFormat](#specifying-jsonformat) et [Définition d’OrcFormat](#specifying-orcformat). Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), vous pouvez ignorer la section Format dans les deux définitions de jeu de données d’entrée et de sortie. | Non 


> [AZURE.NOTE] fileName et fileFilter ne peuvent pas être utilisés simultanément.


### Utilisation de la propriété partitionedBy

Comme mentionné ci-dessus, vous pouvez spécifier un folderPath dynamique, fileName pour les données de série chronologique avec partitionedBy. Vous pouvez le faire avec les macros Data Factory et les variables système SliceStart et SliceEnd, qui indiquent la période logique d’une tranche de données spécifique.

Consultez les articles [Création de jeux de données](data-factory-create-datasets.md), [Planification et exécution](data-factory-scheduling-and-execution.md) et [Création de pipelines](data-factory-create-pipelines.md) pour mieux comprendre les jeux de données de série chronologique, la planification et les segments.

#### Exemple 1 :

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Dans l'exemple ci-dessus {Slice} est remplacé par la valeur de la variable système Data Factory SliceStart au format (AAAAMMJJHH) spécifié. SliceStart fait référence à l'heure de début du segment. folderPath est différent pour chaque segment. Par exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### Exemple 2 :

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

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]  
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Propriétés de type de l’activité de copie HDFS

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés telles que le nom, la description, les tables d'entrée et de sortie, les différentes stratégies, etc. sont disponibles pour tous les types d'activités.

Par contre, les propriétés disponibles dans la section typeProperties de l'activité varient avec chaque type d'activité et dans le cas de l'activité de copie, elles varient selon les types de sources et de récepteurs.

Dans le cas d’une activité de copie, quand la source est de type **FileSystemSource**, les propriétés suivantes sont disponibles dans la section typeProperties :

**FileSystemSource** prend en charge les propriétés suivantes :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| recursive | Indique si les données sont lues de manière récursive dans les sous-dossiers ou uniquement dans le dossier spécifié. | True, False (par défaut)| Non |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

<!---HONumber=AcomDC_0608_2016-->