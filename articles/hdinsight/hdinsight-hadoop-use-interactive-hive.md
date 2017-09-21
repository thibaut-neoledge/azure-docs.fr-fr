---
title: Utilisation de Hive interactif avec Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Hive interactif (Hive LLAP) avec HDInsight."
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
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 190ca4b228434a7d1b30348011c39a979c22edbd
ms.openlocfilehash: 6c4e3d0e97eb9ad4500d684c7da3b19c5669994f
ms.contentlocale: fr-fr
ms.lasthandoff: 09/09/2017

---
# <a name="use-interactive-hive-with-hdinsight-preview"></a>Utilisation de Hive interactif avec HDInsight (préversion)
Hive interactif (également appelé Hive LLAP ou [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) est un nouveau [type de cluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. Hive interactif prend en charge la mise en mémoire cache, ce qui accélère les requêtes Hive et les rend beaucoup plus interactives. 

Les clusters Hive interactifs sont différents des clusters Hadoop. Ils contiennent uniquement le service Hive. 

> [!NOTE]
> MapReduce, Pig, Sqoop, Oozie et d’autres services seront prochainement supprimés de ce type de cluster.
> Vous pouvez accéder au service Hive dans le cluster Hive interactif uniquement à travers l’affichage Ambari Hive, Beeline et le pilote Open Database Connectivity Microsoft Hive (ODBC Hive). Vous ne pouvez pas y accéder à travers la console Hive, Templeton, l’outil en ligne de commande Azure (Azure CLI) ni Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-hive-cluster"></a>Création d’un cluster Hive interactif
Vous pouvez uniquement utiliser des clusters Hive interactifs sur des clusters basés sur Linux. Pour plus d’informations sur la création de clusters HDInsight, consultez [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="execute-hive-queries-from-interactive-hive"></a>Exécution de requêtes Hive à partir de Hive interactif
Pour exécuter des requêtes Hive, vous disposez des options suivantes :

* Exécuter Hive à l’aide de l’affichage Ambari Hive.
  
    Pour plus informations sur l’utilisation de l’affichage Hive, consultez [Utiliser la vue Hive avec Hadoop dans HDInsight](hdinsight-hadoop-use-hive-ambari-view.md).
* Exécuter Hive à l’aide de Beeline.
  
    Pour plus informations sur l’utilisation de Beeline sur HDInsight, consultez [Utiliser Hive avec Hadoop dans HDInsight avec Beeline](hdinsight-hadoop-use-hive-beeline.md).
  
    Vous pouvez utiliser Beeline à partir du nœud principal ou d’un nœud de périphérie vide. L’utilisation de Beeline à partir d’un nœud de périphérie vide est recommandée. Pour plus d’informations sur la création d’un cluster HDInsight avec un nœud de périphérie vide, consultez [Utiliser des nœuds de périphérie vides dans HDInsight](hdinsight-apps-use-edge-node.md).
* Exécuter Hive à l’aide d’ODBC Hive.
  
    Pour plus d’informations sur l’utilisation d’ODBC Hive, consultez [Connecter Excel à Hadoop avec le pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).

Pour rechercher la chaîne de connexion Java Database Connectivity (JDBC) :

1. Connectez-vous à Ambari à travers l’URL suivante : https://\<nom du cluster\>.AzureHDInsight.net.
2. Dans le menu de gauche, sélectionnez **Hive**.
3. Pour copier l’URL, sélectionnez l’icône du Presse-papiers :
   
   ![HDInsight Hadoop Hive interactif LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [créer des clusters Hive interactifs dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
* Découvrez comment [utiliser Beeline pour envoyer des requêtes Hive dans HDInsight](hdinsight-hadoop-use-hive-beeline.md).
* Découvrez comment [connecter Excel à Hadoop avec le pilote ODBC Microsoft Hive](hdinsight-connect-excel-hive-odbc-driver.md).


