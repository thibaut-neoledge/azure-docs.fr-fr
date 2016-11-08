---
title: Déplacement de données à partir d’Amazon Simple Storage Service à l’aide de Data Factory | Microsoft Docs
description: Découvrez comment déplacer des données à partir d’Amazon Simple Storage Service (S3) à l’aide d’Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: jingwang

---
# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Déplacement de données à partir d’Amazon Simple Storage Service à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie d’une fabrique de données Azure pour déplacer des données d’Amazon Simple Storage Service (S3) vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement de données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement de données et une liste des magasins de données source/récepteur pris en charge avec l’activité de copie.  

Actuellement, Data Factory prend uniquement en charge le déplacement de données d’Amazon S3 vers d’autres magasins de données, mais pas l’inverse.

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données depuis Amazon S3 consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données. 

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Il vous montre comment copier des données à partir d’Amazon S3 vers le stockage des objets blob Azure. Toutefois, les données peuvent être copiées vers n’importe lequel des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample:-copy-data-from-amazon-s3-to-azure-blob"></a>Exemple : copie de données à partir d’Amazon S3 vers un objet blob Azure
Cet exemple indique comment copier des données à partir d’Amazon S3 vers un stockage des objets blob Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores) , via l’activité de copie d’Azure Data Factory.  

L’exemple contient les entités de fabrique de données suivantes :

* Un service lié de type [AwsAccessKey](#linked-service-properties).
* Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
* Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [AmazonS3](#dataset-type-properties).
* Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Un [pipeline](data-factory-create-pipelines.md) avec activité de copie qui utilise [FileSystemSource](#copy-activity-type-properties) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie des données à partir d’Amazon S3 vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples. 

**Service lié Amazon S3**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
            }
        }
    }

**Service lié Azure Storage**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Jeu de données d’entrée Amazon S3**

La définition de **« external » : true** informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à Data Factory et non produit par une activité dans Data Factory. Définissez cette propriété sur true sur un jeu de données d’entrée qui n’est pas produit par une activité dans le pipeline.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }



**Jeu de données de sortie Azure Blob**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **FileSystemSource** et le type **sink** est défini sur **BlobSink**. 

    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriétés du service lié
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié Amazon S3 (**AwsAccessKey**).

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| accessKeyID |ID de la clé d’accès secrète. |string |Oui |
| secretAccessKey |La clé d’accès secrète elle-même. |Chaîne secrète chiffrée |Oui |

## <a name="dataset-type-properties"></a>Propriétés de type du jeu de données
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. La section typeProperties du jeu de données de type **Amazon S3** (comprenant le jeu de données Amazon S3) présente les propriétés suivantes

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| bucketName |Le nom de compartiment S3. |string |Oui |
| key |La clé d’objet S3. |string |Non |
| prefix |Préfixe de la clé d’objet S3. Les objets dont les clés commencent par ce préfixe sont sélectionnés. S’applique uniquement lorsque la clé est vide. |string |Non |
| version |La version de l’objet S3 si le contrôle de version S3 est activé. |string |Non |
| format |Les types de formats suivants sont pris en charge : **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Définissez la propriété **type** située sous Format sur l’une de ces valeurs. Pour plus d’informations, consultez les sections [Définition de TextFormat](#specifying-textformat), [Définition d’AvroFormat](#specifying-avroformat), [Définition de JsonFormat](#specifying-jsonformat), [Définition d’OrcFormat](#specifying-orcformat) et [Définition de ParquetFormat](#specifying-parquetformat). Si vous souhaitez copier des fichiers en l’état entre des magasins de fichiers (copie binaire), vous pouvez ignorer la section Format dans les deux définitions de jeu de données d’entrée et de sortie. |Non | |
| compression |Spécifiez le type et le niveau de compression pour les données. Types pris en charge : **GZip**, **Deflate** et **BZip2** ; niveaux pris en charge : **Optimal** et **Fastest** (le plus rapide). Pour l’instant, les paramètres de compression ne sont pas pris en charge pour les données au format **AvroFormat** ou **OrcFormat**. Pour plus d’informations, consultez la section [Prise en charge de la compression](#compression-support) . |Non | |

> [!NOTE]
> bucketName + key spécifient l’emplacement de l’objet S3 où le compartiment est le conteneur racine pour les objets S3 et la clé est le chemin d’accès complet à l’objet S3.
> 
> 

### <a name="sample-dataset-with-prefix"></a>Exemple de jeu de données avec le préfixe
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-(with-version)"></a>Exemple de jeu de données (avec la version)
    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Chemins d’accès dynamiques pour S3
Dans l’exemple, nous avons utilisé des valeurs fixes pour les propriétés key et bucketName dans le jeu de données Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Vous pouvez demander à Data Factory de calculer les propriétés key et bucketName dynamiquement au moment de l’exécution à l’aide de variables système telles que SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

Vous pouvez faire de même pour la propriété « prefix » d’un jeu de données Amazon S3. Pour obtenir la liste des fonctions et variables prises en charge, consultez [Variables système et fonctions Data Factory](data-factory-functions-variables.md) . 

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="copy-activity-type-properties"></a>Propriétés de type de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités. 

En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Lorsque la source de l’activité de copie est de type **FileSystemSource** (qui inclut Amazon S3), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| recursive |Spécifie s’il faut répertorier de manière récursive les objets S3 sous le répertoire. |true/false |Non |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants : 

* [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie. 

<!--HONumber=Oct16_HO2-->


