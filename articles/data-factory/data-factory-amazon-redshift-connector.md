<properties 
    pageTitle="Déplacer des données depuis Amazon Redshift à l’aide de Data Factory | Microsoft Azure" 
    description="Apprenez à déplacer des données à partir d’Amazon Redshift à l’aide d’Azure Data Factory." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="jingwang"/>


# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Déplacer des données depuis Amazon Redshift à l’aide d’Azure Data Factory

Cet article explique comment utiliser l'activité de copie d’une fabrique de données Azure pour déplacer des données d’Amazon Redshift vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et une liste de magasins de données sources/récepteurs.  

Actuellement, Data Factory prend uniquement en charge le déplacement de données d’Amazon Redshift vers d’autres magasins de données, mais pas l’inverse.

## <a name="prerequisites"></a>Composants requis

- Si vous déplacez des données vers un magasin de données local, donnez à la passerelle de gestion des données accès au cluster d’Amazon Redshift (en utilisant l’adresse IP de la machine). Pour davantage d’instructions, consultez la rubrique [Authorize access to the cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autoriser l’accès au cluster). 
- Si vous déplacez des données vers un magasin de données Azure, consultez la page [Plages IP des centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour connaître les plages d’adresses IP de calcul (y compris les plages SQL) utilisées par les centres de données Microsoft Azure. 

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données depuis Amazon Redshift consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données. 

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Il vous montre comment copier des données à partir d’Amazon Redshift vers le stockage d’objets blob Azure. Toutefois, les données peuvent être copiées vers n’importe lequel des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample:-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemple : copie de données à partir d’Amazon Redshift vers le stockage d’objets blob Azure
Cet exemple indique comment copier des données à partir d’une base de données Amazon Redshift locale vers un système de stockage Blob Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores) , via l’activité de copie d’Azure Data Factory.  
 
L’exemple contient les entités de fabrique de données suivantes :

- Un service lié de type [AmazonRedshift](#linked-service-properties).
- Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
- Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#dataset-type-properties).
- Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie toutes les heures les données de résultat d’une requête de base de données Amazon Redshift vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples. 

**Service lié Amazon Redshift**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
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

**Jeu de données d’entrée Amazon Redshift**

La définition de **« external » : true** informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory. Définissez cette propriété sur true sur un jeu de données d’entrée qui n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


**Jeu de données de sortie Azure Blob**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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



**Pipeline avec activité de copie**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
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
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service lié

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Amazon Redshift.

| Propriété | Description | Requis |
| -------- | ----------- | -------- | 
| type | La propriété de type doit être définie sur : **AmazonRedshift**. | Oui |
| server | Nom d’hôte ou adresse IP du serveur Amazon Redshift. | Oui |
| port | Le numéro du port TCP utilisé par le serveur Amazon Redshift pour écouter les connexions clientes. | Non, valeur par défaut : 5439 |
| database | Nom de la base de données Amazon Redshift. | Oui |
| username | Nom d’utilisateur ayant accès à la base de données.| Oui |
| password | Mot de passe du compte d’utilisateur.| Oui |


## <a name="dataset-type-properties"></a>Propriétés de type du jeu de données

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **RelationalTable** (qui inclut le jeu de données Amazon Redshift) a les propriétés suivantes

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| TableName | Nom de la table dans l’instance de base de données Amazon Redshift à laquelle le service lié fait référence. | Non (si la **requête** de **RelationalSource** est spécifiée) | 

## <a name="copy-activity-type-properties"></a>Propriétés de type de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités. 

En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Lorsque la source d’une activité de copie est de type **RelationalSource** (qui inclut Amazon Redshift), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| query | Utilise la requête personnalisée pour lire des données. | Chaîne de requête SQL. Par exemple : select * from MyTable. | Non (si **tableName** de **dataset** est spécifiée) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Mappage de type pour Amazon Redshift

Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en deux étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données vers Amazon Redshift, les mappages suivants seront utilisés pour passer des types Amazon Redshift aux types .NET.

Type Amazon Redshift | Type basé sur .NET
-------------------- | ---------------
SMALLINT | Int16
INTEGER | Int32
BIGINT | Int64
DÉCIMAL | DÉCIMAL
REAL | Single
DOUBLE PRECISION | Double
BOOLEAN | String
CHAR | String
VARCHAR | String
DATE | DateTime
TIMESTAMP | DateTime
TEXTE | String



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 
- [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie. 


<!--HONumber=Oct16_HO2-->


