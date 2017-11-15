---
title: "Guides de résolution des problèmes Azure HDInsight | Documents Microsoft"
description: "Résoudre des problèmes de charges de travail Hadoop à l’aide d’Azure HDInsight. La documentation pas à pas vous indique comment utiliser HDInsight pour résoudre les problèmes courants liés à Hive, Spark, YARN, HBase, HDFS et Storm."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 785884b7fd03339d195fbd7853b17300effecc6a
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-by-using-azure-hdinsight"></a>Résoudre des problèmes à l’aide d’Azure HDInsight

| Charge de travail Apache | Principales questions |
|---|---|
|![HBase](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Résoudre des problèmes HBase](hbase/apache-troubleshoot-hbase.md)|<br>[Comment exécuter des rapports de commande hbck avec plusieurs régions non affectées ?](hbase/apache-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Comment corriger les problèmes de délai d’expiration lors de l’utilisation des commandes hbck pour l’affectation des régions ?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?](hbase/apache-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)<br><br>[Comment résoudre les problèmes de connectivité JDBC ou SQLLine avec Apache Phoenix ?](hbase/apache-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Pourquoi le démarrage d’un serveur maître échoue-t-il ?](hbase/apache-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Pourquoi le redémarrage échoue-t-il sur un serveur régional ?](hbase/apache-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Résoudre des problèmes HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Comment accéder au stockage HDFS local depuis un cluster ?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![Hive](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Résoudre des problèmes HBase](hdinsight-troubleshoot-hive.md)|<br>[Comment exporter un metastore Hive et l’importer dans un autre cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Comment localiser les journaux Hive dans un cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-locate-hive-logs-on-a-cluster)<br><br>[Comment lancer l’interpréteur de commandes Hive avec des configurations spécifiques dans un cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Comment analyser les données de DAG Tez dans un chemin critique de cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Comment télécharger des données de DAG Tez à partir d’un cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![Spark](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Résoudre des problèmes Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Comment configurer une application Spark sur des clusters via Ambari ?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-ambari-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via un bloc-notes Jupyter ?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-a-jupyter-notebook-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via Livy ?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-livy-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via spark-submit ?](spark/apache-troubleshoot-spark.md#how-do-i-configure-a-spark-application-by-using-spark-submit-on-clusters)<br><br>[À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](spark/apache-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![Storm](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Résoudre des problèmes Storm](hdinsight-troubleshoot-STORM.md)|<br>[Comment accéder à l’interface utilisateur Storm sur un cluster ?](storm/apache-troubleshoot-storm.md#how-do-i-access-the-storm-ui-on-a-cluster)<br><br>[Comment transférer les informations de point de contrôle du Spout Storm Eventhub d’une topologie à une autre ?](storm/apache-troubleshoot-storm.md#how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Comment localiser les fichiers binaires de Storm sur un cluster ?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Comment déterminer la topologie de déploiement d’un cluster Storm ?](storm/apache-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Comment localiser les fichiers binaires du Spout de concentrateurs d’événements Storm pour le développement ?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-event-hub-spout-binaries-for-development)<br><br>[Comment localiser les fichiers de configuration Storm Log4J sur les clusters ?](storm/apache-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Résoudre des problèmes YARN](hdinsight-troubleshoot-YARN.md)|<br>[Comment créer une file d’attente YARN dans un cluster ?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Comment télécharger les journaux YARN à partir d’un cluster ?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Ressources pour la résolution des problèmes Azure HDInsight

| Pour obtenir des informations sur | Reportez-vous aux articles suivants |
| --- | --- |
| HDInsight sur Linux et optimisation | - [Informations sur l’utilisation de HDInsight sous Linux](hdinsight-hadoop-linux-information.md)<br>- [Résolution des problèmes liés aux performances et à la mémoire Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>- [Performances des requêtes Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Journaux et dumps | - [Accéder aux journaux d’applications YARN Hadoop sous Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>- [Activer les dumps de tas pour les services Hadoop sous Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)<br>- [Analyse des journaux HDInsight](hdinsight-debug-jobs.md)|
| Errors | - [Comprendre et résoudre les erreurs WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>- [Paramètres Hive pour résoudre l’erreur OutofMemory](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Outils | - [Utiliser les vues Ambari pour déboguer les travaux Tez](hdinsight-debug-ambari-tez-view.md)<br>- [Optimiser les requêtes Hive](hdinsight-hadoop-optimize-hive-query.md) |
