---
title: "Déplacement de données à l’aide de l’activité de copie | Microsoft Docs"
description: "Apprenez-en plus sur le déplacement des données dans les pipelines Data Factory : migration de données entre des magasins de cloud, entre des boutiques locales et cloud. Utiliser l’activité de copie."
keywords: "copier des données, déplacement des données, migration des données, transférer des données"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 91a2ac08f6daac8cba195454e09bb07afe265046
ms.lasthandoff: 03/15/2017


---
# <a name="move-data-by-using-copy-activity"></a>Déplacer des données à l’aide de l’activité de copie
## <a name="overview"></a>Vue d'ensemble
Dans Azure Data Factory, vous pouvez utiliser l’activité de copie pour copier les données de différentes formes de diverses sources de données locales et cloud vers Azure. Une fois les données copiées, elles peuvent être davantage transformées et analysées. Vous pouvez également utiliser l’activité de copie pour publier les résultats de transformation et d’analyse pour l’aide à la décision (BI) et l’utilisation d’application.

![Rôle d’activité de copie](media/data-factory-data-movement-activities/copy-activity.png)

L’activité de copie est effectuée par un [service globalement disponible](#global)sécurisé, fiable et évolutif. Cet article fournit des détails sur le déplacement des données dans Data Factory et dans l’activité de copie.

Voyons d’abord comment la migration de données se produit entre deux banques de données cloud, et entre une banque de données locale et une banque de données cloud.

> [!NOTE]
> Pour en savoir plus sur les activités en général, consultez [Présentation des pipelines et des activités](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copie de données entre deux magasins de données cloud
Lorsque les magasins de données source et récepteur se trouvent dans le cloud, l'activité de copie passe par les étapes suivantes pour copier des données de la source vers le récepteur. Le service qui alimente l’activité de copie :

1. Lit les données du magasin de données source.
2. Effectue la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Il effectue ces opérations en se basant sur les configurations du jeu de données d’entrée, du jeu de données de sortie et de l’activité de copie.
3. Écrit les données dans le magasin de données de destination.

Le service choisit automatiquement la région optimale pour effectuer le déplacement des données. Cette région est généralement la plus proche de la banque de données du récepteur.

![Copie cloud-cloud](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copie de données entre un magasin de données local et un magasin de données cloud
Pour déplacer en toute sécurité des données entre des banques de données locales et une banque de données cloud, installez la passerelle de gestion des données sur votre ordinateur local. La passerelle de gestion des données est un agent qui permet le traitement et le déplacement de données hybrides. Vous pouvez l’installer sur la même machine que le magasin de données lui-même ou sur une machine distincte ayant accès au magasin de données.

Dans ce scénario, la passerelle de gestion des données effectue la sérialisation/désérialisation, la compression/décompression, le mappage de colonnes et la conversion de type sont effectués par la passerelle de gestion des données. Les données ne transitent pas via le service Azure Data Factory. Au lieu de cela, la passerelle de gestion des données les écrit directement dans le magasin de destination.

![Copie de type local vers cloud](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Consultez la page [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md) pour une introduction et une procédure pas à pas. Pour obtenir des informations détaillées sur cet agent, consultez [Passerelle de gestion de données](data-factory-data-management-gateway.md) .

Vous pouvez également déplacer des données depuis/vers des magasins de données pris en charge hébergés sur des machines virtuelles IaaS Azure avec la passerelle de gestion des données. Dans ce cas, vous pouvez installer la passerelle de gestion des données sur la même machine virtuelle Azure que le magasin de données lui-même ou sur une machine virtuelle distincte ayant accès au magasin de données.

## <a name="supported-data-stores-and-formats"></a>Banques de données et formats pris en charge
[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

Si vous devez déplacer des données vers ou à partir d’un magasin de données qui n’est pas pris en charge par l’activité de copie, utilisez une **activité personnalisée** dans Data Factory avec votre propre logique de copie/déplacement des données. Pour plus d’informations sur la création et l’utilisation d’une activité personnalisée, consultez [Utilisation des activités personnalisées dans un pipeline Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="supported-file-formats"></a>Formats de fichiers pris en charge
Vous pouvez utiliser l’activité de copie pour copier des **fichiers tels quels** entre deux banques de données basées sur des fichiers comme le Stockage Blob Azure, Azure Data Lake Store, Amazon S3, FTP, un système de fichiers et HDFS. Pour ce faire, vous pouvez ignorer la [section format](data-factory-create-datasets.md) dans les définitions des jeux de données d’entrée et de sortie. Les données sont copiées efficacement, sans sérialisation/désérialisation.

L’activité de copie peut également lire dans et écrire dans des fichiers de formats spécifiés : **texte, Avro, ORC, Parquet et JSON**, et les codecs de compression **GZip, Deflate, BZip2 et ZipDeflate** sont pris en charge. Vous pouvez effectuer les activités de copie suivantes, par exemple :

* Copier des données au format texte compressé GZip (CSV) provenant d’objets blob Azure et les écrire dans une base de données SQL Azure.
* Copier des fichiers au format texte (CSV) provenant d’un système de fichiers local et les écrire dans des objets blob Azure au format Avro.
* Copier les données dans le SQL Server local et les écrire dans Azure Data Lake Store au format ORC.
* Copier les fichiers compressés depuis le système de fichiers local, les décompresser, puis accéder à Azure Data Lake Store.

## <a name="global"></a>Déplacement des données disponible globalement
Azure Data Factory est disponible uniquement dans les régions Europe du Nord, États-Unis de l'Est et États-Unis de l'Ouest. Cependant, le service proposant l’activité de copie est disponible globalement dans les régions et zones géographiques suivantes. La topologie globalement disponible garantit le déplacement efficace des données en évitant généralement les sauts entre régions. Consultez la section [Services par région](https://azure.microsoft.com/regions/#services) pour connaître la disponibilité de Data Factory et du déplacement des données dans une région.

### <a name="copy-data-between-cloud-data-stores"></a>Copier des données entre des banques de données cloud
Lorsque les banques de données source et récepteur résident toutes les deux dans le cloud, Data Factory utilise un déploiement de service dans la région la plus proche du récepteur dans la même zone géographique afin de déplacer des données. Pour connaître le mappage, reportez-vous au tableau suivant :

| Géographie de la banque de données de destination | Région de la banque de données de destination | Région utilisée pour le déplacement des données |
|:--- |:--- |:--- |
| États-Unis | Est des États-Unis | Est des États-Unis |
| . | Est des États-Unis 2 | Est des États-Unis 2 |
| . | Centre des États-Unis | Centre des États-Unis |
| . | États-Unis - partie centrale septentrionale | États-Unis - partie centrale septentrionale |
| . | Centre-Sud des États-Unis | Centre-Sud des États-Unis |
| . | Centre-Ouest des États-Unis | Centre-Ouest des États-Unis |
| . | Ouest des États-Unis | Ouest des États-Unis |
| . | Ouest des États-Unis 2 | Ouest des États-Unis |
| Canada | Est du Canada | Centre du Canada |
| . | Centre du Canada | Centre du Canada |
| Brésil | Sud du Brésil | Sud du Brésil |
| Europe | Europe du Nord | Europe du Nord |
| . | Europe de l'Ouest | Europe de l'Ouest |
| Asie-Pacifique | Asie du Sud-Est | Asie du Sud-Est |
| . | Est de l'Asie | Asie du Sud-Est |
| Australie | Est de l’Australie | Est de l’Australie |
| . | Sud-Est de l’Australie | Sud-Est de l’Australie |
| Japon | Est du Japon | Est du Japon |
| . | Ouest du Japon | Est du Japon |
| Inde | Inde centrale | Inde centrale |
| . | Inde occidentale | Inde centrale |
| . | Inde du Sud | Inde centrale |

Vous pouvez également indiquer explicitement la région du service Data Factory à utiliser pour effectuer la copie en spécifiant la propriété `executionLocation` sous l’activité de copie `typeProperties`. Les valeurs prises en charge pour cette propriété sont énumérées dans la colonne **Région utilisée pour le déplacement des données** ci-dessus. Notez que vos données parcourront cette région sur le câble pendant la copie. Par exemple, pour copier entre les banques Azure au Royaume-Uni, vous pouvez spécifier `"executionLocation": "North Europe"` pour un acheminement via l’Europe du Nord (voir [exemple JSON](#by-using-json-scripts) en tant que référence).

> [!NOTE]
> Si la région de la banque de données de destination ne figure pas dans la liste précédente ou n’est pas détectable, par défaut, l’activité de copie échoue au lieu de passer par une autre région, sauf si `executionLocation` est spécifié. La liste des régions prises en charge sera développée au fil du temps.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copie de données entre un magasin de données local et un magasin de données cloud
Lorsque les données sont copiées entre l’instance locale (ou une machine virtuelle Azure/IaaS) et l’instance cloud, le déplacement des données est effectué par la [passerelle de gestion des données](data-factory-data-management-gateway.md) sur un ordinateur local ou une machine virtuelle IaaS Azure. Les données ne transitent pas via le service dans le cloud, sauf si vous utilisez la fonctionnalité [Copie intermédiaire](data-factory-copy-activity-performance.md#staged-copy) . Dans ce cas, les données sont transmises via le stockage d’objets blob Azure intermédiaire avant d’être écrites dans la banque de données de récepteur.

## <a name="create-a-pipeline-with-copy-activity"></a>Créer un pipeline avec une activité de copie
Vous pouvez créer un pipeline avec une activité de copie de plusieurs façons :

### <a name="by-using-the-copy-wizard"></a>Avec l’assistant de copie
L’Assistant Data Factory Copy vous aide à créer un pipeline avec l’activité de copie. Le pipeline vous permet de créer un pipeline pour copier des données de sources prises en charge vers des destinations *sans avoir à écrire de définitions JSON* pour les services liés, les jeux de données et les pipelines. Reportez-vous à [l’Assistant Data Factory Copy](data-factory-copy-wizard.md) pour connaître les détails de cet Assistant.  

### <a name="by-using-json-scripts"></a>Avec utilisation de scripts JSON
Vous pouvez utiliser Data Factory Editor dans le portail Azure, Visual Studio ou Azure PowerShell pour créer une définition JSON pour un pipeline (en utilisant l’activité de copie). Vous pouvez ensuite le déployer pour créer le pipeline dans Data Factory. Consultez le [Didacticiel : Utilisation de l’activité de copie dans un pipeline Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) pour connaître les instructions des procédures pas à pas.    

Les propriétés JSON (le nom, la description, les tables d'entrée et de sortie et les différentes stratégies) sont disponibles pour tous les types d'activités. Les propriétés qui sont disponibles dans la section `typeProperties` de l’activité varient avec chaque type d’activité.

Pour une activité de copie, la section `typeProperties` varie selon les types de source et de récepteur. Cliquez sur une source/un récepteur dans la section sur les [sources/récepteurs pris en charge](#supported-data-stores-and-formats) pour en savoir plus sur les propriétés de type prises en charge par l’activité de copie de cette banque de données.

Voici un exemple de définition JSON :

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "North Europe"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
La planification définie dans le jeu de données de sortie détermine quand l’activité s’exécute (par exemple **quotidiennement** : fréquence : **jour** et intervalle : **1**). L’activité permet de copier les données d’un jeu de données d’entrée (**source**) vers un jeu de données de sortie (**récepteur**).

Vous pouvez spécifier plus d’un jeu de données d’entrée pour l’activité de copie. Ils sont utilisés pour vérifier les dépendances avant l’exécution de l’activité. Toutefois, seules les données du premier jeu de données sont copiées vers le jeu de données de destination. Pour plus d’informations, consultez [Planification et exécution](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Performances et réglage
Consultez [Guide des performances et de l’optimisation de l’activité de copie](data-factory-copy-activity-performance.md), qui décrit les facteurs clés affectant les performances du déplacement de données dans Azure Data Factory (activité de copie). Il répertorie également les performances observées lors des tests internes, et présente les différentes manières d’optimiser les performances de l’activité de copie.

## <a name="scheduling-and-sequential-copy"></a>Planification et copie séquentielle
Consultez [Planification et exécution](data-factory-scheduling-and-execution.md) pour plus d’informations sur le fonctionnement de la planification et de l’exécution dans Data Factory. Il est possible d’exécuter plusieurs opérations de copie l’une après l’autre, de manière séquentielle/ordonnée. Consultez la section [Copier de manière séquentielle](data-factory-scheduling-and-execution.md#run-activities-in-a-sequence).

## <a name="type-conversions"></a>Conversions des types
Les magasins de données ont différents types de systèmes natifs. L’activité de copie convertit automatiquement des types source en types récepteur selon l’approche en&2; étapes suivante :

1. Conversion de types natifs source en types .NET.
2. Conversion de types .NET en types récepteur natifs.

Le mappage d’un système de type natif donné en .NET pour une banque de données se trouve dans les articles de banque de données respectifs. (Cliquez sur le lien spécifié dans la table [Magasins de données pris en charge](#supported-data-stores) ). Vous pouvez utiliser ces mappages pour déterminer les types appropriés lors de la création de vos tables afin d'exécuter les conversions adaptées lors de l'activité de copie.

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur l’activité de copie, consultez [Copie de données d’Azure Blob Storage vers une base de données SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Consultez [Déplacement de données entre des sources locales et le cloud à l’aide de la passerelle de gestion des données](data-factory-move-data-between-onprem-and-cloud.md)pour en savoir plus sur le déplacement des données depuis une banque de données locale vers une banque de données cloud.

