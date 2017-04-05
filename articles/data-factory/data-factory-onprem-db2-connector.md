---
title: "Déplacer des données depuis DB2 à l’aide d’Azure Data Factory | Microsoft Docs"
description: "Découvrez comment déplacer des données depuis une base de données DB2 à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: c1644e17-4560-46bb-bf3c-b923126671f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 59a83a62ddee89c44533060b811bc8fc2f144bee
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-db2-using-azure-data-factory"></a>Déplacer des données depuis DB2 à l’aide d’Azure Data Factory
Cet article décrit la façon dont vous pouvez utiliser l’activité de copie dans une Azure Data Factory pour copier des données depuis une base de données DB2 locale vers n’importe quel magasin de données de la colonne du récepteur dans la section [Sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

Actuellement, Data Factory prend uniquement en charge le déplacement de données depuis une base de données DB2 vers les [magasins récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats), et non le déplacement de données depuis d’autres magasins de données vers une base de données DB2.

## <a name="prerequisites"></a>Composants requis
Pour que le service Azure Data Factory puisse se connecter à la base de données DB2 locale, vous devez installer une passerelle de gestion de données sur l’ordinateur qui héberge la base de données ou sur un autre ordinateur, afin d’éviter toute mise en concurrence avec la base de données pour les ressources. La passerelle de gestion de données est un composant qui connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Consultez l’article [Passerelle de gestion des données](data-factory-data-management-gateway.md) pour obtenir des informations détaillées sur la passerelle de gestion des données. Consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour obtenir des instructions détaillées sur la configuration de la passerelle, un pipeline de données, pour déplacer des données.

Vous devez utiliser la passerelle pour vous connecter à une base de données DB2, même si elle est hébergée dans le cloud, par exemple sur une machine virtuelle IaaS Azure. Vous pouvez installer la passerelle sur la même machine virtuelle que le magasin de données ou sur une autre machine virtuelle, tant que la passerelle peut se connecter à la base de données.  

La passerelle de gestion des données fournit un pilote DB2 intégré. Par conséquent, vous n’avez pas besoin d’installer manuellement un pilote lors de la copie de données à partir de DB2.

> [!NOTE]
> Consultez [Résolution des problèmes de passerelle](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) pour obtenir des conseils sur la résolution des problèmes de connexion/passerelle.

## <a name="supported-versions"></a>Versions prises en charge
Ce connecteur DB2 prend en charge les plateformes et versions IBM DB2 suivantes avec les versions 9, 10 et 11 de SQL Access Manager (SQLAM) DRDA (Distributed Relational Database Architecture) :

* IBM DB2 pour z/OS 11.1
* IBM DB2 pour z/OS 10.1
* IBM DB2 pour i 7.2
* IBM DB2 pour i 7.1
* IBM DB2 pour LUW 11
* IBM DB2 pour LUW 10.5
* IBM DB2 pour LUW 10.1

## <a name="getting-started"></a>Prise en main
Vous pouvez créer un pipeline avec une activité de copie qui déplace les données d’un magasin de données DB2 local à l’aide de différents outils/API. 

- Le moyen le plus simple de créer un pipeline consiste à utiliser **l’Assistant de copie**. Consultez la page [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md) pour une procédure pas à pas rapide sur la création d’un pipeline à l’aide de l’Assistant Copier des données. 
- Vous pouvez également utiliser les outils suivants pour créer un pipeline : le **portail Azure**, **Visual Studio**, **Azure PowerShell**, le **modèle Azure Resource Manager**, l’**API .NET** et l’**API REST**. Consultez le [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec une activité de copie. 

Que vous utilisiez des outils ou des API, la création d’un pipeline qui déplace les données d’un magasin de données source vers un magasin de données récepteur implique les étapes suivantes :

1. Création de **services liés** pour lier les magasins de données d’entrée et de sortie à votre fabrique de données.
2. Création de **jeux de données** pour représenter les données d’entrée et de sortie de l’opération de copie. 
3. Création d’un **pipeline** avec une activité de copie qui prend un jeu de données en tant qu’entrée et un jeu de données en tant que sortie. 

Lorsque vous utilisez l’Assistant, les définitions JSON de ces entités Data Factory (services liés, jeux de données et pipeline) sont automatiquement créées pour vous. Lorsque vous utilisez des outils/API (à l’exception de l’API .NET), vous devez définir ces entités Data Factory au format JSON.  Pour consulter un exemple contenant des définitions JSON pour les entités Data Factory utilisées pour copier des données d’un magasin de données DB2 local, consultez la section [Exemple JSON : copier des données depuis DB2 vers Azure Blob](#json-example-copy-data-from-db2-to-azure-blob) de cet article. 

Les sections suivantes contiennent des informations détaillées sur les propriétés JSON utilisées pour définir les entités Data Factory propres à un magasin de données DB2 :

## <a name="linked-service-properties"></a>Propriétés du service lié
Le tableau suivant fournit la description des éléments JSON spécifiques au service lié DB2.

| Propriété | Description | Requis |
| --- | --- | --- |
| type |Le type de propriété doit être défini sur : **OnPremisesDB2** |Oui |
| server |Nom du serveur DB2. |Oui |
| database |Nom de la base de données DB2. |Oui |
| schema |Nom du schéma dans la base de données. Le nom du schéma respecte la casse. |Non |
| authenticationType |Type d'authentification utilisé pour se connecter à la base de données DB2. Les valeurs possibles sont : Anonyme, De base et Windows. |Oui |
| username |Spécifiez le nom d'utilisateur si vous utilisez l'authentification de base ou Windows. |Non |
| password |Spécifiez le mot de passe du compte d’utilisateur que vous avez spécifié pour le nom d’utilisateur. |Non |
| gatewayName |Nom de la passerelle que le service Data Factory doit utiliser pour se connecter à la base de données DB2 locale. |Oui |


## <a name="dataset-properties"></a>Propriétés du jeu de données
Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md). Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données (SQL Azure, Azure Blob, Azure Table, etc.).

La section typeProperties est différente pour chaque type de jeu de données et fournit des informations sur l'emplacement des données dans le magasin de données. La section typeProperties pour le jeu de données de type RelationalTable (qui inclut le jeu de données DB2) a les propriétés suivantes.

| Propriété | Description | Requis |
| --- | --- | --- |
| TableName |Nom de la table dans l'instance de base de données DB2 à laquelle le service lié fait référence. Le nom de la table respecte la casse. |Non (si la **requête** de **RelationalSource** est spécifiée) |

## <a name="copy-activity-properties"></a>Propriétés de l’activité de copie
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d'entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d'activités.

En revanche, les propriétés disponibles dans la section typeProperties de l’activité varient pour chaque type d'activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Pour une activité de copie, quand la source est de type **RelationalSource** (qui inclut DB2), les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| query |Utilise la requête personnalisée pour lire des données. |Chaîne de requête SQL. Par exemple : `"query": "select * from "MySchema"."MyTable""`. |Non (si **tableName** de **dataset** est spécifiée) |

> [!NOTE]
> Les noms de schéma et de table respectent la casse. Entourez les noms de "" (guillemets doubles) dans la requête.  

**Exemple :**

```sql
"query": "select * from "DB2ADMIN"."Customers""
```


## <a name="json-example-copy-data-from-db2-to-azure-blob"></a>Exemple JSON : copie de données de DB2 vers Azure Blob
Cet exemple présente des exemples de définition JSON, que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Il indique comment copier des données depuis une base de données DB2 et un Stockage Blob Azure. Toutefois, les données peuvent être copiées vers l’un des récepteurs indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats) , via l’activité de copie d’Azure Data Factory.

L’exemple contient les entités de fabrique de données suivantes :

1. Un service lié de type [OnPremisesDb2](data-factory-onprem-db2-connector.md#linked-service-properties).
2. Un service lié de type [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [RelationalTable](data-factory-onprem-db2-connector.md#dataset-properties).
4. Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [RelationalSource](data-factory-onprem-db2-connector.md#copy-activity-properties) et [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'exemple copie toutes les heures les données de résultat d’une requête de base de données DB2 vers un objet Blob Azure. Les propriétés JSON utilisées dans ces exemples sont décrites dans les sections suivant les exemples.

Pour commencer, installez et configurez la passerelle de gestion des données. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service lié DB2 :**

```json
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
```

**Service lié Azure Blob Storage :**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Jeu de données d’entrée DB2 :**

L'exemple suppose que vous avez créé une table « MyTable » dans DB2 et qu'elle contient une colonne appelée « timestamp » pour les données de série chronologique.

La définition de external sur true informe le service Data Factory que le jeu de données est externe à la Data Factory et non produite par une activité dans la Data Factory. Notez que le **type** est défini sur **RelationalTable**.

```json
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
```

**Jeu de données de sortie Azure Blob :**

Les données sont écrites dans un nouvel objet blob toutes les heures (fréquence : heure, intervalle : 1). Le chemin d’accès du dossier pour l’objet blob est évalué dynamiquement en fonction de l’heure de début du segment en cours de traitement. Le chemin d'accès du dossier utilise l'année, le mois, le jour et l'heure de l'heure de début.

```json
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
```

**Pipeline avec activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition du pipeline JSON, le type **source** est défini sur **RelationalSource** et le type **sink** est défini sur **BlobSink**. La requête SQL spécifiée pour la propriété **query** sélectionne les données de la table Orders.

```json
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
                        "query": "select * from \"Orders\""
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
```


## <a name="type-mapping-for-db2"></a>Mappage de type pour DB2
Comme mentionné dans l’article consacré aux [activités de déplacement des données](data-factory-data-movement-activities.md) , l’activité de copie convertit automatiquement des types source en types récepteur à l’aide de l’approche en 2 étapes suivante :

1. Conversion de types natifs source en types .NET
2. Conversion de types .NET en types récepteur natifs

Lors du déplacement de données vers DB2, les mappages suivants sont utilisés pour passer du type DB2 au type .NET.

| Type de base de données DB2 | Type de .NET Framework |
| --- | --- |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Décimal |Décimal |
| DecimalFloat |Décimal |
| Chiffre |Décimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |
| VarChar |String |
| LongVarChar |String |
| DB2DynArray |String |
| Fichier binaire |Byte[] |
| VarBinary |Byte[] |
| LongVarBinary |Byte[] |
| Graphic |String |
| VarGraphic |String |
| LongVarGraphic |String |
| Clob |String |
| Blob |Byte[] |
| DbClob |String |
| SmallInt |Int16 |
| Integer |Int32 |
| BigInt |Int64 |
| Real |Single |
| Double |Double |
| Float |Double |
| Décimal |Décimal |
| DecimalFloat |Décimal |
| Chiffre |Décimal |
| Date |Datetime |
| Time |TimeSpan |
| Timestamp |Datetime |
| xml |Byte[] |
| Char |String |

## <a name="map-source-to-sink-columns"></a>Mapper les colonnes source aux colonnes du récepteur
Pour en savoir plus sur le mappage de colonnes du jeu de données source à des colonnes du jeu de données récepteur, voir [Mappage des colonnes d’un jeu de données dans Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Lecture renouvelée de sources relationnelles
Lorsque vous copiez des données à partir de magasins de données relationnels, gardez à l’esprit la répétabilité de l’opération, afin d’éviter des résultats imprévus. Dans Azure Data Factory, vous pouvez réexécuter une tranche manuellement. Vous pouvez également configurer une stratégie de nouvelles tentatives pour un jeu de données, afin qu’une tranche soit réexécutée en cas de défaillance. Lorsqu’une tranche est réexécutée d’une manière ou d’une autre, vous devez vous assurer que les mêmes données sont lues et ce, quel que soit le nombre d’exécutions de la tranche. Voir [Lecture renouvelée de sources relationnelles](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) dans Azure Data Factory et les différentes manières de les optimiser.

