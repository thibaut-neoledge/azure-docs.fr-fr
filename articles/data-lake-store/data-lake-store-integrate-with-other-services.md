---
title: Intégration de Data Lake Store à d'autres services Azure | Microsoft Docs
description: Présentation de l'intégration de Data Lake Store à d'autres services Azure
documentationcenter: ''
services: data-lake-store
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Intégration de Data Lake Store à d'autres services Azure
Azure Data Lake Store peut être utilisé conjointement avec d'autres services Azure afin d'augmenter le nombre de scénarios. L'article suivant liste les services auxquels Data Lake Store peut être intégré.

## Utiliser Data Lake Store avec Azure HDInsight
Vous pouvez approvisionner un cluster [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) qui utilise Data Lake Store comme stockage compatible HDFS. Pour cette version, pour les clusters Hadoop et Storm sous Windows et Linux, vous ne pouvez utiliser Data Lake Store que comme stockage supplémentaire. Ces clusters utilisent toujours Azure Storage (WASB) comme stockage par défaut. Toutefois, pour les clusters HBase sous Windows et Linux, vous pouvez utiliser Data Lake Store comme stockage par défaut, comme stockage supplémentaire ou les deux.

Pour savoir comment approvisionner un cluster HDInsight avec Data Lake Store, consultez :

* [Approvisionner un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Approvisionner un cluster HDInsight avec Data Lake Store à l'aide d'Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

**Vous préférez visualiser des vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos expliquant comment utiliser Data Lake Store avec des clusters HDInsight.

* [Créer un cluster HDInsight ayant accès à Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* Une fois le cluster configuré, [accédez aux données dans Data Lake Store à l’aide de scripts Hive et Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)

## Utiliser Data Lake Store avec Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) vous permet de travailler avec le Big Data à l'échelle du cloud. Il approvisionne dynamiquement des ressources et vous permet d'effectuer des analyses sur des téraoctets, voire des exaoctets, de données stockées dans plusieurs sources de données prises en charge, parmi lesquelles Data Lake Store. Data Lake Analytics est spécialement optimisé pour fonctionner avec Azure Data Lake Store, fournissant ainsi le plus haut niveau de performances, de débit et de parallélisation pour vos charges de travail de Big Data.

Pour obtenir des instructions sur l'utilisation de Data Lake Analytics avec Data Lake Store, consultez [Prise en main de Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

**Vous préférez visualiser des vidéos ?** Suivez les liens ci-dessous pour regarder des vidéos expliquant comment utiliser Data Lake Store avec des clusters HDInsight.

* [Connect Azure Data Lake Analytics to Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k) (Connecter Azure Data Lake Analytics à Azure Data Lake Store)
* [Access Azure Data Lake Store via Data Lake Analytics](https://mix.office.com/watch/1n0s45up381a8) (Accéder à Azure Data Lake Store via Data Lake Analytics)

## Utiliser Data Lake Store avec Azure Data Factory
Vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour recevoir des données à partir de tables Azure, de bases de données SQL Azure, d'Azure SQL DataWarehouse, d'objets Blob Azure Storage et de bases de données locales. Jouissant d'un statut de premier ordre dans l'écosystème Azure, Azure Data Factory peut être utilisé pour orchestrer l'ingestion de données de ces sources vers Azure Data Lake Store.

Pour obtenir des instructions sur l'utilisation d'Azure Data Factory avec Data Lake Store, consultez [Déplacer des données vers et depuis Data Lake Store avec Data Factory](../data-factory/data-factory-azure-datalake-connector.md).

**Encore des vidéos !** Consultez [Data Orchestration using Azure Data Factory for Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka) (Orchestration de données à l’aide d’Azure Data Factory pour Azure Data Lake Store).

## Copier des données d’objets blob Azure Storage dans Data Lake Store
Azure Data Lake Store fournit un outil en ligne de commande, AdlCopy, qui vous permet de copier les données d’objets blob Azure Storage vers un compte Data Lake Store. Pour plus d’informations, consultez [Copier des données d’objets blob Azure Storage vers Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md).

## Copier des données entre Azure SQL Database et Data Lake Store
Vous pouvez utiliser Apache Sqoop pour importer et exporter des données entre Azure SQL Database et Data Lake Store. Pour plus d’informations, consultez [Copier des données entre Data Lake Store et une base de données SQL Azure à l’aide de Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

**Regardez cette vidéo** : [Use Apache Sqoop to move data between relational sources and Azure Data Lake Store](https://mix.office.com/watch/1butcdjxmu114) (Utiliser Apache Sqoop pour déplacer des données entre des sources relationnelles et Azure Data Lake Store)

## Utiliser Data Lake Store avec Stream Analytics
Vous pouvez utiliser Data Lake Store en tant que sortie pour stocker les données diffusées en continu à l’aide d’Azure Stream Analytics. Pour plus d’informations, consultez [Diffuser des données à partir d’un objet blob Azure Storage dans Data Lake Store à l’aide d’Azure Stream Analytics](data-lake-store-stream-analytics.md).

## Utiliser Data Lake Store avec Power BI
Vous pouvez utiliser Power BI pour importer des données à partir d’un compte Data Lake Store en vue de les analyser et de les visualiser. Pour plus d’informations, consultez [Analyse des données dans Data Lake Store à l’aide de Power BI](data-lake-store-power-bi.md).

## Utiliser Data Lake Store avec Data Catalog
Vous pouvez inscrire des données issues de Data Lake Store dans Azure Data Catalog pour qu’elles puissent être découvertes à l’échelle de l’organisation. Pour plus d’informations, consultez [Register data from Data Lake Store in Azure Data Catalog](data-lake-store-with-data-catalog.md) (Inscrire des données issues de Data Lake Store dans Azure Data Catalog).

## Voir aussi
* [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
* [Prise en main de Data Lake Store avec le portail](data-lake-store-get-started-portal.md)
* [Prise en main de Data Lake Store avec PowerShell](data-lake-store-get-started-powershell.md)

<!---HONumber=AcomDC_0914_2016-->