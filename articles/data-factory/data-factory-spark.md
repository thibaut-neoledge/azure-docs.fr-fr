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
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8be5e1525a7c481de5cb02edd26da305af2d4798
ms.lasthandoff: 03/18/2017


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

## <a name="hdinsight-linked-service"></a>Service lié HDInsight
Avant d’utiliser une activité Spark dans un pipeline Data Factory, créez votre propre service lié HDInsight. L’extrait de code JSON suivant illustre la définition d’un service lié HDInsight pointant vers votre propre cluster Azure HDInsight Spark.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> Actuellement, l’activité Spark ne prend pas en charge les clusters Spark qui utilisent Data Lake Store en tant que service lié HDInsight de stockage principal ou à la demande. 

Pour plus d’informations sur le service lié HDInsight et d’autres services liés de calcul, consultez l’article [Environnements de calcul pris en charge par Azure Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>JSON d’activité Spark
Voici la définition d’exemple JSON d’une activité Spark :    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
Le tableau suivant décrit les propriétés JSON utilisées dans la définition JSON : 

| Propriété | Description | Requis |
| -------- | ----------- | -------- |
| name | Nom de l’activité dans le pipeline. | Oui |
| Description | Texte décrivant l’activité. | Non |
| type | Cette propriété doit être définie sur HDInsightSpark. | Oui |
| linkedServiceName | Référence à un service lié HDInsight sur lequel s’exécute le programme Spark. | Oui |
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


