---
title: Appeler des programmes Spark à partir d’Azure Data Factory
description: Apprenez à appeler des programmes Spark à partir d'une fabrique de données Azure avec l'activité MapReduce.
services: data-factory
documentationcenter: ''
author: spelluru
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: spelluru

---
# Appeler des programmes Spark à partir de Data Factory
## Introduction
Vous pouvez utiliser l'activité MapReduce dans un pipeline Data Factory pour exécuter des programmes Spark sur votre cluster HDInsight Spark. Consultez l’article [Activité MapReduce](data-factory-map-reduce.md) pour plus d'informations sur l'utilisation de l'activité avant de lire cet article.

## Exemple Spark sur GitHub
L’[exemple Spark - Data Factory sur GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) montre comment utiliser l'activité MapReduce pour appeler un programme Spark. Le programme Spark copie simplement les données d'un conteneur d'objets blob Azure vers un autre.

## Entités Data Factory
Le dossier **Spark-ADF/src/ADFJsons** contient des fichiers pour les entités Data Factory (services liés, jeux de données, pipeline).

Il y a deux **services liés** dans cet exemple : Azure Storage et Azure HDInsight. Spécifiez le nom et les valeurs de clés Azure Storage dans **StorageLinkedService.json** et clusterUri, le nom d'utilisateur et le mot de passe dans **HDInsightLinkedService.json**.

Il y a deux **jeux de données** dans cet exemple : **input.json** et **output.json**. Ces fichiers se trouvent dans le dossier **Datasets**. Ces fichiers représentent les jeux de données d'entrée et de sortie de l'activité MapReduce

Vous trouverez des exemples de pipelines dans le dossier **ADFJsons/Pipeline**. Observez un pipeline pour comprendre comment appeler un programme Spark à l’aide de l’activité MapReduce.

L’activité MapReduce est configurée pour appeler **com.adf.sparklauncher.ja** dans le conteneur **adflibs** dans votre stockage Azure (spécifié dans StorageLinkedService.json). Le code source de ce programme se trouve dans le dossier Spark-ADF/src/main/java/com/adf/ et il appelle spark-submit et exécute des tâches Spark.

> [!IMPORTANT]
> Lisez [README.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) pour les informations les plus récentes et d’autres instructions avant d’utiliser l’exemple.
> 
> Utilisez votre propre cluster HDInsight Spark avec cette approche pour appeler des programmes Spark avec l'activité MapReduce. L’utilisation d'un cluster HDInsight à la demande n’est pas prise en charge.
> 
> 

## Voir aussi
* [Activité Hive](data-factory-hive-activity.md)
* [Activité pig](data-factory-pig-activity.md)
* [Activité MapReduce](data-factory-map-reduce.md)
* [Activité de diffusion en continu Hadoop](data-factory-hadoop-streaming-activity.md)
* [Appeler des scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->