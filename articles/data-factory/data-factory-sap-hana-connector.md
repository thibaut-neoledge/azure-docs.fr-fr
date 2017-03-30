---
title: "Déplacer des données depuis SAP HANA à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment déplacer des données depuis SAP HANA à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 415e91ca2f1e47094fc8b64569b5cc29a706999a
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>Déplacer des données depuis SAP HANA à l’aide d’Azure Data Factory
Cet article explique comment utiliser l'activité de copie d’un pipeline de fabrique de données Azure pour déplacer des données de SAP HANA vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge. Actuellement, Data Factory prend uniquement en charge le déplacement de données de SAP HANA vers d’autres magasins de données, mais pas l’inverse.

## <a name="supported-versions-and-installation"></a>Versions prises en charge et installation
Ce connecteur prend en charge n’importe quelle version de base de données SAP HANA. Il prend en charge la copie de données à partir de modèles d’informations HANA (comme les vues Analyse et Calcul) et les tables Ligne/Colonne à l’aide de requêtes SQL.

Pour activer la connectivité à l’instance SAP HANA, installez les composants suivants :
- **Passerelle de gestion des données** : le service de fabrique de données prend en charge la connexion aux magasins de données locaux (y compris SAP HANA) à l’aide d’un composant appelé passerelle de gestion des données. Consultez l’article [Déplacement de données d’un magasin de données local vers un magasin de données cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle. Une passerelle est requise même si SAP HANA est hébergé sur une machine virtuelle Azure IaaS. Vous pouvez installer la passerelle sur la même machine virtuelle que le magasin de données, ou sur une autre machine virtuelle pourvu que la passerelle puisse se connecter à la base de données.
- Le **pilote ODBC SAP HANA** sur la machine passerelle. Vous pouvez télécharger le pilote ODBC SAP HANA à partir du [Centre de téléchargement de logiciels SAP](https://support.sap.com/swdc). Faites une recherche avec le mot-clé **SAP HANA CLIENT for Windows**. 

## <a name="supported-sinks"></a>Récepteurs pris en charge
Consultez le tableau [Banques de données prises en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pour obtenir la liste des banques de données prises en charge en tant que sources et réceptrices pour l’activité de copie. Vous pouvez déplacer des données de SAP HANA vers n’importe quel magasin de données de récepteur pris en charge. 

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie des données à partir d’une base de données SAP HANA vers n’importe quel magasin de données récepteur pris en charge consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Cet exemple indique comment copier des données depuis SAP HANA vers un système Blob Storage Microsoft Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs répertoriés [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.  

> [!IMPORTANT]
> Cet exemple fournit des extraits de code JSON. Il n’inclut pas d’instructions détaillées pour la création de la fabrique de données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="sample-copy-data-from-sap-hana-to-azure-blob"></a>Exemple : copie de données de SAP HANA vers Azure Blob
L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [SapHana](#sap-hana-linked-service).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#sap-hana-dataset).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#sap-hana-source-in-copy-activity) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données d’une instance SAP HANA vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-hana-linked-service"></a>Service lié SAP HANA
Ce service lié relie votre instance SAP HANA à la fabrique de données. La propriété type est définie sur **SapHana**. La section typeProperties fournit des informations de connexion pour l’instance SAP HANA.

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Service lié Azure Storage
Le service lié relie votre compte de stockage Azure à la fabrique de données. La propriété de type est définie sur **AzureStorage**. La section typeProperties fournit des informations de connexion pour le compte Azure Storage.

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

### <a name="sap-hana-input-dataset"></a>Jeu de données d’entrée SAP HANA

Ce jeu de données définit le jeu de données SAP HANA. Vous définissez le type de données du jeu de données Data Factory sur **RelationalTable**. Actuellement, vous ne spécifiez pas de propriétés propres à un type pour un jeu de données SAP HANA. La requête dans la définition de l’activité de copie spécifie les données à lire à partir de l’instance SAP HANA. 

La définition de la propriété external sur true informe le service Data Factory qu’il s’agit d’un jeu de données qui est externe à la Data Factory et non produit par une activité dans la Data Factory.

Les propriétés de fréquence et d’intervalle définissent la planification. Dans ce cas, les données sont lues à partir de l’instance SAP HANA toutes les heures. 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Jeu de données de sortie d’objet Blob Azure
Ce jeu de données définit le jeu de données d’objet blob Azure de sortie. La propriété type est définie sur AzureBlob. La section typeProperties indique l’endroit auquel les données copiées à partir de l’instance SAP HANA sont stockées. Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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


### <a name="pipeline-with-copy-activity"></a>Pipeline avec activité de copie

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** (pour la source SAP HANA) et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
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
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-hana-linked-service"></a>Service lié SAP HANA
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié SAP HANA.

Propriété | Description | Valeurs autorisées | Requis
-------- | ----------- | -------------- | --------
server | Le nom du serveur sur lequel réside l’instance SAP HANA. Si votre serveur utilise un port personnalisé, spécifiez `server:port`. | string | Oui
authenticationType | Type d'authentification. | chaîne. « Basic » ou « Windows » | Oui 
username | Nom de l’utilisateur qui a accès au serveur SAP | string | Oui
password | Mot de passe pour l’utilisateur. | string | Oui
gatewayName | Nom de la passerelle que le service Data Factory doit utiliser pour se connecter à l’instance SAP HANA locale. | string | Oui
Encryptedcredential | La chaîne d’informations d’identification chiffrée. | string | Non

## <a name="sap-hana-dataset"></a>Jeu de données SAP HANA
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. Aucune propriété propre à un type n’est prise en charge pour le type de jeu de données SAP HANA **RelationalTable**. 


## <a name="sap-hana-source-in-copy-activity"></a>Source SAP HANA dans l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d’activités.

En revanche, les propriétés disponibles dans la section **typeProperties** de l'activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Lorsque la source de l’activité de copie est de type **RelationalSource** (qui inclut SAP HANA), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| query | Spécifie la requête SQL pour lire les données de l’instance SAP HANA. | Requête SQL. | Oui |

### <a name="type-mapping-for-sap-hana"></a>Mappage de type pour SAP HANA
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en deux étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement des données à partir de SAP HANA, les mappages suivants sont réalisés des types SAP HANA vers les types .NET.

Type SAP HANA | Type basé sur .NET
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
BIGINT | Int64
REAL | Single
DOUBLE | Single
DÉCIMAL | Décimal
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
DATE | DateTime
TEMPS | intervalle de temps
TIMESTAMP | DateTime
SECONDDATE | DateTime

## <a name="known-limitations"></a>Limites connues
Il existe quelques limitations connues lors de la copie des données à partir de SAP HANA :

- Les chaînes NVARCHAR sont tronquées à une longueur maximale de 4 000 caractères Unicode
- SMALLDECIMAL n’est pas pris en charge
- VARBINARY n’est pas pris en charge
- Les dates valides sont celles comprises entre 1899/12/30 et 9999/12/31

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

