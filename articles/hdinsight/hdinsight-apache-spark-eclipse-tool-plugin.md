---
title: "Kit de ressources Azure pour Eclipse - Créer des applications Scala pour HDInsight Spark | Microsoft Docs"
description: "Utilisez les outils HDInsight dans le kit de ressources Azure pour Eclipse pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark, directement à partir de l’IDE Eclipse."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 013a3175d5e19689629d1d0ea3b413184e71c485
ms.contentlocale: fr-fr
ms.lasthandoff: 06/07/2017


---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Utiliser le kit de ressources Azure pour Eclipse pour créer des applications Spark pour un cluster HDInsight

Utilisez HDInsight Tools du kit de ressources Azure pour Eclipse pour développer des applications Spark écrites en Scala et envoyez-les à un cluster Azure HDInsight Spark, directement à partir de l’environnement de développement intégré (IDE) Eclipse. Vous pouvez utiliser le plug-in HDInsight Tools de différentes manières :

* Pour développer une application Spark Scala et l’envoyer à un cluster HDInsight Spark
* Pour accéder à vos ressources de cluster Azure HDInsight Spark
* Pour développer et exécuter une application Spark Scala localement

> [!IMPORTANT]
> Cet outil peut être utilisé pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.
> 
> 

## <a name="prerequisites"></a>Composants requis

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit version 8, qui est utilisé pour l’exécution d’IDE Eclipse. Vous pouvez le télécharger à partir du [site web Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Eclipse. Cet article utilise Eclipse Neon. Vous pouvez l’installer à partir du [site web Eclipse](https://www.eclipse.org/downloads/).
* IDE Scala pour Eclipse. 
  
  * **Si vous avez installé l’IDE Eclipse**, vous pouvez ajouter le plug-in IDE Scala en accédant à **Help (Aide)** > **Install New Software (Installer un nouveau logiciel)**, puis ajouter [http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site) en tant que source pour télécharger le plug-in Scala pour Eclipse. 
  * **Si l’IDE Eclipse n’est pas installé**, vous pouvez installer l’IDE Scala directement à partir du [site web Scala](http://scala-ide.org/download/sdk.html). Téléchargez le fichier .zip, extrayez-le, accédez au dossier **/eclipse**, puis exécutez le fichier **eclipse.exe** à partir de cet emplacement.
    
    > [!NOTE]
    > Les étapes décrites dans cet article reposent sur l’utilisation de l’IDE Eclipse avec le plug-in Scala installé.
    > 
    > 
* Kit de développement logiciel (SDK) Spark. Vous pouvez le télécharger à partir de [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409).
* e(fx)clipse. Vous pouvez l’installer à partir de la [page de téléchargement du site web Eclipse](https://www.eclipse.org/efxclipse/install.html).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Installation d’HDInsight Tools dans le kit de ressources Azure pour Eclipse
HDInsight Tools pour Eclipse est disponible dans le cadre du kit de ressources Azure pour Eclipse. Pour plus d’informations sur l’installation, voir [Installation du kit de ressources Azure pour Eclipse](../azure-toolkit-for-eclipse-installation.md).

## <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :
1. Démarrez l’IDE Eclipse et ouvrez l’Explorateur Azure. Dans le menu **Window** (Fenêtre), cliquez sur **Show View** (Afficher la vue), puis sur **Other** (Autre). Dans la boîte de dialogue qui s’ouvre, développez **Azure**, cliquez sur **Explorateur Azure**, puis sur **OK**.

    ![Boîte de dialogue Affichage](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Cliquez avec le bouton droit sur le nœud **Azure**, puis cliquez sur **Se connecter**.
3. Dans la boîte de dialogue **Azure Sign in (Connexion à Azure)**, choisissez la méthode d’authentification, cliquez sur **Se connecter** et entrez vos informations d’identification Azure.
   
    ![Boîte de dialogue Connexion à Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Une fois que vous êtes connecté, la boîte de dialogue **Sélectionner des abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Cliquez sur **Sélectionner** pour fermer la boîte de dialogue.

    ![Boîte de dialogue Sélectionner des abonnements](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Dans l’onglet **Explorateur Azure**, développez **HDInsight** pour afficher les clusters HDInsight Spark de votre abonnement.
   
    ![Clusters HDInsight Spark dans l’Explorateur Azure](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.
   
    ![Développement d’un nom de cluster pour voir les ressources](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configuration d’un projet Spark Scala pour un cluster HDInsight Spark

1. Dans l’espace de travail IDE Eclipse, cliquez sur **File** (Fichier), sur **New** (Nouveau), puis sur **Project** (Projet). 
2. Dans l’Assistant New Project (Nouveau projet), développez **HDInsight**, sélectionnez **Spark on HDInsight (Scala)** (Spark sur HDInsight [Scala]), puis cliquez sur **Next** (Suivant).

    ![Sélection du projet Spark sur HDInsight (Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Dans la boîte de dialogue **New HDInsight Scala Project** (Nouveau projet HDInsight Scala), indiquez les valeurs suivantes, puis cliquez sur **Next** (Suivant) :
   * Entrez un nom pour le projet.
   * Dans la zone **JRE**, vérifiez que l’option **Use an execution environment JRE** (Utiliser un environnement d’exécution JRE) est définie sur **JavaSE-1.7**.
   * Vérifiez que le Kit de développement logiciel (SDK) Spark est défini sur l’emplacement où vous avez téléchargé le Kit de développement logiciel (SDK). Le lien vers l’emplacement de téléchargement est inclus dans la section [Composants requis](#prerequisites), plus haut dans cet article. Vous pouvez également télécharger le Kit de développement logiciel (SDK) à partir du lien inclus dans la boîte de dialogue.

    ![Boîte de dialogue New HDInsight Scala Project (Nouveau projet HDInsight Scala)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
4.  Dans la boîte de dialogue suivante, cliquez sur l’onglet **Libraries** (Bibliothèques), conservez les valeurs par défaut, puis cliquez sur **Finish** (Terminer). 
   
    ![Onglet Libraries (Bibliothèques)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)


## <a name="run-a-spark-scala-application-on-an-azure-data-lake-store-cluster"></a>Exécuter une application Spark Scala sur un cluster Azure Data Lake Store
Si vous souhaitez soumettre une application à Azure Data Lake Store, vous devez choisir le mode **interactif** pendant le processus de connexion à Azure. 

   ![Option interactive à la connexion](./media/hdinsight-apache-spark-eclipse-tool-plugin/Interactive-Authentication.png)

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Créer une application Scala pour un cluster HDInsight Spark

1. Dans l’IDE Eclipse, dans Package Explorer (Explorateur de packages), développez le projet créé précédemment, cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis cliquez sur **Other** (Autre).
2. Dans la boîte de dialogue **Select a wizard** (Sélectionner un Assistant), développez **Scala Wizards** (Assistants Scala), cliquez sur **Scala Object** (Objet Scala), puis cliquez sur **Next** (Suivant).
   
    ![Boîte de dialogue Select a wizard (Sélectionner un Assistant)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. Dans la boîte de dialogue **Create New File** (Créer un nouveau fichier), entrez un nom pour l’objet, puis cliquez sur **Finish** (Terminer).
   
    ![Boîte de dialogue Create New File (Créer un fichier)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Collez le code suivant dans l’éditeur de texte :
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACOut")
          }        
        }
5. Exécutez l’application sur un cluster HDInsight Spark :
   
   1. Dans Package Explorer (Explorateur de packages), cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).        
   2. Dans la boîte de dialogue **Spark Submission** (Envoi Spark), entrez les valeurs suivantes, puis cliquez sur **Submit (Envoyer)** :
      
      * Pour **Cluster Name**(Nom du cluster), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.
      * Sélectionnez un artefact à partir du projet Eclipse ou choisissez-en un sur un disque dur.
      * Dans la zone de texte **Main class name** (Nom de la classe principale), entrez le nom de l’objet spécifié dans le code.
      * Étant donné que le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.
        
       ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
   3. L’onglet **Spark Submission** (Envoi Spark) doit commencer à afficher la progression. Vous pouvez arrêter l’application en cliquant sur le bouton rouge dans la fenêtre **Spark Submission** (Envoi Spark). Vous pouvez également afficher les journaux pour cette exécution d’application spécifique en cliquant sur l’icône en forme de globe (indiquée par la zone bleue dans l’image).
      
       ![Fenêtre Spark Submission (Envoi Spark)](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide de HDInsight Tools dans le kit de ressources Azure pour Eclipse
Vous pouvez effectuer diverses opérations à l’aide de HDInsight Tools, y compris en accédant à la sortie du travail.

### <a name="access-the-storage-container-for-the-cluster"></a>Accéder au conteneur de stockage du cluster
1. Dans l’Explorateur Azure, développez le nœud racine **HDInsight** pour afficher la liste des clusters HDInsight Spark disponibles.
2. Développez le nom de cluster pour voir le compte de stockage et le conteneur de stockage par défaut du cluster.
   
    ![Compte de stockage et conteneur de stockage par défaut](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Cliquez sur le nom du conteneur de stockage associé au cluster. Dans le volet droit, double-cliquez sur le dossier **HVACOut**. Ouvrez l’un des fichiers **part-** pour afficher la sortie de l’application.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark
1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.
2. Dans le tableau de bord du serveur d’historique Spark, utilisez le nom de l’application pour rechercher l’application que vous venez d’exécuter. Dans le code précédent, vous définissez le nom de l’application en utilisant `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Démarrer le portail Ambari
1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters [Ambari]). 
2. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure
Par défaut, HDInsight Tools du kit de ressources Azure pour Eclipse répertorie les clusters Spark de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster. 

1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nœud racine **Azure**, puis cliquez sur **Gérer les abonnements**. 
2. Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis cliquez sur **Fermer**. Vous pouvez également cliquer sur **Se déconnecter** si vous souhaitez vous déconnecter de votre abonnement Azure.

## <a name="run-a-spark-scala-application-locally"></a>Exécuter une application Spark Scala localement
Vous pouvez utiliser HDInsight Tools du kit de ressources Azure pour Eclipse pour exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage, et elles peuvent être exécutées et testées localement.

### <a name="prerequisite"></a>Configuration requise
Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Cette exception est liée à l’absence du fichier **WinUtils.exe** dans Windows. 

Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Vous devez ensuite ajouter la variable d’environnement **HADOOP_HOME** et définir la valeur de la variable sur **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Exécuter une application Spark Scala locale
1. Démarrez Eclipse et créez un projet. Dans la boîte de dialogue **New Project** (Nouveau projet), choisissez les options suivantes, puis cliquez sur **Next** (Suivant).
   
   * Dans le volet gauche, sélectionnez **HDInsight**.
   * Dans le volet droit, sélectionnez **Spark on HDInsight Local Run Sample (Scala)**(Exemple d’exécution locale de Spark sur HDInsight [Scala]).

    ![Boîte de dialogue Nouveau projet](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
2. Pour fournir les détails du projet, suivez les étapes 3 à 6 indiquées dans la section précédente [Configuration d’un projet Spark Scala pour un cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark cluster).
3. Le modèle ajoute un exemple de code (**LogQuery**) sous le dossier **src** que vous pouvez exécuter localement sur votre ordinateur.
   
    ![Emplacement de LogQuery](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. Cliquez avec le bouton droit sur l’application **LogQuery**, pointez sur **Run As** (Exécuter en tant que), puis cliquez sur **1 Scala Application**. Une sortie semblable à celle-ci doit apparaître dans l’onglet **Console** en bas de l’écran :
   
   ![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback-and-known-issues"></a>Commentaires et problèmes connus
Pour l’instant, la visualisation directe des sorties Spark n’est pas prise en charge.

Si vous avez des suggestions ou des commentaires, ou que vous rencontrez des problèmes lors de l’utilisation de cet outil, n’hésitez pas à nous envoyer un e-mail à l’adresse hdivstool@microsoft.com.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)


