---
title: "Résolution de problèmes HIVE - Azure HDInsight | Microsoft Docs"
description: "Utilisez le FAQ Hive pour obtenir des réponses aux questions courantes sur l’utilisation de Hive sur la plateforme Azure HDInsight."
keywords: "Azure HDInsight, Hive, FAQ, guide de dépannage, questions courantes"
services: Azure HDInsight
documentationcenter: na
author: dharmeshkakadia
manager: 
editor: 
ms.assetid: 15B8D0F3-F2D3-4746-BDCB-C72944AA9252
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/7/2017
ms.author: dharmeshkakadia
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 67b096c0585f7b73a57b784683944052d1cfaa33
ms.contentlocale: fr-fr
ms.lasthandoff: 07/11/2017


---

# <a name="hive-troubleshooting"></a>Résolution de problèmes HIVE

Cet article décrit les principaux problèmes rencontrés lors de l’utilisation de charges utiles HIVE dans Apache Ambari, et leur résolution.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Comment exporter un metastore Hive et l’importer dans un autre cluster ?

### <a name="issue"></a>Problème :

Vous avez besoin d’exporter un metastore Hive et de l’importer dans un autre cluster HDInsight.  

### <a name="resolution-steps"></a>Étapes de résolution : 

1. Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH) (consultez la section Pour aller plus loin ci-dessous).
1. Exécutez la commande suivante sur le cluster HDInsight à partir duquel vous souhaitez exporter le metastore :

```apache
for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
```

Cela génère un fichier nommé `allatables.sql`.

- Copiez le fichier `alltables.sql` dans le nouveau cluster HDInsight et exécutez la commande suivante :

```apache
hive -f alltables.sql
```

Ce code part du principe que les chemins de données sont les mêmes sur le nouveau cluster que sur l’ancien. Si ce n’est pas le cas, vous pouvez modifier manuellement le fichier  
`alltables.sql` générer pour qu’il reflète les modifications éventuelles.

### <a name="further-reading"></a>Pour aller plus loin :

- [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)


## <a name="how-do-locate-hive-logs-on-a-cluster"></a>Comment localiser les journaux Hive sur un cluster ?

### <a name="issue"></a>Problème :

Vous avez besoin de localiser les journaux du client Hive, du metastore et du serveur Hive sur le cluster HDInsight.  

### <a name="resolution-steps"></a>Étapes de résolution : 

- Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH) (consultez la section Pour aller plus loin ci-dessous).
- Vous trouverez les journaux du client Hive à l’emplacement :

```apache
/tmp/<username>/hive.log 
```

- Les journaux du metastore Hive se trouvent à cet emplacement :

```apache
/var/log/hive/hivemetastore.log 
```

- Les journaux du serveur Hive se trouvent quant à eux à l’emplacement :

```apache
/var/log/hive/hiveserver2.log 
```

### <a name="further-reading"></a>Pour aller plus loin :

- [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)

## <a name="how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster"></a>Comment lancer un interpréteur de commandes Hive avec des configurations spécifiques sur un cluster ?

### <a name="issue"></a>Problème :

Vous avez besoin de remplacer ou de spécifier des configurations d’interpréteur de commandes Hive au moment du lancement sur les clusters HDInsight.  

### <a name="resolution-steps"></a>Étapes de résolution : 

- Spécifiez une paire clé-valeur de configuration pour le démarrage de l’interpréteur de commandes Hive (consultez la section Pour aller plus loin ci-dessous) :

```apache
hive -hiveconf a=b 
```

- Répertoriez toutes les configurations actives sur l’interpréteur de commandes Hive à l’aide de la commande suivante :

```apache
hive> set;
```

Par exemple, utilisez la commande suivante pour démarrer l’interpréteur de commandes Hive avec l’option d’enregistrement de débogage activée sur la console :
             
```apache
hive -hiveconf hive.root.logger=ALL,console 
```

### <a name="further-reading"></a>Pour aller plus loin :

- [Hive configuration properties](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties) (Propriétés de configuration Hive, en anglais)


## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Comment analyser les données de DAG Tez sur un chemin critique de cluster ?

### <a name="issue"></a>Problème :

Vous devez analyser des informations de DAG (Directed Acyclic Graph) Tez, en particulier sur un chemin critique du cluster HDInsight.

### <a name="resolution-steps"></a>Étapes de résolution :
 
- Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH) (consultez la section Pour aller plus loin ci-dessous).

- Exécutez la commande suivante dans l’invite de commandes :
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
```

- Obtenez la liste des autres analyseurs pouvant être utilisés pour analyser les informations de DAG Tez à l’aide de la commande suivante :

```apache
hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
```

Un exemple de programme doit être donné comme premier argument.

Voici les noms de programme valides : ContainerReuseAnalyzer: Print container reuse details in a DAG CriticalPath: Find the critical path of a DAG LocalityAnalyzer: Print locality details in a DAG ShuffleTimeAnalyzer: Analyze the shuffle time details in a DAG SkewAnalyzer: Analyze the skew details in a DAG SlowNodeAnalyzer: Print node details in a DAG SlowTaskIdentifier: Print slow task details in a DAG SlowestVertexAnalyzer: Print slowest vertex details in a DAG SpillAnalyzer: Print spill details in a DAG TaskConcurrencyAnalyzer: Print the task concurrency details in a DAG VertexLevelCriticalPathAnalyzer: Find critical path at vertex level in a DAG


### <a name="further-reading"></a>Pour aller plus loin :

- [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)



## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Comment télécharger des données de DAG Tez à partir d’un cluster ?

#### <a name="issue"></a>Problème :

Vous devez télécharger des informations de DAG (Directed Acyclic Graph) Tez à partir d’un cluster HDInsight.

#### <a name="resolution-steps"></a>Étapes de résolution :

Il existe deux façons de collecter les données de DAG Tez.

- À partir de la ligne de commande :
 
    Connectez-vous au cluster HDInsight à l’aide d’un client Secure Shell (SSH). Exécutez la commande suivante dans l’invite de commandes :
   
```apache
hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId> 
```

- Vous pouvez également utiliser la vue Tez dans Ambari :
   
Accédez à Ambari--> Accédez à la vue Tez (sous l’icône de vignettes dans l’angle supérieur droit) --> Cliquez sur le DAG qui vous intéresse --> Cliquez sur Download Data (Télécharger les données).

#### <a name="further-reading"></a>Pour aller plus loin :

1) [Se connecter à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)








