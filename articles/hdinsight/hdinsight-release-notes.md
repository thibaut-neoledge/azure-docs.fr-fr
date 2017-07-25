---
title: Notes de publication pour les composants Hadoop sur Azure HDInsight | Microsoft Docs
description: "Dernières notes de publication et versions des composants Hadoop pour Azure HDInsight. Obtenez des conseils et détails concernant le développement pour Spark, R Server, Hive et bien plus."
services: hdinsight
documentationcenter: 
editor: cgronlun
manager: jhubbard
author: nitinme
tags: azure-portal
ms.assetid: a363e5f6-dd75-476a-87fa-46beb480c1fe
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 782cab231e1b152c720abebff1fc76ae0559d12a
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---
# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notes de publication pour les composants Hadoop sur Azure HDInsight

Cet article fournit des informations sur les mises à jour **les plus récentes** des versions d’Azure HDInsight. Pour plus d’informations sur les versions antérieures, voir [Archive des notes de publication de HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour plus d’informations, consultez [l’article sur le contrôle de version de HDInsight](hdinsight-component-versioning.md).

## <a name="04062017---general-availability-of-hdinsight-36"></a>06/04/2017 - Disponibilité générale de HDInsight 3.6

* Avec cette version, Azure HDInsight ajoute la version 3.6, qui est basée sur HDP 2.6. Les notes de publication de HDP 2.6 sont disponibles [ici](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). De plus amples informations sur les versions de HDInsight sont disponibles [ici](hdinsight-component-versioning.md). HDInsight 3.6 est disponible pour les charges de travail suivantes :

    * Hadoop v2.7.3
    * HBase v1.1.2
    * Storm v1.1.0
    * Spark v2.1.0
    * Hive interactif v2.1.0

* **Prise en charge de Hive View 2.0**. Cela doit améliorer l’expérience utilisateur de Hive interactif. Pour plus d’informations, voir la [documentation Hortonworks](http://docs.hortonworks.com/HDPDocuments/Ambari-2.5.0.3/bk_ambari-views/content/ch_using_hive_view.html).

* **Amélioration des performances avec Hive LLAP**. Pour plus d’informations, consultez la [documentation Hortonworks](https://hortonworks.com/blog/top-5-performance-boosters-with-apache-hive-llap/).

* **Nouvelles fonctionnalités Hive** Pour plus d’informations, consultez la [documentation Hortonworks](https://hortonworks.com/apache/hive/#section_4).

* **Obsolescence de l’interface CLI Hive** : cette interface est déconseillée et les clients sont encouragés à utiliser Beeline à la place. Pour plus d’informations, consultez la [documentation Apache](https://cwiki.apache.org/confluence/display/Hive/Replacing+the+Implementation+of+Hive+CLI+Using+Beeline). Pour obtenir des instructions sur l’utilisation de Beeline avec HDInsight, consultez [Utiliser Beeline avec des clusters Hadoop HDInsight](hdinsight-hadoop-use-hive-beeline.md).

* **Nouvelles fonctionnalités Apache Phoenix et HBase**.
    * Prise en charge du quota de stockage : couramment utilisée dans des environnements mutualisés, cette option permet d’avoir un espace de stockage limité par table et niveau d’espace de noms.
    * Améliorations apportées à l’indexation Phoenix : création d’index incrémentiel et régénération/reprise de l’indexation à partir d’échecs précédents.
    * Outil d’intégrité des données Phoenix : prend en charge la validation de schémas, d’index et d’autres métadonnées.


* **Problème avec HBase**: lors de l’exécution d’un travail MapReduce de chargement en masse CSV, la syntaxe suivante peut provoquer une erreur.

        HADOOP_CLASSPATH=$(hbase mapredcp):/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv

    Utilisez plutôt la syntaxe suivante :

        HADOOP_CLASSPATH=/path/to/hbase-protocol.jar:/path/to/hbase/conf hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table EXAMPLE --input /data/example.csv


## <a name="02282017---release-of-spark-21-on-hdinsight-36-preview"></a>Version du 28/02/2017 de Spark 2.1 sur HDInsight 3.6 (préversion)
* [Spark 2.1](http://spark.apache.org/releases/spark-release-2-1-0.html) améliore les nombreux problèmes de stabilité et de convivialité des versions précédentes. Il apporte également de nouvelles fonctionnalités pour toutes les charges de travail Spark telles que Spark Core, SQL, ML et Streaming.
* La fonctionnalité Structured Streaming bénéficie d’une meilleure évolutivité avec prise en charge des filigranes de temps d’événement et du connecteur Kafka 0.10.
* Le partitionnement Spark SQL est désormais géré à l’aide d’un nouveau mécanisme de gestion de partition évolutive. Vous trouverez [ici](http://spark.apache.org/releases/spark-release-2-1-0.html) plus de détails sur la mise à niveau.
* Actuellement, Spark 2.1 sur Azure HDInsight 3.6 version préliminaire ne prend pas en charge la connectivité des outils décisionnels à l’aide du pilote ODBC.
* L’accès à Azure Data Lake Store à partir de clusters Spark 2.1 n’est pas pris en charge dans cette version préliminaire.


## <a name="11182016---release-of-spark-201-on-hdinsight-35"></a>Version du 18/11/2016 de Spark 2.0.1 sur HDinsight 3.5
Spark 2.0.1 est désormais disponible sur les clusters Spark (HDInsight version 3.5).

## <a name="11162016---release-of-r-server-90-on-hdinsight-35-spark-20"></a>Version du 16/11/2016 de R Server 9.0 sur HDInsight 3.5 (Spark 2.0)
*   Les clusters R Server incluent désormais un choix entre deux versions : R Server 9.0 sur HDI 3.5 (Spark 2.0) et R Server 8.0 sur HDI 3.4 (Spark 1.6).
*   R Server 9.0 sur HDI 3.5 (Spark 2.0) repose sur R 3.3.2 et inclut les nouvelles fonctions de source de données ScaleR appelées RxHiveData et RxParquetData permettant de charger des données à partir de Hive et de Parquet directement dans Spark DataFrames, pour que ScaleR les analyse. Pour plus d’informations, voir l’aide en ligne sur ces fonctions dans R à l’aide des commandes **?RxHiveData** et **?RxParquetData**.
*   L’édition communautaire de RStudio Server est désormais installée par défaut (avec une option de désistement) dans le panneau Configuration de cluster dans le cadre du flux d’approvisionnement.

## <a name="11092016---release-of-spark-20-on-hdinsight"></a>Version du 09/11/2016 de Spark 2.0 sur HDinsight
* Les clusters Spark 2.0 sur HDInsight 3.5 prennent désormais en charge les services Livy et Jupyter.

## <a name="10262016---release-of-r-server-on-hdinsight"></a>26/10/2016 - Version de R Server sur HDInsight
* L’URI d’accès au nœud de périmètre a été modifiée en **clustername**-ed-ssh.azurehdinsight.net
* L’approvisionnement du cluster R Server sur HDInsight a été simplifié.
* R Server sur HDInsight est désormais disponible en tant que type de cluster R Server HDInsight standard, et n’est plus installé comme une application HDInsight distincte. Le nœud de périphérie et les fichiers binaires R Server sont désormais approvisionnés dans le cadre du déploiement du cluster R Server. Cela améliore la vitesse et la fiabilité de l’approvisionnement. Le modèle de tarification de R Server est mis à jour en conséquence.
* Le prix du type de cluster R Server est désormais basé sur le prix de niveau Standard, plus le coût supplémentaire R Server. Le niveau Premium est désormais réservé aux fonctionnalités Premium disponibles sur différents types de clusters, et n’est pas utilisé pour le type de cluster R Server. Cette modification n’affecte pas la tarification effective de R Server. Elle modifie uniquement la façon dont les frais sont présentés sur la facture. Tous les clusters R Server existants continuent de fonctionner, ainsi que les modèles Resource Manager jusqu’à avis de désapprobation. **Il est cependant recommandé de mettre à jour vos déploiements par script afin d’utiliser le nouveau modèle Resource Manager.**

## <a name="08302016---release-of-r-server-on-hdinsight"></a>30/08/2016 - Version de R Server sur HDInsight
Numéros de version complets des clusters HDInsight sous Linux déployés avec cette version :

| HDI | Version de cluster HDI | HDP | Build HDP | Build d’Ambari |
| --- | --- | --- | --- | --- |
| 3.2 |3.2.1000.0.8268980 |2.2 |2.2.9.1-19 |2.2.1.12-4 |
| 3.3 |3.3.1000.0.8268980 |2.3 |2.3.3.1-25 |2.2.1.12-4 |
| 3.4 |3.4.1000.0.8269383 |2.4 |2.4.2.4-5 |2.2.1.12-4 |

Numéros de version complets des clusters HDInsight sous Windows déployés avec cette version :

| HDI | Version de cluster HDI | HDP | Build HDP |
| --- | --- | --- | --- |
| 2.1 |2.1.10.1033.2559206 |1.3 |1.3.12.0-01795 |
| 3.0 |3.0.6.1033.2559206 |2.0 |2.0.13.0-2117 |
| 3.1 |3.1.4.1033.2559206 |2.1 |2.1.16.0-2374 |
| 3.2 |3.2.7.1033.2559206 |2.2 |2.2.9.1-11 |
| 3.3 |3.3.0.1033.2559206 |2.3 |2.3.3.1-25 |






