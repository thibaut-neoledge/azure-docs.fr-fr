---
title: "Utilisation de Hive interactif dans HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser Hive interactif (Hive sur LLAP) dans HDInsight."
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 72206e299c245187731e4397610bbcfb01f5311c
ms.lasthandoff: 04/18/2017


---
# <a name="use-interactive-hive-in-hdinsight-preview"></a>Utilisation de Hive interactif dans HDInsight (version préliminaire)
Hive interactif (également appelé [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) est un nouveau [type de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) HDInsight.  Hive interactif permet la mise en mémoire cache ce qui rend les requêtes Hive beaucoup plus interactives et rapides. Cette nouvelle fonctionnalité fait de HDInsight l’une des solutions de Big Data dans le cloud les plus performantes, flexibles et ouvertes, avec des caches in-memory (utilisant Hive et Spark), ainsi que des services d’analyse avancée via une intégration étroite avec R Services. 

Le cluster Hive interactif est différent du cluster Hadoop. Il contient uniquement le service Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie et d’autres services seront prochainement supprimés de ce type de cluster.
> Le service Hive du cluster Hive interactif est uniquement accessible via la vue Ambari Hive, Beeline et ODBC Hive. Il n’est pas accessible via la console Hive, Templeton, l’interface de ligne de commande Azure et Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Création d’un cluster Hive interactif
Le cluster Hive interactif est uniquement pris en charge sur les clusters basés sur Linux. Pour des informations sur la création de clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-from-interactive-hive"></a>Exécution de Hive à partir de Hive interactif
Il existe différentes options vous permettant d’exécuter des requêtes Hive :

* Exécution de Hive à l’aide de la vue Ambari Hive
  
    Pour des informations sur l’utilisation de la vue Hive, consultez [Utiliser la vue Hive avec Hadoop dans HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Exécution de Hive à l’aide de Beeline
  
    Pour des informations sur l’utilisation de Beeline sur HDInsight, consultez [Utiliser Hive avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Vous utilisez Beeline à partir du nœud principal ou d’un nœud de périmètre vide.  L’utilisation de Beeline à partir d’un nœud de périmètre vide est recommandée.  Pour des informations sur la création d’un cluster HDInsight avec un nœud de périmètre vide, consultez [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md).
* Exécution de Hive à l’aide d’ODBC Hive
  
    Pour des informations sur l’utilisation de Hive ODBC, consultez la page [Connexion d’Excel à Hadoop avec le pilote ODBC Hive de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).

**Pour rechercher la chaîne de connexion JDBC :**

1. Connectez-vous à Ambari à l’aide de l’URL suivante : https://<ClusterName>.AzureHDInsight.net.
2. Cliquez sur **Hive** dans le menu de gauche.
3. Cliquez sur l’icône en surbrillance pour copier l’URL :
   
   ![HDInsight Hadoop Hive interactif LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Voir aussi
* [Créer des clusters Hadoop dans HDInsight basés sur Linux](hdinsight-hadoop-provision-linux-clusters.md) : apprenez à créer des clusters Hive interactif dans HDInsight.
* [Utilisation de Hive avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md) : apprenez à utiliser Beeline pour envoyer des requêtes Hive.
* [Connexion d’Excel à Hadoop avec le pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md) : apprenez à connecter Excel à Hive.


