---
title: "Créer des applications Spark Scala à l’aide d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ | Microsoft Docs"
description: "Apprenez à créer une application Spark autonome à exécuter sur un cluster HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 70fbc8f973a341f818cb5958931a199021b7dc46
ms.openlocfilehash: 4e63cb94d9bd39f0621eb0dc07c60335a91324d8


---
# <a name="use-hdinsight-tools-in-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-spark-linux-cluster"></a>Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster Linux HDInsight Spark
Cet article fournit des instructions pas à pas sur le développement d’applications Spark écrites en Scala et leur envoi à un cluster HDInsight Spark à l’aide d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ.  Vous pouvez utiliser ces outils de différentes manières :

* Pour développer une application Spark Scala et l’envoyer à un cluster HDInsight Spark
* Pour accéder à vos ressources de cluster Azure HDInsight Spark
* Pour développer et exécuter une application Spark Scala localement

Vous pouvez également visionner une vidéo [ici](https://mix.office.com/watch/1nqkqjt5xonza) pour commencer.

> [!IMPORTANT]
> Cet outil peut être utilisé pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.
> 
> 

## <a name="prerequisites"></a>Conditions préalables
* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IntelliJ IDEA. Cet article utilise la version 15.0.1. Vous pouvez l’installer [ici](https://www.jetbrains.com/idea/download/).

## <a name="install-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Installer HDInsight Tools dans le kit de ressources Azure pour IntelliJ
HDInsight Tools pour IntelliJ est disponible dans le cadre du kit de ressources Azure pour IntelliJ. Pour obtenir des instructions sur l’installation du kit de ressources Azure, voir [Installation du kit de ressources Azure pour IntelliJ](../azure-toolkit-for-intellij-installation.md).

## <a name="log-into-your-azure-subscription"></a>Connexion à votre abonnement Azure
1. Lancez l’IDE IntelliJ et ouvrez Azure Explorer. Dans le menu **Affichage** de l’IDE, cliquez sur **Fenêtres d’outil**, puis sur **Azure Explorer**.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. Cliquez avec le bouton droit sur le nœud **Azure** dans **Azure Explorer**, puis cliquez sur **Gérer les abonnements**.
3. Dans la boîte de dialogue **Gérer les abonnements**, cliquez sur **Se connecter** et entrez vos informations d’identification Azure.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. Une fois que vous êtes connecté, la boîte de dialogue **Gérer les abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Cliquez sur **Fermer** dans la boîte de dialogue.
5. Dans l’onglet **Azure Explorer**, développez **HDInsight** pour afficher les clusters HDInsight Spark de votre abonnement.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Exécuter une application Scala Spark sur un cluster HDInsight Spark
1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
   
   * Dans le volet gauche, sélectionnez **HDInsight**.
   * Dans le volet droit, sélectionnez **Spark on HDInsight (Scala)**(Spark on HDInsight (Scala)).
   * Cliquez sur **Next**.
2. Dans la fenêtre suivante, fournissez les informations de projet.
   
   * Fournissez un nom de projet et un emplacement de projet.
   * Pour un **projet SDK**, Java 1.7 ou version ultérieure pour un cluster Spark 1.6, et Java 1.8 pour un cluster Spark 2.0.
   * Pour **Scala SDK** (Kit de développement logiciel de Scala (SDK)), cliquez sur **Créer**, cliquez sur **Télécharger**, puis sélectionnez la version de Scala à utiliser.
   * * Choisissez **JDK 1.8 et Scala 2.11.x** si vous souhaitez envoyer la tâche à un cluster Spark 2.0.
   * * Choisissez **JDK 1.7 ou version ultérieure et Scala 2.10.x** si vous souhaitez envoyer la tâche à un cluster Spark 1.6.

        ![](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * Pour le **Kit de développement logiciel (SDK) Spark**, téléchargez et utilisez le Kit de développement logiciel (SDK) disponible [ici](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) (spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar pour un cluster Spark 2.0 et spark-assembly-x.jar pour un cluster Spark 1.6). Vous pouvez également ignorer ce dernier et utiliser le [référentiel Spark Maven](http://mvnrepository.com/search?q=spark) à la place. Toutefois, vérifiez que vous disposez du référentiel Maven approprié pour développer vos applications Spark. (Par exemple, vous devez vous assurer que le module Spark Streaming est installé si vous utilisez la fonctionnalité de diffusion en continu de Spark. En outre, vérifiez que vous utilisez le référentiel libellé Scala 2.10 pour un cluster Spark 1.6 et Scala 2.11 pour un cluster Spark 2.0.)
     
       ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
   * Cliquez sur **Terminer**.
3. Le projet Spark crée automatiquement un artefact à votre intention. Pour voir l’artefact, procédez comme suit.
   
   1. Dans le menu **Fichier**, cliquez sur **Structure de projet**.
   2. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Artefacts** pour afficher l’artefact créé par défaut.
      
       ![Créer un fichier jar](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
      Vous pouvez également créer votre propre artefact en cliquant sur l’icône **+** mis en surbrillance dans la capture d’écran ci-dessus.
4. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Projet**. Si le **SDK du projet** est défini sur 1.8, assurez-vous que le **niveau de langage du projet** est défini sur **7 - Diamonds, ARM, multi-catch, etc** (facultatif pour un cluster Spark 2.0).
   
    ![Définir le niveau de langage du projet](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. Ajoutez le code source de votre application.
   
   1. Dans **Project Explorer** (Explorateur de projets), cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis cliquez sur **Scala class** (Classe Scala).
      
       ![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. Dans la boîte de dialogue **Create New Scala Class** (Créer une classe Scala), indiquez un nom, dans la zone **Kind** (Genre), sélectionnez **Object** (Objet), puis cliquez sur **OK**.
      
       ![Ajouter le code source](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. Dans le fichier **MyClusterApp.scala** , collez le code suivant. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV et écrit la sortie dans **/HVACOut** sous le conteneur de stockage par défaut du cluster.

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows which have only one digit in the 7th column in the CSV
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

1. Exécutez l’application sur un cluster HDInsight Spark.
   
   1. Dans **l’Explorateur de projets**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).
      
       ![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. Vous êtes invité à entrer les informations d’identification de votre abonnement Azure. Dans la boîte de dialogue **Spark Submission** (Envoi Spark), entrez les valeurs suivantes.
      
      * Dans la zone **Spark clusters (Linux only)**(Clusters Spark (Linux uniquement)), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.
      * Vous devez sélectionnez un artefact à partir du projet IntelliJ, ou en choisir un sur le disque dur.
      * En regard de la zone de texte **Main class name** (Nom de classe principale), cliquez sur le bouton de sélection (![ellipsis](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png)), sélectionnez la classe principale dans le code source de votre application, puis cliquez sur **OK**.
        
          ![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * Étant donné que le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.
      * Après avoir entré toutes les entrées, la boîte de dialogue doit être similaire à l’exemple suivant.
        
          ![Envoyer l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      * Cliquez sur **Envoyer**.
   3. L’onglet **Spark Submission** (Envoi Spark) en bas de la fenêtre doit commencer à afficher la progression. Vous pouvez également arrêter l’application en cliquant sur le bouton rouge dans la fenêtre « Spark Submission ».
      
       ![Résultat de l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      Dans la section suivante, vous allez apprendre à accéder à la sortie du travail à l’aide d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ.

## <a name="access-and-manage-hdinsight-spark-clusters-using-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ
Vous pouvez effectuer diverses opérations à l’aide d’HDInsight Tools, qui fait partie du kit de ressources Azure pour IntelliJ.

### <a name="access-the-job-view-directly-from-the-hdinsight-tools"></a>Accéder directement à l’affichage des travaux à partir d’HDInsight Tools
1. Dans **Azure Explorer**, développez **HDInsight**, développez le nom du cluster Spark, puis cliquez sur **Travaux**.
2. Dans le volet droit, l’onglet **Spark Job View** (Affichage des travaux Spark) affiche toutes les applications qui ont été exécutées sur le cluster. Cliquez sur le nom de l’application pour laquelle vous souhaitez afficher plus de détails.
   
    ![Accéder à la vue des tâches](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)
3. Les zones **Error Message** (Message d’erreur), **Job Output** (Sortie des travaux), **Livy Job Logs** (Journaux de travaux Livy) et **Spark Driver Logs** (Journaux de pilotes Spark) sont renseignées en fonction de l’application que vous sélectionnez.
4. Vous pouvez également ouvrir **l’interface utilisateur de l’historique Spark** et **l’interface utilisateur YARN** (au niveau de l’application) en cliquant sur les boutons respectifs en haut de l’écran.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark
1. Dans **Azure Explorer**, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.
2. Dans le tableau de bord Serveur d’historique Spark, vous pouvez rechercher le nom de l’application que vous venez d’exécuter. Dans le code ci-dessus, vous avez défini le nom de l’application en utilisant la syntaxe `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### <a name="launch-the-ambari-portal"></a>Lancez le portail Ambari
Dans **Azure Explorer**, développez **HDInsight**, cliquez avec le bouton droit sur le nom de votre cluster Spark et sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters (Ambari)). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous devez les avoir spécifiées au moment de l’approvisionnement du cluster.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure
Par défaut, HDInsight Tools répertorie les clusters Spark à partir de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster. Dans **Azure Explorer**, cliquez avec le bouton droit sur le nœud racine **Azure**, puis cliquez sur **Gérer les abonnements**. Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis cliquez sur **Close**(Fermer). Vous pouvez également cliquer sur **Sign Out** (Déconnexion) si vous souhaitez vous déconnecter de votre abonnement Azure.

## <a name="run-a-spark-scala-application-locally"></a>Exécuter une application Spark Scala localement
Vous pouvez utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin d’exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage et peuvent être exécutées et testées localement.

### <a name="prerequisite"></a>Configuration requise
Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception liée à l’absence d’un fichier WinUtils.exe sur Windows, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) . Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable à partir d’ici](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Vous devez ensuite ajouter une variable d’environnement **HADOOP_HOME** et définir la valeur de la variable sur **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Exécuter une application Spark Scala locale
1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.
   
    ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
   
   * Dans le volet gauche, sélectionnez **HDInsight**.
   * Dans le volet droit, sélectionnez **Spark on HDInsight Local Run Sample (Scala)**(Exemple d’exécution locale de Spark sur HDInsight (Scala)).
   * Cliquez sur **Next**.
2. Dans la fenêtre suivante, fournissez les informations de projet.
   
   * Fournissez un nom de projet et un emplacement de projet.
   * Pour **Project SDK**(Kit de développement logiciel (SDK) de projet), prenez soin d’indiquer une version de Java supérieure à la version 7.
   * Pour **Scala SDK** (Kit de développement logiciel de Scala (SDK)), cliquez sur **Créer**, cliquez sur **Télécharger**, puis sélectionnez la version de Scala à utiliser. **Scala 2.11.x pour Spark 2.0 et Scala 2.10.x pour Spark 1.6**.
     
       ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * Pour le **Kit de développement logiciel (SDK) Spark**, téléchargez et utilisez le Kit de développement logiciel (SDK) disponible [ici](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409). Vous pouvez également ignorer ce dernier et utiliser le [référentiel Spark Maven](http://mvnrepository.com/search?q=spark) à la place. Toutefois, vérifiez que vous disposez du référentiel Maven approprié pour développer vos applications Spark. (Par exemple, vous devez vous assurer que le module Spark Streaming est installé si vous utilisez la fonctionnalité de diffusion en continu de Spark. En outre, vérifiez que vous utilisez le référentiel libellé Scala 2.10 pour un cluster Spark 1.6 et Scala 2.11 pour un cluster Spark 2.0.)
     
       ![Créer une application Spark Scala](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
   * Cliquez sur **Terminer**.
3. Le modèle ajoute un exemple de code (**LogQuery**) dans le dossier **src** que vous pouvez exécuter localement sur votre ordinateur.
   
    ![Application Scala locale](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. Cliquez avec le bouton droit sur l’application **LogQuery**, puis cliquez sur **Run 'LogQuery'** (Exécuter LogQuery). Une sortie semblable à celle-ci doit apparaître dans l’onglet **Run** (Exécuter) en bas de l’écran.
   
   ![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-the-hdinsight-tools-in-azure-toolkit-for-intellij"></a>Convertir des applications IntelliJ IDEA existantes pour qu’elles utilisent HDInsight Tools dans le kit de ressources Azure pour IntelliJ
Vous pouvez également convertir vos applications Spark Scala existantes créées dans IntelliJ IDEA pour qu’elles soient compatibles avec HDInsight Tools dans le kit de ressources Azure pour IntelliJ. Cette opération vous permet d’utiliser l’outil pour envoyer les applications à un cluster HDInsight Spark. Pour ce faire, vous pouvez procéder comme suit :

1. Pour une application Spark Scala existante créée à l’aide d’IntelliJ IDEA, ouvrez le fichier .iml associé.
2. Au niveau de la racine se trouve un élément **module** comme celui-ci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
3. Ajoutez `UniqueKey="HDInsightTool"` à la fin de l’élément **module** , qui doit alors ressembler à ceci :
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
4. Enregistrez les modifications. Votre application doit maintenant être compatible avec HDInsight Tools dans le kit de ressources Azure pour IntelliJ. Vous pouvez effectuer un test en cliquant avec le bouton droit sur le nom du projet dans l’explorateur de projets. Le menu contextuel doit désormais comporter l’option **Submit Spark Application to HDInsight**(Envoyer l’application Spark à HDInsight).

## <a name="troubleshooting"></a>Résolution de problèmes
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>Erreur « Please use a larger heap size » (Veuillez utiliser un tas de plus grande taille) pour l’exécution locale
Dans Spark 1.6, si vous utilisez un kit de développement logiciel (SDK) Java 32 bits au cours de l’exécution locale, vous pouvez rencontrer les erreurs suivantes :

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
        at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
        at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
        at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
        at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
        at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
        at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
        at LogQuery$.main(LogQuery.scala:53)
        at LogQuery.main(LogQuery.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

Cette erreur est due au fait que le tas n’est pas suffisamment grand pour permettre l’exécution, dans la mesure où Spark nécessite au moins 471 Mo (vous pouvez obtenir plus d’informations dans l’article [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) si vous le souhaitez). Une solution simple consiste à utiliser un kit de développement logiciel (SDK) Java 64 bits. Vous pouvez également modifier les paramètres de la JVM dans IntelliJ en ajoutant les options suivantes :

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Résultat de l’exécution locale de l’application Spark](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback--known-issues"></a>Commentaires et problèmes connus
Pour l’instant, la visualisation directe des sorties Spark n’est pas prise en charge. Nous travaillons à la résolution de ce problème.

Si vous avez des suggestions ou des commentaires, ou que vous rencontrez des problèmes lors de l’utilisation de cet outil, n’hésitez pas à nous envoyer un e-mail à l’adresse hdivstool@microsoft.com.

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

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
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Dec16_HO1-->


