---
title: "Déplacer des données depuis SAP Business Warehouse à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment déplacer des données depuis SAP Business Warehouse à l’aide d’Azure Data Factory."
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
ms.openlocfilehash: 6a14e5e0cab25b26782ebd45b52aa7542b7e24d5
ms.lasthandoff: 03/17/2017


---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Déplacer des données depuis SAP Business Warehouse à l’aide d’Azure Data Factory
Cet article explique comment utiliser l’activité de copie dans un pipeline Azure Data Factory pour déplacer des données de SAP Business Warehouse vers un autre magasin de données. Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge. Actuellement, Data Factory prend uniquement en charge le déplacement de données de SAP Business Warehouse vers d’autres magasins de données, mais pas l’inverse.


## <a name="supported-versions-and-installation"></a>Versions prises en charge et installation
Ce connecteur prend en charge la version 7.x de SAP Business Warehouse. Il prend en charge la copie de données d’InfoCubes et de QueryCubes (y compris les requêtes BEx) à l’aide de requêtes MDX.

Pour activer la connectivité à l’instance SAP BW, installez les composants suivants :
- **Passerelle de gestion des données** : le service Data Factory prend en charge la connexion aux magasins de données locaux (y compris SAP Business Warehouse) à l’aide d’un composant appelé passerelle de gestion des données. Consultez l’article [Déplacement de données d’un magasin de données local vers un magasin de données cloud](data-factory-move-data-between-onprem-and-cloud.md) pour en savoir plus sur la passerelle de gestion des données et obtenir des instructions détaillées sur la configuration de la passerelle. Une passerelle est requise même si SAP Business Warehouse est hébergé sur une machine virtuelle Azure IaaS. Vous pouvez installer la passerelle sur la même machine virtuelle que le magasin de données, ou sur une autre machine virtuelle pourvu que la passerelle puisse se connecter à la base de données.
- **Bibliothèque SAP NetWeaver** sur la machine passerelle. Vous pouvez obtenir la bibliothèque SAP Netweaver auprès de votre administrateur SAP, ou directement depuis le [Centre de téléchargement de logiciel SAP](https://support.sap.com/swdc). Recherchez la **Note SAP 1025361** pour obtenir l’emplacement de téléchargement de la version la plus récente. Assurez-vous que l’architecture de la bibliothèque de SAP NetWeaver (32 bits ou 64 bits) correspond à votre installation de la passerelle. Ensuite, installez tous les fichiers inclus dans le Kit de développement logiciel (SDK) RFC de SAP NetWeaver en fonction de la note SAP. La bibliothèque de SAP NetWeaver est également incluse dans l’installation des outils clients SAP.

## <a name="supported-sinks"></a>Récepteurs pris en charge
Consultez le tableau [Magasins de données pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats) pour obtenir la liste des magasins de données pris en charge en tant que sources et récepteurs pour l’activité de copie. Vous pouvez déplacer des données de SAP Business Warehouse vers n’importe quel magasin de données récepteur pris en charge. 

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie les données à partir de SAP Business Warehouse vers n’importe quel magasin de données récepteur pris en charge consiste à utiliser l’Assistant Copier des données. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données.

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Cet exemple indique comment copier des données depuis un SAP Business Warehouse local vers un Stockage Blob Azure. Toutefois, les données peuvent être copiées **directement** vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.  

> [!IMPORTANT]
> Cet exemple fournit des extraits de code JSON. Il n’inclut pas d’instructions détaillées pour la création de la fabrique de données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="sample-copy-data-from-sap-business-warehouse-to-azure-blob"></a>Exemple : copie de données depuis SAP Business Warehouse vers un objet blob Azure
L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [SapBw](#sap-bw-linked-service).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](#sap-bw-dataset).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](#sap-bw-source-in-copy-activity) et [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L’exemple copie les données d’une instance SAP Business Warehouse vers un objet blob Azure toutes les heures. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

### <a name="sap-business-warehouse-linked-service"></a>Service lié SAP Business Warehouse
Ce service lié relie votre instance SAP BW à la fabrique de données. La propriété du type est définie sur **SapBw**. La section typeProperties fournit des informations de connexion pour l’instance SAP BW. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Service lié Azure Storage
Le service lié relie votre compte de stockage Azure à la fabrique de données. La propriété de type est définie sur **AzureStorage**. La section typeProperties fournit des informations de connexion pour le compte de stockage Azure.

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

### <a name="sap-bw-input-dataset"></a>Jeu de données d’entrée SAP BW
Ce jeu de données définit le jeu de données SAP Business Warehouse. Vous définissez le type de données du jeu de données Data Factory sur **RelationalTable**. Actuellement, vous ne spécifiez pas de propriétés propres à un type pour un jeu de données SAP BW. La requête dans la définition de l’activité de copie spécifie les données à lire à partir de l’instance SAP BW. 

La définition de la propriété external sur true informe le service Data Factory qu’il s’agit d’une table qui est externe à la fabrique de données et non produite par une activité dans la fabrique de données.

Les propriétés de fréquence et d’intervalle définissent la planification. Dans ce cas, les données sont lues à partir de l’instance SAP BW toutes les heures. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
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
Ce jeu de données définit le jeu de données d’objet blob Azure de sortie. La propriété du type est définie sur AzureBlob. La section typeProperties indique l’emplacement auquel les données copiées à partir de l’instance SAP BW sont stockées. Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d’accès du dossier utilise l’année, le mois, le jour et l’heure de l’heure de début.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** (pour la source SAP BW) et le type **sink** est défini sur **BlobSink**. La requête spécifiée pour la propriété **query** sélectionne les données de la dernière heure à copier.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
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
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


## <a name="sap-bw-linked-service"></a>Service lié SAP BW
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié SAP Business Warehouse (BW).

Propriété | Description | Valeurs autorisées | Requis
-------- | ----------- | -------------- | --------
server | Nom du serveur sur lequel réside l’instance SAP BW. | string | Oui
systemNumber | Numéro de système du système SAP BW. | Nombre décimal à deux chiffres représenté sous forme de chaîne. | Oui
clientId | ID client du client dans le système SAP W. | Nombre décimal à trois chiffres représenté sous forme de chaîne. | Oui
username | Nom de l’utilisateur qui a accès au serveur SAP | string | Oui
password | Mot de passe pour l’utilisateur. | string | Oui
gatewayName | Nom de la passerelle que le service Data Factory doit utiliser pour se connecter à l’instance SAP BW locale. | string | Oui
Encryptedcredential | La chaîne d’informations d’identification chiffrée. | string | Non

## <a name="sap-bw-dataset"></a>Jeu de données SAP BW
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section **typeProperties** est différente pour chaque type de jeu de données et fournit des informations sur l’emplacement des données dans le magasin de données. Aucune propriété propre à un type n’est prise en charge pour le type de jeu de données SAP BW **RelationalTable**. 


## <a name="sap-bw-source-in-copy-activity"></a>Source SAP BW dans l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d’activités.

En revanche, les propriétés disponibles dans la section **typeProperties** de l’activité varient pour chaque type d’activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Lorsque la source de l’activité de copie est de type **RelationalSource** (qui inclut SAP BW), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| query | Spécifie la requête MDX pour lire les données de l’instance SAP BW. | Requête MDX. | Oui |

### <a name="type-mapping-for-sap-bw"></a>Mappage de type pour SAP BW
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en deux étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement des données à partir de SAP BW, les mappages suivants sont réalisés des types SAP BW vers les types .NET.

Type de données dans le dictionnaire ABAP | Type de données .Net
-------------------------------- | --------------
ACCP |    int
CHAR | String
CLNT | String
CURR | Décimal
CUKY | String
DEC | Décimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Décimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
UNITÉ | String
DATS | String
NUMC | String
TIMS | String

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]
[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]
[!INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

