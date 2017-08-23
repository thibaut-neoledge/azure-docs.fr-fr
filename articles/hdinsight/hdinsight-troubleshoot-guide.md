---
title: "Documentation Azure HDInsight - Guide de résolution des problèmes | Microsoft Docs"
description: "Résolvez les problèmes de charge de travail Hadoop sur HDInsight. Cette documentation vous montre étape par étape comment résoudre les problèmes courants liés à Hive, Spark, HBase, Storm et Kafka sur HDInsight."
services: hdinsight
author: arijitt
manager: arijitt
layout: LandingPage
ms.assetid: 
ms.service: hdinsight
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing - page
ms.date: 7/06/2017
ms.author: arijitt
ms.translationtype: HT
ms.sourcegitcommit: b6c65c53d96f4adb8719c27ed270e973b5a7ff23
ms.openlocfilehash: 9313f0713818f80bae99c32a438d97ba544a7a6b
ms.contentlocale: fr-fr
ms.lasthandoff: 08/17/2017

---


# <a name="azure-hdinsight-troubleshooting"></a>Résolution des problèmes Azure HDInsight

| Charge de travail Apache | Principales questions |
|---|---|
|![HBASE](./media/hdinsight-troubleshoot-guide/HBASE.png)<br>[Résolution de problèmes HBASE](hdinsight-troubleshoot-hbase.md)|<br>[Comment exécuter des rapports de commande hbck avec plusieurs régions non affectées ?](hdinsight-troubleshoot-hbase.md#how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions)<br><br>[Comment corriger les problèmes de délai d’expiration avec les commandes hbck pour l’affectation des régions ?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments)<br><br>[Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?](hdinsight-troubleshoot-hbase.md#how-do-i-force-disable-hdfs-safe-mode-in-an-cluster)<br><br>[Comment résoudre les problèmes de connectivité JDBC ou sqlline avec Apache Phoenix ?](hdinsight-troubleshoot-hbase.md#how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix)<br><br>[Pourquoi le démarrage d’un serveur maître échoue-t-il ?](hdinsight-troubleshoot-hbase.md#what-causes-a-master-server-to-fail-to-start)<br><br>[Pourquoi le redémarrage échoue-t-il sur un serveur régional ?](hdinsight-troubleshoot-hbase.md#what-causes-a-restart-failure-on-a-region-server)|
|![HDFS](./media/hdinsight-troubleshoot-guide/HDFS.png)<br>[Résolution de problèmes HDFS](hdinsight-troubleshoot-hdfs.md)|<br>[Comment accéder au stockage HDFS local depuis un cluster ?](hdinsight-troubleshoot-hdfs.md#how-do-i-access-local-hdfs-from-inside-a-cluster)<br><br>[Comment forcer la désactivation du mode sans échec du stockage HDFS dans un cluster ?](hdinsight-troubleshoot-hdfs.md#how-do-i-force-disable-hdfs-safe-mode-in-a-cluster)|
|![HIVE](./media/hdinsight-troubleshoot-guide/HIVE.png)<br>[Résolution de problèmes HIVE](hdinsight-troubleshoot-hive.md)|<br>[Comment exporter un metastore Hive et l’importer dans un autre cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster)<br><br>[Comment localiser les journaux Hive sur un cluster ?](hdinsight-troubleshoot-hive.md#how-do-locate-hive-logs-on-a-cluster)<br><br>[Comment lancer un interpréteur de commandes Hive avec des configurations spécifiques sur un cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-launch-hive-shell-with-specific-configurations-on-a-cluster)<br><br>[Comment analyser les données de DAG Tez sur un chemin critique de cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path)<br><br>[Comment télécharger des données de DAG Tez à partir d’un cluster ?](hdinsight-troubleshoot-hive.md#how-do-i-download-tez-dag-data-from-a-cluster)|
|![SPARK](./media/hdinsight-troubleshoot-guide/SPARK.png)<br>[Résolution de problèmes Spark](hdinsight-troubleshoot-SPARK.md)|<br>[Comment configurer une application Spark sur des clusters via Ambari ?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-ambari-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via un bloc-notes Jupyter ?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-a-jupyter-notebook-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via Livy ?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-livy-on-clusters)<br><br>[Comment configurer une application Spark sur des clusters via spark-submit ?](hdinsight-troubleshoot-spark.md#how-do-i-configure-a-spark-application-through-spark-submit-on-clusters)<br><br>[À quoi est dû l’échec d’une application Spark avec l’exception OutOfMemoryError ?](hdinsight-troubleshoot-spark.md#what-causes-a-spark-application-outofmemoryerror-exception)|
|![STORM](./media/hdinsight-troubleshoot-guide/STORM.png)<br>[Résolution de problèmes STORM](hdinsight-troubleshoot-STORM.md)|<br>[Comment accéder à l’interface utilisateur Storm sur un cluster ?](hdinsight-troubleshoot-storm.md#how-do-i-access-storm-ui-on-a-cluster)<br><br>[Comment transférer les informations de point de contrôle du spout Storm Eventhub d’une topologie à une autre ?](hdinsight-troubleshoot-storm.md#how-do-i-transfer-storm-eventhub-spout-checkpoint-information-from-one-topology-to-another)<br><br>[Comment localiser les fichiers binaires de Storm sur un cluster ?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-binaries-on-a-cluster)<br><br>[Comment déterminer la topologie de déploiement d’un cluster Storm ?](hdinsight-troubleshoot-storm.md#how-do-i-determine-the-deployment-topology-of-a-storm-cluster)<br><br>[Comment localiser les fichiers binaires de spout EventHub Storm pour le développement ?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-eventhub-spout-binaries-for-development)<br><br>[Comment localiser les fichiers de configuration Storm Log4J sur les clusters ?](hdinsight-troubleshoot-storm.md#how-do-i-locate-storm-log4j-configuration-files-on-clusters)|
|![YARN](./media/hdinsight-troubleshoot-guide/YARN.png)<br>[Résolution de problèmes YARN](hdinsight-troubleshoot-YARN.md)|<br>[Comment créer une file d’attente Yarn sur un cluster ?](hdinsight-troubleshoot-yarn.md#how-do-i-create-a-new-yarn-queue-on-a-cluster)<br><br>[Comment télécharger les journaux Yarn à partir d’un cluster ?](hdinsight-troubleshoot-yarn.md#how-do-i-download-yarn-logs-from-a-cluster)|

## <a name="hdinsight-troubleshooting-resources"></a>Ressources pour la résolution des problèmes Azure HDInsight
Pour obtenir des informations supplémentaires en vue de résoudre vos problèmes, consultez les articles suivants :

| Pour obtenir des informations sur | Consultez |
| --- | --- |
| HDInsight sur Linux et optimisation | [Informations sur l’utilisation de HDInsight sous Linux](hdinsight-hadoop-linux-information.md)<br>[Performances et mémoire Hadoop](hdinsight-hadoop-stack-trace-error-messages.md)<br>[Performances des requêtes Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) |
| Journaux et dumps | [Accéder aux journaux d’applications YARN Hadoop sous Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)<br>[Activer les dumps de tas pour les services Hadoop](hdinsight-hadoop-collect-debug-heap-dump-linux.md)|
| Erreurs | [Comprendre et résoudre les erreurs WebHCat](hdinsight-hadoop-templeton-webhcat-debug-errors.md)<br>[Paramètres Hive pour résoudre les erreurs de mémoire insuffisante](hdinsight-hadoop-hive-out-of-memory-error-oom.md) |
| Outils | [Utiliser les vues Ambari pour déboguer les travaux Tez](hdinsight-debug-ambari-tez-view.md)<br>[Optimiser les requêtes Hive](hdinsight-hadoop-optimize-hive-query.md) |



