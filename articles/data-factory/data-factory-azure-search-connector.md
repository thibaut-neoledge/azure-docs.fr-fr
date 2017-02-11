---
title: "Envoyer des données à l’index de recherche à l’aide de Data Factory | Microsoft Docs"
description: "Découvrez comment envoyer des données à l’index Recherche Azure à l’aide d’Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 55c988bf74ff0f2e519e895a735dc68f3dc99855
ms.openlocfilehash: e2deed13106db9467eef181f25a0a226034df5a2

---

# <a name="push-data-to-an-azure-search-index-by-using-azure-data-factory"></a>Envoyer des données à un index Recherche Azure à l’aide d’Azure Data Factory
Cet article décrit l’utilisation de l’activité de copie pour envoyer des données d’un magasin de données local pris en charge par le service Data Factory à l’index Recherche Azure. Les magasins de données sources pris en charge sont répertoriés dans la colonne Source du tableau des [sources et récepteurs pris en charge](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Cet article s’appuie sur l’article des [activités de déplacement des données](data-factory-data-movement-activities.md) qui présente une vue d’ensemble du déplacement des données avec l’activité de copie et les combinaisons de magasins de données prises en charge.

## <a name="enabling-connectivity"></a>Activation de la connectivité
Pour permettre au service Data Factory de se connecter à un magasin de données local, vous devez installer la passerelle de gestion des données dans votre environnement local. Vous pouvez l’installer sur l’ordinateur qui héberge le magasin de données sources ou sur un autre ordinateur afin d’éviter toute mise en concurrence des ressources avec le magasin de données.

La passerelle de gestion des données connecte des sources de données locales à des services cloud de manière gérée et sécurisée. Pour plus d’informations sur la passerelle de gestion de données, consultez l’article [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) .

## <a name="copy-data-wizard"></a>Assistant Copier des données
Le moyen le plus simple de créer un pipeline qui copie des données vers Recherche Azure à partir d’un magasin de données sources pris en charge consiste à utiliser l’Assistant Copier des données. Pour obtenir une procédure rapide, consultez l’article [Didacticiel : Créer un pipeline avec l’activité de copie à l’aide de l’Assistant Data Factory Copy](data-factory-copy-data-wizard-tutorial.md).

L’exemple suivant présente des exemples de définitions de JSON que vous pouvez utiliser pour créer un pipeline à l’aide du [portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), de [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [d’Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Ils indiquent comment copier des données depuis un serveur SQL Server local vers un index Recherche Azure. Toutefois, vous pouvez copier des données à partir des magasins de données locaux indiqués [ici](data-factory-data-movement-activities.md#supported-data-stores-and-formats), par le biais de l’activité de copie d’Azure Data Factory.   

## <a name="sample-copy-data-from-on-premises-sql-server-to-azure-search-index"></a>Exemple : copier des données depuis un serveur SQL Server local vers un index Recherche Azure

L’exemple suivant montre :

1.    Un service lié de type [AzureSearch](#azure-search-linked-service-properties).
2.    Service lié de type [OnPremisesSqlServer](data-factory-sqlserver-connector.md#sql-server-linked-service-properties).
3.    Un [jeu de données](data-factory-create-datasets.md) d’entrée de type [SqlServerTable](data-factory-sqlserver-connector.md#sql-server-dataset-type-properties).
4.    Un [jeu de données](data-factory-create-datasets.md) de sortie de type [AzureSearchIndex](#azure-search-index-dataset-properties).
4.    Un [pipeline](data-factory-create-pipelines.md) avec une activité de copie qui utilise [SqlSource](data-factory-sqlserver-connector.md#sql-server-copy-activity-type-properties) et [AzureSearchIndexSink](#azure-search-index-sink-properties).

L’exemple copie toutes les heures les données de série chronologique d’une base de données SQL Server locale vers un index Recherche Azure. Les propriétés JSON utilisées dans cet exemple sont décrites dans les sections suivant les exemples.

Dans un premier temps, configurez la passerelle de gestion des données sur votre ordinateur local. Les instructions se trouvent dans l’article [Déplacement de données entre des emplacements locaux et le cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Service lié Recherche Azure :**

```JSON
{
    "name": "AzureSearchLinkedService",
       "properties": {
        "type": "AzureSearch",
           "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
       }
}
```

**Service SQL Server lié**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**Jeu de données d’entrée de SQL Server**

L’exemple suppose que vous avez créé une table « MyTable » dans SQL Server et qu’elle contient une colonne appelée « timestampcolumn » pour les données de série chronologique. Vous pouvez interroger plusieurs tables au sein d’une même base de données en utilisant un jeu de données unique, mais une seule table doit être utilisée pour la propriété typeProperty tableName du jeu de données.

La définition de external sur true informe le service Data Factory que le jeu de données est externe à la Data Factory et non produite par une activité dans la Data Factory.

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
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
```

**Jeu de données de sortie Recherche Azure :**

L’exemple copie des données vers un index Recherche Azure nommé **produits**. Data Factory ne crée pas l’index. Pour tester l’exemple, créez un index portant ce nom. Créez l’index Recherche Azure avec le même nombre de colonnes que dans le jeu de données d’entrée. De nouvelles entrées sont ajoutées toutes les heures à l’index Recherche Azure.

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
         "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
   }
}
```

**Pipeline avec une activité de copie :**

Le pipeline contient une activité de copie qui est configurée pour utiliser les jeux de données d'entrée et de sortie, et qui est planifiée pour s'exécuter toutes les heures. Dans la définition JSON du pipeline, le type **source** est défini sur **SqlSource** et le type **sink** est défini sur **AzureSearchIndexSink**. La requête SQL spécifiée pour la propriété **SqlReaderQuery** sélectionne les données de la dernière heure à copier.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
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
```

Si vous copiez des données d’un magasin de données cloud vers Recherche Azure, la propriété `executionLocation` est requise. Ci-dessous figure un exemple de modification nécessaire dans l’activité de copie `typeProperties`. Consultez la section [Copier des données entre des banques de données cloud](data-factory-data-movement-activities.md#global) pour plus d’informations et les valeurs prises en charge.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="azure-search-linked-service-properties"></a>Propriétés du service lié Recherche Azure

Le tableau suivant décrit les éléments JSON propres au service lié Recherche Azure.

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété de type doit être définie sur **AzureSearch**. | Oui |
| URL | URL du service Recherche Azure. | Oui |
| key | Clé d’administration du service Recherche Azure. | Oui |

## <a name="azure-search-index-dataset-properties"></a>Propriétés des jeux de données de l’index Recherche Azure

Pour obtenir une liste complète des sections et propriétés disponibles pour la définition de jeux de données, consultez l’article [Création de jeux de données](data-factory-create-datasets.md) . Les sections comme la structure, la disponibilité et la stratégie d'un jeu de données JSON sont similaires pour tous les types de jeux de données. La section **typeProperties** est différente pour chaque type de jeu de données. La section typeProperties d’un jeu de données de type **AzureSearchIndex** comprend les propriétés suivantes :

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| type | La propriété de type doit être définie sur **AzureSearchIndex**.| Oui |
| indexName | Nom de l’index Recherche Azure. Data Factory ne crée pas l’index. L’index doit exister dans Recherche Azure. | Oui |


## <a name="azure-search-index-sink-properties"></a>Propriétés des récepteurs de l’index Recherche Azure
Pour obtenir la liste complète des sections et des propriétés disponibles pour la définition des activités, consultez l’article [Création de pipelines](data-factory-create-pipelines.md). Les propriétés comme le nom, la description, les tables d’entrée et de sortie et les différentes stratégies sont disponibles pour tous les types d’activité. En revanche, les propriétés qui sont disponibles dans la section typeProperties de l’activité varient pour chaque type d’activité. Pour l’activité de copie, elles dépendent des types de sources et récepteurs.

Pour l’activité de copie, quand la source est de type **AzureSearchIndexSink**, les propriétés suivantes sont disponibles dans la section typeProperties :

| Propriété | Description | Valeurs autorisées | Requis |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Indique s’il convient de procéder à une fusion ou à un remplacement lorsqu’un document existe déjà dans l’index. Voir la [propriété WriteBehavior](#writebehavior-property).| Merge (par défaut)<br/>Télécharger| Non |
| writeBatchSize | Charge des données dans l’index Recherche Azure lorsque la taille du tampon atteint writeBatchSize. Pour plus d’informations, voir la [propriété WriteBatchSize](#writebatchsize-property). | 1 à 1 000. Valeur par défaut : 1 000. | Non |

### <a name="writebehavior-property"></a>Propriété WriteBehavior
AzureSearchSink effectue une opération d’upsert lors de l’écriture des données. En d’autres termes, lorsque vous écrivez un document, si la clé du document existe déjà dans l’index Recherche Azure, Recherche Azure met à jour le document existant au lieu de lever une exception de conflit.

AzureSearchSink fournit les deux comportements upsert suivants (en utilisant le Kit de développement logiciel (SDK) AzureSearch) :

- **Fusion** : combine toutes les colonnes du nouveau document avec celles du document existant. Pour les colonnes ayant une valeur Null dans le nouveau document, la valeur de celles du document existant est conservée.
- **Chargement **: le nouveau document remplace l’ancien. Pour les colonnes qui ne sont pas spécifiées dans le nouveau document, la valeur est définie sur Null qu’il existe ou non une valeur autre que Null dans le document existant.

**Fusion** est le comportement par défaut.

### <a name="writebatchsize-property"></a>Propriété WriteBatchSize
Le service Recherche Azure prend en charge l’écriture de documents sous forme d’un lot. Un lot peut contenir 1 à 1 000 actions. Une action gère un document pour effectuer l’opération de chargement/fusion.

### <a name="data-type-support"></a>Prise en charge des types de données
Le tableau suivant indique si un type de données Recherche Azure est pris en charge ou non.

| Type de données Recherche Azure | Pris en charge dans le récepteur de l’index Recherche Azure |
| ---------------------- | ------------------------------ |
| String | O |
| Int32 | O |
| Int64 | O |
| Double | O |
| Booléen | O |
| DataTimeOffset | O |
| Tableau de chaînes | N |
| GeographyPoint | N |

## <a name="copy-from-a-cloud-source"></a>Copier à partir d’une source cloud
Si vous copiez des données d’un magasin de données cloud vers Recherche Azure, la propriété `executionLocation` est requise. Ci-dessous figure un exemple de modification nécessaire dans l’activité de copie `typeProperties`. Consultez la section [Copier des données entre des banques de données cloud](data-factory-data-movement-activities.md#global) pour plus d’informations et les valeurs prises en charge.

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Spécification de la définition de la structure des jeux de données rectangulaires
La section structure dans les jeux de données JSON est une section **facultative** pour les tables rectangulaires (avec des lignes et colonnes) et contient une collection de colonnes de la table. Utilisez la section structure pour indiquer des informations de type pour les conversions de type ou pour effectuer des mappages de colonnes. Les sections suivantes décrivent ces fonctionnalités en détail.

Chaque colonne contient les propriétés suivantes :

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| name | Nom de la colonne. | Oui |
| type | Type de données de la colonne. Pour plus d’informations sur le moment où vous devez spécifier les informations de type, consultez la section des [conversions de type](#type-conversion-sample). | Non |
| culture | Culture .NET à utiliser lorsque le type est spécifié et qu’il est de type .NET `Datetime` ou `Datetimeoffset`. La valeur par défaut est `en-us`.  | Non |
| format | Chaîne de format à utiliser lorsque le type est spécifié et qu’il est de type .NET `Datetime` ou `Datetimeoffset`. | Non |

L’exemple suivant présente la section JSON structure d’une table qui comporte trois colonnes : `userid`, `name` et `lastlogindate`.

```JSON
"structure":
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```
Suivez les recommandations ci-dessous pour savoir quand inclure les informations de « structure » et pour connaître les éléments à inclure dans la section **structure**.

- **Pour les sources de données structurées** qui stockent le schéma de données et les informations de type, ainsi que les données proprement dites (exemple : table Azure, SQL Server, Oracle, etc.) : spécifiez la section « structure » uniquement si vous mappez des colonnes sources spécifiques aux colonnes spécifiques du récepteur et si leurs noms ne sont pas identiques. Pour plus d’informations, consultez la section sur le mappage de colonnes.

    Comme mentionné plus haut, les informations de type sont facultatives dans la section « structure ». Pour les sources structurées, les informations de type sont disponibles dans le cadre de la définition du jeu de données dans le magasin de données. Vous ne devez donc pas les inclure lorsque vous incluez la section « structure ».
- **Pour un schéma des sources de données de lecture (en particulier les objets blob Azure)** : vous pouvez choisir de stocker des données sans les informations de type ou de schéma. Pour ces types de source de données, vous devez inclure « structure » dans les deux cas suivants :
    - Vous mappez les colonnes sources aux colonnes du récepteur.
    - Lorsque le jeu de données est une source d’une activité de copie, vous pouvez fournir des informations de type dans la « structure ». Data Factory utilise ces informations de type pour la conversion en types natifs du récepteur. Pour plus d’informations, consultez l’article [Déplacer des données vers et depuis un stockage d’objets blob Azure](data-factory-azure-blob-connector.md).

### <a name="supported-net-based-types"></a>Types .NET pris en charge
Data Factory prend en charge les valeurs de type .NET conformes CLS suivantes pour fournir des informations de type dans la section « structure » du schéma dans les sources de données de lecture telles qu’un objet blob Azure.

- Int16
- Int32
- Int64
- Single
- Double
- Décimal
- Bool
- String
- Datetime
- Datetimeoffset
- Timespan

Pour Datetime et Datetimeoffset, vous pouvez également spécifier une chaîne « culture » et « format » pour faciliter l’analyse de votre chaîne Datetime personnalisée. Consultez l’exemple de conversion de type dans la section suivante :


[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


## <a name="performance-and-tuning"></a>Performances et réglage  
Consultez l’article [Guide sur les performances et le réglage de l’activité de copie](data-factory-copy-activity-performance.md) pour en savoir plus sur les facteurs clés affectant les performances de déplacement des données (activité de copie) et les différentes manières de les optimiser.

## <a name="next-steps"></a>Étapes suivantes
Consultez les articles suivants :

* [Didacticiel de l’activité de copie](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour obtenir des instructions détaillées sur la création d’un pipeline avec Activité de copie.



<!--HONumber=Feb17_HO2-->


