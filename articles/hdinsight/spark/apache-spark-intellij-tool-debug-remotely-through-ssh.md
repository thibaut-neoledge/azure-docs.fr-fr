---
title: "Kit de ressources Azure pour IntelliJ : déboguer des applications Spark à distance via SSH | Microsoft Docs"
description: "Obtenez des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur des clusters HDInsight via SSH"
keywords: "déboguer à distance intellij, débogage à distance intellij, ssh, intellij, hdinsight, déboguer intellij, débogage"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 08/24/2017
ms.author: jejiang
ms.openlocfilehash: cebbe2a0e28d49c93d0ebf12cc04b3d201dcec97
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Déboguez des applications Spark sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH

Cet article propose des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur un cluster HDInsight. Pour déboguer votre projet, vous pouvez également regarder la vidéo [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Déboguer des applications HDInsight Spark avec le kit de ressources Azure pour IntelliJ).

**Configuration requise**

* **Outils HDInsight dans le kit de ressources Azure pour IntelliJ**. Cet outil fait partie du kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).
* **Kit de ressources Azure pour IntelliJ**. Utilisez ce kit de ressources pour créer des applications Spark pour un cluster HDInsight. Pour plus d’informations, veuillez suivre les instructions de la rubrique [Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Spark pour un cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Service HDInsight SSH avec gestion de nom d’utilisateur et de mot de passe**. Pour plus d’informations consultez la rubrique [Connexion à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) et [Utiliser le tunneling SSH pour accéder à l’IU web Ambari, JobHistory, NameNode, Oozie et à d’autres IU web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Créer une application Spark Scala et la configurer pour le débogage à distance

1. Démarrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet** , procédez comme suit :

   a. Sélectionnez **HDInsight**. 

   b. Sélectionnez un modèle Java ou Scala en fonction de vos préférences. Sélectionnez entre les options suivantes :

      - **Spark sur HDInsight (Scala)**

      - **Spark sur HDInsight (Java)**

      - **Spark sur exemple d’exécution de cluster HDInsight (Scala)**

      Cet exemple utilise un modèle **Spark sur exemple d’exécution de cluster HDInsight (Scala)**.

   c. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

      - **Maven**, pour la prise en charge de l’Assistant de création de projets Scala

      -  **SBT**, pour gérer les dépendances et la génération du projet Scala 

      ![Créer un projet de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Sélectionnez **Suivant**.     
 
3. Dans la fenêtre **Nouveau projet** qui s’ouvre, effectuez les opérations suivantes :

   ![Sélectionner le SDK Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Entrez un nom de projet et un emplacement de projet.

   b. Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster **Spark 2.x** ou **Java 1.7** pour le cluster **Spark 1.x**.

   c. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le SDK Spark et le SDK Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x.** La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.

   d. Sélectionnez **Terminer**.

4. Sélectionnez **src** > **principal** > **scala** pour ouvrir votre code dans le projet. Cet exemple utilise le script **SparkCore_wasbloTest**.

5. Pour accéder au menu **Modifier les configurations**, sélectionnez l’icône dans le coin supérieur droit. Dans ce menu, vous pouvez créer ou modifier les configurations pour le débogage à distance.

   ![Modifier les configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

6. Dans la boîte de dialogue **Run/Debug Configurations** (Exécuter/Déboguer les configurations) sélectionnez le signe plus (**+**). Puis sélectionnez l’option **Soumettre un travail Spark**.

   ![Ajouter une nouvelle configuration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
7. Entrez les informations sur le **Nom**, le **Cluster Spark** et le **Nom principal de la classe**. Puis sélectionnez **Configuration avancée**. 

   ![Exécuter le débogage des configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

8. Dans la boîte de dialogue **Configuration avancée des soumissions Spark**, sélectionnez **Activer le débogage à distance Spark**. Entrez le nom d’utilisateur SSH, puis entrez un mot de passe ou utilisez un fichier de clé privée. Pour enregistrer la configuration, sélectionnez **OK**.

   ![Activer le débogage à distance Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

9. La configuration est maintenant enregistrée avec le nom fourni. Pour afficher les détails de configuration, sélectionnez le nom de configuration. Pour apporter des modifications, sélectionnez **Modifier les configurations**. 

10. Une fois le paramétrage des configurations terminé, vous pouvez exécuter le projet sur le cluster à distance ou effectuer un débogage à distance.

## <a name="learn-how-to-perform-remote-debugging"></a>Apprendre à effectuer un débogage à distance
### <a name="scenario-1-perform-remote-run"></a>Scénario 1 : effectuer une exécution à distance

Dans cette section, nous vous montrons comment déboguer des pilotes et des exécuteurs.

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. Configurez des points de rupture, puis sélectionnez l’icône **Déboguer**.

   ![Sélectionner l’icône de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quand l’exécution du programme atteint le point de rupture, un onglet **Pilote** et deux onglets **Exécuteur** s’affichent dans le volet **Débogueur**. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le code ; le point de rupture suivant est alors atteint et l’onglet **Exécuteur** correspondant est sélectionné. Vous pouvez consulter les journaux d’exécution sous l’onglet **Console** correspondant.

   ![Onglet Débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>Scénario 2 : effectuer un débogage à distance et une résolution des bogues
Dans cette section, nous vous indiquons comment mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ pour une résolution simple. Dans l’exemple de code suivant, une exception est levée car le fichier cible existe déjà.
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>Pour effectuer un débogage à distance et une résolution des bogues
1. Configurez deux points de rupture, puis sélectionnez l’icône **Déboguer** pour démarrer le processus de débogage à distance.

2. Le code s’arrête au premier point de rupture et les informations sur les paramètres et les variables s’affichent dans le volet **Variable**. 

3. Sélectionnez l’icône **Reprendre le programme** pour continuer. Le code s’arrête au deuxième point. L’exception est interceptée comme prévu.

  ![Lever l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Resélectionnez l’icône **Reprendre le programme**. La fenêtre **Soumission HDInsight Spark** affiche une erreur d’exécution de tâche.

  ![Soumission de l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Pour mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ, resélectionnez **Déboguer**. Le volet **Variables** réapparaît. 

6. Cliquez avec le bouton droit de la cible de l’onglet **Déboguer**, puis sélectionnez **Définir la valeur**. Ensuite, entrez une nouvelle valeur pour la variable. Puis sélectionnez **Entrer** pour enregistrer la valeur. 

  ![Définir la valeur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le programme. Cette fois-ci, aucune exception n’est interceptée. Vous pouvez voir que le projet s’exécute correctement sans aucune exception.

  ![Déboguer sans exception](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Étapes suivantes
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créez un projet Scala (vidéo) : [Créer des applications Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser le kit de ressources Azure pour IntelliJ afin de déboguer des applications Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans le cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
