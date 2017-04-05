---
title: "Appeler des programmes Spark à partir d’Azure Data Factory"
description: "Apprenez à appeler des programmes Spark à partir d&quot;une fabrique de données Azure avec l&quot;activité MapReduce."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Appeler des programmes Spark à partir de Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Activité Hive](data-factory-hive-activity.md) 
> * [Activité pig](data-factory-pig-activity.md)
> * [Activité MapReduce](data-factory-map-reduce.md)
> * [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Activité Spark](data-factory-spark.md)
> * [Activité d’exécution par lot Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Activité des ressources de mise à jour de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Activité de procédure stockée](data-factory-stored-proc-activity.md)
> * [Activité U-SQL Data Lake Analytics](data-factory-usql-activity.md)
> * [Activité personnalisée .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introduction
L’activité Spark HDInsight d’un [pipeline](data-factory-create-pipelines.md) Data Factory exécute des programmes Spark sur [votre propre](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight. Cet article s'appuie sur l'article [Activités de transformation des données](data-factory-data-transformation-activities.md) qui présente une vue d'ensemble de la transformation des données et les activités de transformation prises en charge.

> [!IMPORTANT]
> Si vous débutez avec Azure Data Factory, nous vous recommandons de parcourir le didacticiel [Générer votre premier pipeline](data-factory-build-your-first-pipeline.md) avant de lire cet article. Pour obtenir une vue d’ensemble du service Data Factory, consultez [Présentation d’Azure Data Factory](data-factory-introduction.md). 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Cluster Apache Spark dans Azure HDInsight
Tout d’abord, créez un cluster Apache Spark dans Azure HDInsight en suivant les instructions dans le didacticiel : [Cluster Apache Spark dans Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="create-data-factory"></a>Créer une fabrique de données 
Voici les étapes classiques pour créer un pipeline Data Factory avec une activité Spark.  

1. Créer une fabrique de données.
2. Créez un service lié qui relie le cluster Apache Spark dans Azure HDInsight à votre fabrique de données.
3. Actuellement, vous devez spécifier un jeu de données de sortie d’une activité même si aucune sortie n’est produite. Pour créer un jeu de données d’objets Blob Azure, procédez comme suit :
    1. Créez un service lié qui relie votre compte de stockage Azure à la fabrique de données.
    2. Créez un jeu de données faisant référence au service lié Stockage Azure.  
3. Créer un pipeline avec activité Spark faisant référence au service lié Apache HDInsight créé au point 2. L’activité est configurée avec le jeu de données que vous avez créé à l’étape précédente comme un jeu de données de sortie. Le jeu de données de sortie pilote la planification (horaire, quotidienne, etc.). Par conséquent, vous devez spécifier le jeu de données de sortie même si l’activité ne produit pas vraiment de sortie.

Pour obtenir des instructions détaillées pour créer une fabrique de données, consultez le didacticiel : [Générer votre premier pipeline](data-factory-build-your-first-pipeline.md). Ce didacticiel utilise une activité Hive avec un cluster HDInsight Hadoop, mais les étapes sont similaires pour l’utilisation d’une activité Spark avec un cluster HDInsight Spark.   

Les sections suivantes fournissent des informations sur les entités Data Factory pour utiliser le cluster Apache Spark et l’activité Spark dans votre fabrique de données.   

## <a name="hdinsight-linked-service"></a>Service lié HDInsight
Avant d’utiliser une activité Spark dans un pipeline Data Factory, créez votre propre service lié HDInsight. L’extrait de code JSON suivant illustre la définition d’un service lié HDInsight qui pointe vers un cluster Azure HDInsight Spark.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Actuellement, l’activité Spark ne prend pas en charge les clusters Spark qui utilisent Azure Data Lake Store en tant que service lié HDInsight de stockage principal ou à la demande. 

Pour plus d’informations sur le service lié HDInsight et d’autres services liés de calcul, consultez l’article [Environnements de calcul pris en charge par Azure Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>JSON d’activité Spark
Voici la définition d’exemple JSON d’un pipeline avec activité Spark :    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Notez les points suivants : 
- La propriété du type est définie sur HDInsightSpark. 
- Le chemin racine est défini sur adfspark\\pyFiles où adfspark est le conteneur d’objets Blob Azure et le dossier pyFiles se trouve dans ce conteneur. Dans cet exemple, le stockage Blob Azure est celui qui est associé au cluster Spark. Vous pouvez charger le fichier vers un autre stockage Azure. Si vous procédez ainsi, créez un service lié de stockage Azure pour lier ce compte de stockage à la fabrique de données. Ensuite, spécifiez le nom du service lié en tant que valeur pour la propriété sparkJobLinkedService. Consultez les [propriétés de l’activité Spark](#spark-activity-properties) pour plus d’informations sur cette propriété et d’autres propriétés prises en charge par l’activité Spark.  
- entryFilePath est défini sur test.py, c’est-à-dire le fichier python. 
- Les valeurs des paramètres pour le programme Spark sont transmises à l’aide de la propriété arguments. Dans cet exemple, il existe deux arguments : arg1 et arg2. 
- La propriété getDebugInfo est définie sur Always, ce qui signifie que les fichiers journaux sont toujours générés (succès ou échec). 
- La section sparkConfig spécifie un paramètre d’environnement python : worker.memory. 
- La section des sorties possède un jeu de données de sortie. Vous devez spécifier un jeu de données de sortie même si le programme Spark ne génère aucune sortie. Le jeu de données de sortie pilote la planification du pipeline (horaire, quotidienne, etc.).     

Les propriétés de type (dans la section typeProperties) sont décrites plus loin dans cet article dans la section [Propriétés de l’activité Spark](#spark-activity-properties). 

Comme mentionné précédemment, vous devez spécifier un jeu de données de sortie de l’activité, car c’est ce qui pilote la planification du pipeline (horaire, quotidienne, etc.). Dans cet exemple, un jeu de données Blob Azure est utilisé. Pour créer un jeu de données Blob Azure, vous devez d’abord créer un service lié Azure Storage. 

Voici les exemples de définitions du service lié Azure Storage et le jeu de données d’objets Blob Azure : 

**Service lié Azure Storage :**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Jeu de données d’objet blob Azure :** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Ce jeu de données est un jeu de données factice. Data Factory utilise les paramètres de fréquence et d’intervalle et exécute le pipeline quotidiennement entre les heures de début et de fin d’un pipeline. Dans l’exemple de définition de pipeline, les heures de début et de fin ne sont que d’une seule journée, afin que le pipeline ne s’exécute qu’une seule fois. 

## <a name="spark-activity-properties"></a>Propriétés de l'activité Spark

Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON : 

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| name | Nom de l'activité dans le pipeline. | Oui |
| Description | Texte décrivant l’activité. | Non |
| type | Cette propriété doit être définie sur HDInsightSpark. | Oui |
| linkedServiceName | Nom d’un service lié HDInsight sur lequel s’exécute le programme Spark. | Oui |
| rootPath | Conteneur d’objets blob Azure et dossier contenant le fichier Spark. Le nom de fichier respecte la casse. | Oui |
| entryFilePath | Chemin d’accès relatif au dossier racine du code/package Spark. | Oui |
| className | Classe principale Java/Spark de l’application. | Non | 
| arguments | Liste d’arguments de ligne de commande du programme Spark. | Non | 
| proxyUser | Identité du compte d’utilisateur à emprunter pour exécuter le programme Spark. | Non | 
| sparkConfig | Propriétés de configuration Spark. | Non | 
| getDebugInfo | Spécifie quand les fichiers journaux de Spark sont copiés vers le stockage Azure utilisé par le cluster HDInsight (ou) spécifié par sparkJobLinkedService. Valeurs autorisées : Aucun, Toujours ou Échec. Valeur par défaut : Aucun. | Non | 
| sparkJobLinkedService | Service lié de stockage Azure qui contient le fichier de travail, les dépendances et les journaux Spark.  Si vous ne spécifiez pas de valeur pour cette propriété, le stockage associé au cluster HDInsight est utilisé. | Non |

## <a name="folder-structure"></a>Structure de dossiers
L’activité Spark ne prend pas en charge un script en ligne, contrairement aux activités Pig et Hive. Les travaux Spark sont également plus extensibles que les travaux Pig/Hive. Pour les travaux Spark, vous pouvez fournir plusieurs dépendances, telles que des packages jar (placés dans le CLASSPATH Java), des fichiers Python (placés dans le PYTHONPATH) et tout autre fichier.

Créez la structure de dossiers suivante dans le stockage Blob Azure référencé par le service lié HDInsight. Ensuite, téléchargez les fichiers dépendants dans les sous-dossiers appropriés dans le dossier racine représenté par **entryFilePath**. Par exemple, téléchargez les fichiers Python dans le sous-dossier pyFiles et les fichiers jar dans le sous-dossier jars du dossier racine. Lors de l’exécution, le service Data Factory attend la structure de dossiers suivante dans le stockage Blob Azure :     

| Chemin | Description | Requis | Type |
| ---- | ----------- | -------- | ---- | 
| .    | Chemin d’accès racine du travail Spark dans le service lié de stockage    | Oui | Dossier |
| &lt;défini par l’utilisateur &gt; | Chemin d’accès pointant vers le fichier d’entrée du travail Spark | Oui | Fichier | 
| ./jars | Tous les fichiers dans ce dossier sont téléchargés et placés dans le classpath Java du cluster | Non | Dossier |
| ./pyFiles | Tous les fichiers dans ce dossier sont téléchargés et placés dans le PYTHONPATH du cluster | Non | Dossier |
| ./files | Tous les fichiers dans ce dossier sont téléchargés et placés dans le répertoire de travail de l’exécuteur | Non | Dossier |
| ./archives | Tous les fichiers dans ce dossier ne sont pas compressés | Non | Dossier |
| ./logs | Dossier dans lequel sont stockés les journaux du cluster Spark| Non | Dossier |

Voici un exemple de stockage qui contient deux fichiers de travail Spark dans le stockage Blob Azure référencé par le service lié HDInsight. 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> Pour accéder à la procédure complète de création d’un pipeline avec une activité de transformation, consultez l’article [Créer un pipeline pour transformer des données](data-factory-build-your-first-pipeline-using-editor.md). 



## <a name="see-also"></a>Voir aussi
* [Activité Hive](data-factory-hive-activity.md)
* [Activité pig](data-factory-pig-activity.md)
* [Activité MapReduce](data-factory-map-reduce.md)
* [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
* [Appeler des scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


