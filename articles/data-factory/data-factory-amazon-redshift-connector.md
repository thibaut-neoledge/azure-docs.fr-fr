---
title: "Déplacer des données à partir d’Amazon Redshift à l’aide de Data Factory | Microsoft Docs"
description: "Apprenez à déplacer des données à partir d’Amazon Redshift à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 2e7b6a7c33e1f86133383f116df2fc2256133012
ms.contentlocale: fr-fr
ms.lasthandoff: 09/06/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Déplacer des données depuis Amazon Redshift à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie dans Azure Data Factory pour déplacer des données à partir de Amazon Redshift. L’article s’appuie sur l’article [Activités de déplacement des données](data-factory-data-movement-activities.md), qui présente une vue d’ensemble du déplacement de données avec l’activité de copie. 

Vous pouvez copier les données de Amazon Redshift dans tout magasin de données récepteur pris en charge. Consultez les [magasins de données pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pour obtenir la liste des magasins de données pris en charge en tant que récepteurs par l’activité de copie. Actuellement, Data Factory prend en charge le déplacement de données d’Amazon Redshift vers d’autres magasins de données, mais pas l’inverse.

> [!TIP]
> Pour obtenir de meilleures performances lors de la copie de grandes quantités de données à partir de Redshift, utilisez le mécanisme Redshift intégré UNLOAD via Amazon S3. Consultez la section [Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) pour plus d’informations.

## <a name="prerequisites"></a>Composants requis
* Si vous déplacez des données vers un magasin de données local, vous devez installer la [passerelle de gestion des données](data-factory-data-management-gateway.md) sur une machine locale. Donnez ensuite à la passerelle de gestion des données l’accès au cluster d’Amazon Redshift (en utilisant l’adresse IP de la machine). Pour davantage d’instructions, consultez la rubrique [Authorize access to the cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autoriser l’accès au cluster).
* Si vous déplacez des données vers un magasin de données Azure, consultez la page [Plages IP des centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) pour connaître les plages d’adresses IP de calcul et plages SQL utilisées par les centres de données Azure.

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui déplace les données d’une source Amazon Redshift à l’aide de différents outils/API.

Le moyen le plus simple de créer un pipeline consiste à utiliser l’**Assistant de copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

Vous pouvez également utiliser les outils suivants pour créer un pipeline : le **portail Azure**, **Visual Studio**, **Azure PowerShell**, le **modèle Azure Resource Manager**, l’**API .NET** et l’**API REST**. Consultez le [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie. 

Que vous utilisiez des outils ou des API, la création d’un pipeline qui déplace les données d’un magasin de données source vers un magasin de données récepteur implique les étapes suivantes : 

1. Création de **services liés** pour lier les magasins de données d’entrée et de sortie à votre fabrique de données.
2. Création de **jeux de données** pour représenter les données d’entrée et de sortie de l’opération de copie. 
3. Création d’un **pipeline** avec une activité de copie qui utilise un jeu de données en tant qu’entrée et un jeu de données en tant que sortie. 

Lorsque vous utilisez l’Assistant, les définitions JSON de ces entités Data Factory (services liés, jeux de données et pipeline) sont automatiquement créées pour vous. Lorsque vous utilisez des outils/API (à l’exception de l’API .NET), vous devez définir ces entités Data Factory au format JSON.  Pour consulter un exemple contenant des définitions JSON pour les entités Data Factory utilisées pour copier des données d’un magasin de données Amazon Redshift, consultez la section [Exemple JSON : copier des données depuis un système Amazon Redshift vers Blob Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) de cet article. 

Les sections suivantes fournissent des informations sur les propriétés JSON utilisées pour définir les entités Data Factory spécifiques à Amazon Redshift : 

## <a name="linked-service-properties"></a>Propriétés du service lié

Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Amazon Redshift.

| Propriété | Description | Requis |
| --- | --- | --- |
| type |La propriété de type doit être définie sur : **AmazonRedshift**. |Oui |
| server |Nom d’hôte ou adresse IP du serveur Amazon Redshift. |Oui |
| port |Le numéro du port TCP utilisé par le serveur Amazon Redshift pour écouter les connexions clientes. |Non, valeur par défaut : 5439 |
| database |Nom de la base de données Amazon Redshift. |Oui |
| username |Nom d’utilisateur ayant accès à la base de données. |Oui |
| password |Mot de passe du compte d’utilisateur. |Oui |

## <a name="dataset-properties"></a>Propriétés du jeu de données

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données. Elle fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type **RelationalTable** (qui inclut le jeu de données Amazon Redshift) a les propriétés suivantes

| Propriété | Description | Requis |
| --- | --- | --- |
| TableName |Nom de la table dans l’instance de base de données Amazon Redshift à laquelle le service lié fait référence. |Non (si la **requête** de **RelationalSource** est spécifiée) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie

Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités.

En revanche, les propriétés disponibles dans la section **typeProperties** de l’activité varient pour chaque type d’activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Quand une source dans une activité de copie est de type **AmazonRedshiftSource**, les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Requis |
| --- | --- | --- |
| query | Utilise la requête personnalisée pour lire des données. |Non (si **tableName** de **dataset** est spécifiée) |
| redshiftUnloadSettings | Groupe de propriétés lors de l’utilisation du mécanisme UNLOAD d’Amazon Redshift. | Non |
| s3LinkedServiceName | Fait référence à un service Amazon S3 à utiliser comme magasin temporaire en spécifiant un nom de service lié ADF de type AwsAccessKey. | Obligatoire sous « redshiftUnloadSettings » |
| bucketName | Indiquez le compartiment S3 pour stocker les données intermédiaires. S’il n’est pas fourni, l’activité de copie génère automatiquement un compartiment. | Obligatoire sous « redshiftUnloadSettings » |

Vous pouvez aussi utiliser le type **RelationalSource** (qui inclut Amazon Redshift) avec la propriété suivante dans la section typeProperties. Notez que ce type de source ne prend pas en charge le mécanisme UNLOAD de Redshift.

| Propriété | Description | Requis |
| --- | --- | --- |
| query |Utilise la requête personnalisée pour lire des données. | Non (si **tableName** de **dataset** est spécifiée) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Utiliser UNLOAD pour copier des données à partir d’Amazon Redshift

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) est un mécanisme fourni par Amazon Redshift qui peut décharger les résultats d’une requête dans un ou plusieurs fichiers sur Amazon Simple Storage Service (Amazon S3). C’est le moyen recommandé par Amazon pour copier un jeu de données volumineux à partir de Redshift.

**Exemple : Copier des données d’Amazon Redshift vers Azure SQL Data Warehouse à l’aide de UNLOAD, une copie intermédiaire et PolyBase**

Dans cet exemple de cas d’utilisation, l’activité de copie décharge d’abord les données d’Amazon Redshift sur Amazon S3, comme configuré dans « redshiftUnloadSettings », puis copie les données d’Amazon S3 dans un objet blob Azure, comme spécifié dans « stagingSettings », et enfin utilise PolyBase pour charger des données dans SQL Data Warehouse. Tous les formats intermédiaires sont gérés correctement par l’activité de copie.

![Flux de travail de copie de Redshift vers SQL DW](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemple JSON : copie de données à partir d’Amazon Redshift vers le stockage d’objets blob Azure
Cet exemple indique comment copier des données à partir d’une base de données Amazon Redshift locale vers un système de stockage Blob Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

* Un service lié de type [AmazonRedshift](#linked-service-properties).
* Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#dataset-properties).
* Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#copy-activity-properties) et [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties).

L’exemple copie toutes les heures les données de résultat d’une requête de base de données Amazon Redshift vers un objet blob. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

**Service lié Amazon Redshift :**

```json
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
```

**Service lié Azure Storage :**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Jeu de données d’entrée Amazon Redshift :**

Le paramètre `"external": true` informe le service Data Factory que ce jeu de données est externe à la fabrique de données et non produit par une activité dans la fabrique de données. Définissez cette propriété sur true sur un jeu de données d’entrée qui n’est pas produit par une activité dans le pipeline.

```json
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
```

**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

```json
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
```

**Activité de copie dans un pipeline, avec une source Azure Redshift (RelationalSource) et un récepteur blob :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
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
```
### <a name="type-mapping-for-amazon-redshift"></a>Mappage de type pour Amazon Redshift
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en deux étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données vers Amazon Redshift, les mappages suivants sont utilisés pour passer des types Amazon Redshift aux types .NET.

| Type Amazon Redshift | Type basé sur .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DÉCIMAL |DÉCIMAL |
| REAL |Single |
| DOUBLE PRECISION |Double |
| BOOLEAN |String |
| CHAR |String |
| VARCHAR |String |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXTE |String |

## <a name="map-source-to-sink-columns"></a>Mapper les colonnes source aux colonnes du récepteur
Pour en savoir plus sur le mappage de colonnes du jeu de données source à des colonnes du jeu de données récepteur, voir [Mappage des colonnes d’un jeu de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lecture renouvelée de sources relationnelles
Lorsque vous copiez des données à partir de magasins de données relationnels, gardez à l’esprit la répétabilité de l’opération, afin d’éviter des résultats imprévus. Dans Azure Data Factory, vous pouvez réexécuter une tranche manuellement. Vous pouvez également configurer une stratégie de nouvelles tentatives pour un jeu de données, afin qu’une tranche soit réexécutée en cas de défaillance. Lorsqu’une tranche est réexécutée d’une manière ou d’une autre, vous devez vous assurer que les mêmes données sont lues et ce, quel que soit le nombre d’exécutions de la tranche. Voir [Lecture renouvelée de sources relationnelles](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

* [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie.

