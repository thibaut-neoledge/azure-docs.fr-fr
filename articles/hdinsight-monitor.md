<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

Surveillance de HDInsight
=========================

Dans cette rubrique, vous allez apprendre à surveiller un cluster HDInsight.

Sommaire
--------

-   [Surveillance d'un cluster HDInsight](#monitorcluster)
-   [Affichage de l'historique des tâches Hadoop](#jobhistory)

Surveillance d'un cluster HDInsight
-----------------------------------

Pour surveiller l'intégrité d'un cluster HDInsight et les tâches Hadoop exécutées sur le cluster, vous pouvez vous connecter au tableau de bord HDInsight et cliquer sur la vignette Monitor Cluster.

![HDI.TileMonitorCluster](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

La page Monitor a l'aspect suivant :

![HDI.MonitorPage](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

À droite, la fenêtre indique que Namenode et le dispositif de suivi des tâches sont opérationnels, et que les 4 nœuds de données sont dans un état sain.

À gauche, la fenêtre affiche les mesures de mappage/réduction au cours des 30 dernières minutes. Vous pouvez régler les fenêtres de surveillance sur 30 minutes, 1 heure, 3 heures, 12 heures, 1 jour, 2 jours, 1 semaine et 2 semaines.

Affichage de l'historique des tâches Hadoop
-------------------------------------------

Pour afficher l'historique des tâches Hadoop, connectez-vous au tableau de bord HDInsight, puis cliquez sur la vignette de l'historique des tâches.

![HDI.TileJobHistory](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

La vignette affiche le nombre de tâches qui ont été exécutées ; l'image précédente, par exemple, indique que l'historique des tâches est disponible pour 6 tâches. La page de l'historique des tâches a l'aspect suivant :

![HDI.JobHistoryPage](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

Voir aussi
----------

-   [Administration de HDInsight](/fr-fr/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Déploiement d'un cluster HDInsight par programme](/fr-fr/manage/services/hdinsight/howto-deploy-cluster/)
-   [Exécution de tâches à distance sur votre cluster HDInsight par programme](/fr-fr/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [Didacticiel : Prise en main d'Azure HDInsight](/fr-fr/manage/services/hdinsight/get-started-hdinsight/)

[hdi-monitor-cluster-tile]: ./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG
[hdi-monitor-page]: ./media/hdinsight-monitor/HDI.MonitorPage.PNG
[hdi-job-history-tile]: ./media/hdinsight-monitor/HDI.TileJobHistory.PNG
[hdi-job-history-page]: ./media/hdinsight-monitor/HDI.JobHistoryPage.PNG
