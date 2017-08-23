---
title: "Kit de ressources Azure pour IntelliJ - Déboguer des applications Spark à distance via ssh | Documents Microsoft"
description: "Obtenez des instructions étape par étape sur la façon d’utiliser les outils HDInsight dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur des clusters HDInsight via ssh"
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
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Déboguez des applications Spark à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH

Cet article propose des instructions étape par étape sur la façon d’utiliser les outils HDInsight dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur un cluster HDInsight. Vous pouvez suivre une [vidéo](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) pour déboguer votre projet.

**Configuration requise :**

* **Outils HDInsight dans le kit de ressources Azure pour IntelliJ**. Il s’agit d’une partie du kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installing the Azure Toolkit for IntelliJ](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)(Installation du kit de ressources Azure pour IntelliJ).
* **Kit de ressources Azure pour IntelliJ**. Utilisez ce kit de ressources pour créer des applications Spark pour le cluster HDInsight. Pour plus d’informations, veuillez suivre les instructions de la rubrique [Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Spark pour le cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).
* **Service HDInsight SSH avec gestion de nom d’utilisateur et de mot de passe.** Pour plus d’informations consultez la rubrique [Connexion à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) et [Utiliser le tunneling SSH pour accéder à l’IU Web Ambari, JobHistory, NameNode, Oozie et à d’autres IU Web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>Créer une application Spark Scala et la configurer pour le débogage à distance
1. Lancez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**. Cet article utilise **Spark sur exemple d’exécution de cluster HDInsight (Scala)** comme exemple.

     ![Créer un projet de débogage](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - Dans le volet gauche, sélectionnez **HDInsight**.
   - Dans le volet droit, sélectionnez un modèle Java ou Scala en fonction de vos préférences. Choisissez entre les options suivantes : 
      - **Spark sur HDInsight (Scala)**
      - **Spark sur HDInsight (Java)**
      - **Spark sur exemple d’exécution de cluster HDInsight (Scala)**
   - Outil de génération : l’assistant de création de projets Scala prend en charge Maven ou SBT en gérant les dépendances et en générant des projets scala. Sélectionnez un besoin correspondant.
2. Dans la fenêtre suivante, fournissez les informations de projet.

   ![Sélection du kit de développement logiciel (SDK) Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - Fournissez un nom de projet et un emplacement de projet.
   - Pour **Projet SDK**, utilisez **Java 1.8** pour le cluster **Spark 2.x** ou **Java 1.7** pour le cluster **Spark 1.x**.
   - Pour la **version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le Kit de développement logiciel Spark et le kit de développement logiciel Scala. Si la version du cluster spark est inférieure à la version 2.0, choisissez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x.** Cet article utilise **Spark 2.0.2 (Scala 2.11.8)** comme exemple.
3. Sélectionnez **src** > **principal** > **scala** pour ouvrir votre code dans le projet. Cet article utilise le script **SparkCore_wasbloTest** comme exemple.
4. Pour accéder au menu **Modifier les configurations**, sélectionnez l’icône dans le coin supérieur droit. Dans ce menu, vous pouvez créer ou modifier les configurations pour le débogage à distance.

   ![Modifier les configurations](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. Dans la fenêtre **Exécuter/déboguer les configurations**, cliquez sur le signe plus (**+**). Puis sélectionnez l’option **Soumettre un travail Spark**.

   ![Modifier les configurations](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. Entrez les informations sur le **Nom**, le **Cluster Spark** et le **Nom principal de la classe**. Puis sélectionnez **Configuration avancée**. 

   ![Exécuter le débogage des configurations](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. Dans la boîte de dialogue **Configuration avancée des soumissions Spark**, sélectionnez **Activer le débogage à distance Spark**. Entrez le nom d’utilisateur ou le mot de passe SSH ou utilisez un fichier de clé privée. Pour l’enregistrer, sélectionnez **Ok**.

   ![Activer le débogage à distance Spark](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. La configuration est maintenant enregistrée avec le nom fourni. Pour afficher les détails de configuration, sélectionnez le nom de configuration. Pour apporter des modifications, sélectionnez **Modifier les configurations**. 
9. Une fois le paramétrage des configurations terminé, vous pouvez exécuter le projet sur le cluster à distance ou effectuer un débogage à distance.

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>Apprendre à effectuer un débogage à distance et une exécution à distance
### <a name="scenario-1-perform-remote-run"></a>Scénario 1 : effectuer une exécution à distance
1. Pour exécuter le projet à distance, sélectionnez l’icône **Exécuter**.

   ![Cliquez sur l’icône d’exécution](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. La fenêtre **Soumission HDInsight Spark** affiche l’état d’exécution de l’application. Vous pouvez surveiller la progression du travail Scala à partir des informations de cette section.

   ![Cliquez sur l’icône d’exécution](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>Scénario 2 : effectuer un débogage à distance
1. Configurez un point de rupture, puis sélectionnez l’icône **Déboguer**.

    ![Cliquez sur l’icône de débogage](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. Lorsque l’exécution du programme atteint le point de rupture, un onglet **Débogueur** s’affiche dans le volet inférieur. Les informations sur les paramètres et les variables de la vue s’affichent également dans la fenêtre **Variable**. Cliquez sur l’icône **Franchir** pour passer à la ligne de code suivante. Vous pouvez ensuite exécuter le code pas à pas. Sélectionnez l’icône **Reprendre le programme** pour continuer l’exécution du code. Vous pouvez consulter l’état d’exécution dans la fenêtre **Soumission HDInsight Spark**. 

   ![Onglet Débogage](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénario 3 : effectuer un débogage à distance et une résolution des bogues
Dans cette section, nous vous indiquons comment mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ pour une résolution simple. Pour l’exemple de code suivant, une exception est levée car le fichier cible existe déjà.
  
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
1. Configurez deux points de rupture, puis cliquez sur l’icône **Déboguer** pour démarrer le processus de débogage à distance.

2. Le code s’arrête au premier point de rupture et les informations sur les paramètres et les variables s’affichent dans la fenêtre **Variable**. 

3. Cliquez sur l’icône **Reprendre le programme** pour continuer. Le code s’arrête au deuxième point. L’exception est interceptée comme prévu.

  ![Lever l’erreur](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. Cliquez sur l’icône **Reprendre le programme** à nouveau. La fenêtre **Soumission HDInsight Spark** affiche une erreur d’exécution des tâches.

  ![Soumission de l’erreur](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. Pour mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ, sélectionnez **Déboguer** à nouveau. Les fenêtres de variables s’affichent à nouveau. 

6. Cliquez avec le bouton droit de la cible de l’onglet **Déboguer**, puis sélectionnez **Définir la valeur**. Ensuite, entrez une nouvelle valeur pour la variable. Puis sélectionnez **Entrer** pour enregistrer la valeur. 

  ![Définir la valeur](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. Cliquez sur l’icône **Reprendre le programme** pour continuer l’exécution du programme. Cette fois-ci, l’exception n’est pas interceptée. Vous pouvez voir que le projet s’exécute correctement sans aucune exception.

  ![Déboguer sans exception](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créez un projet Scala (vidéo) : [Créer des Applications Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur HDInsight Spark via VPN](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)
 
