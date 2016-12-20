---
title: "Créer une application Scala autonome à exécuter sur un cluster HDInsight Spark | Microsoft Docs"
description: "Apprenez à créer une application Spark autonome à exécuter sur un cluster HDInsight Spark."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7b4348fbc77c2682001c8b18ddf7918dbb66872b


---
# <a name="create-a-standalone-scala-application-to-run-on-apache-spark-cluster-on-hdinsight-linux"></a>Création d’une application Scala autonome à exécuter sur un cluster Apache Spark sur HDInsight Linux
Cet article fournit des instructions détaillées pour le développement d'applications Spark autonomes écrites en Scala à l'aide de Maven avec IntelliJ IDEA. Dans cet article, Apache Maven est utilisé comme système de génération. Le document présente dans un premier temps un archétype Maven existant pour Scala fourni par IntelliJ IDEA.  La création d'une application Scala avec IntelliJ IDEA implique généralement les étapes suivantes :

* Utiliser Maven en tant que le système de génération.
* Mettre à jour le fichier de modèle objet du projet (POM) pour résoudre les dépendances de module Spark.
* Écrire votre application dans Scala.
* Générer un fichier jar qui peut être soumis à des clusters HDInsight Spark.
* Exécuter l’application à distance sur le cluster Spark à l’aide de Livy.

> [!NOTE]
> HDInsight propose également un outil de plug-in IntelliJ IDEA pour faciliter le processus de création et d’envoi des applications à un cluster HDInsight Spark sous Linux. Pour plus d’informations, consultez [Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA afin de créer et d’envoyer des applications Spark](hdinsight-apache-spark-intellij-tool-plugin.md).
> 
> 

**Configuration requise**

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir d’ [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Java. Cet article utilise IntelliJ IDEA 15.0.1. Vous pouvez l’installer à partir d’ [ici](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installer le plug-in Scala pour IntelliJ IDEA
Si l’installation d’IntelliJ IDEA ne vous invite pas à activer le plug-in Scala, lancez IntelliJ IDEA et parcourez les étapes suivantes pour installer le plug-in :

1. Démarrez IntelliJ IDEA, puis à partir de l’écran d’accueil, cliquez sur **Configurer**, puis sur **Plug-ins**.
   
    ![Activer le plug-in Scala](./media/hdinsight-apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Dans l'écran suivant, cliquez sur **Installer le plug-in JetBrains** dans le coin inférieur gauche. Dans la boîte de dialogue **Parcourir les plug-ins JetBrains** qui s’ouvre, recherchez Scala, puis cliquez sur **Installer**.
   
    ![Installer le plug-in Scala](./media/hdinsight-apache-spark-create-standalone-application/install-scala-plugin.png)
3. Une fois que le plug-in est correctement installé, cliquez sur bouton **Redémarrer IntelliJ IDEA** pour redémarrer l'IDE.

## <a name="create-a-standalone-scala-project"></a>Créer un programme Scala autonome
1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.
   
    ![Créer un projet Maven](./media/hdinsight-apache-spark-create-standalone-application/create-maven-project.png)
   
   * Sélectionnez **Maven** comme type de projet.
   * Spécifiez un **projet SDK**. Cliquez sur Nouveau, puis accédez au répertoire d'installation Java, généralement `C:\Program Files\Java\jdk1.8.0_66`.
   * Sélectionnez l’option **Créer à partir d'un archétype** .
   * Dans la liste des archétypes, sélectionnez **org.scala-tools.archetypes:scala-archetype-simple**. Cela créera la structure de répertoire appropriée et téléchargera les dépendances requises par défaut pour écrire le programme Scala.
2. Indiquez les valeurs appropriées pour **GroupId**, **ArtifactId** et **Version**. Cliquez sur **Next**.
3. Dans la boîte de dialogue suivante, où vous spécifiez le répertoire de base Maven et d'autres paramètres utilisateur, acceptez les valeurs par défaut et cliquez sur **Suivant**.
4. Dans la dernière boîte de dialogue, spécifiez un nom de projet et un emplacement, puis cliquez sur **Terminer**.
5. Supprimez le fichier **MySpec.Scala** dans **src\test\scala\com\microsoft\spark\example**. Ce fichier est inutile pour l'application.
6. Si nécessaire, renommez les fichiers source et de test nommés par défaut. Dans le volet gauche d’IntelliJ IDEA, accédez à **src\main\scala\com.microsoft.spark.example**. Cliquez avec le bouton droit sur **App.scala**, puis cliquez sur **Refactoriser** et sur Renommer un fichier. Dans la boîte de dialogue, indiquez le nouveau nom de l’application, puis cliquez sur **Refactoriser**.
   
    ![Renommer les fichiers](./media/hdinsight-apache-spark-create-standalone-application/rename-scala-files.png)  
7. Dans les étapes suivantes, vous allez mettre à jour le fichier pom.xml pour définir les dépendances de l'application Spark Scala. Ces dépendances sont téléchargées et résolues automatiquement, c’est pourquoi vous devez configurer Maven en conséquence.
   
    ![Configurer Maven pour les téléchargements automatiques](./media/hdinsight-apache-spark-create-standalone-application/configure-maven.png)
   
   1. Dans le menu **Fichier**, cliquez sur **Paramètres**.
   2. Dans la boîte de dialogue **Paramètres**, accédez à **Génération, exécution, déploiement** > **Outils de génération** > **Maven** > **Importation**.
   3. Sélectionnez l'option **Importer les projets Maven automatiquement**.
   4. Cliquez sur **Appliquer**, puis sur **OK**.
8. Mettez à jour le fichier source Scala pour inclure le code de votre application. Ouvrez et remplacez le code existant par le code suivant et enregistrez les modifications. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la sixième colonne et écrit la sortie dans **/HVACOut** , sous le conteneur de stockage par défaut du cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACout")
          }
        }
9. Mettez à jour le fichier pom.xml.
   
   1. Ajoutez le bloc de code suivant à `<project>\<properties>` :
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Ajoutez le bloc de code suivant à `<project>\<dependencies>` :
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Enregistrez les modifications apportées au fichier pom.xml.
10. Créez le fichier .jar IntelliJ IDEA permet de créer le fichier JAR en tant qu’artefact d'un projet. Procédez comme suit.
    
    1. Dans le menu **Fichier**, cliquez sur **Structure de projet**.
    2. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Artefacts**, puis sur le signe plus. Dans la boîte de dialogue contextuelle, cliquez sur **JAR**, puis sur **À partir de modules ayant des dépendances**.
       
        ![Créer un fichier jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-1.png)
    3. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, cliquez sur le bouton de sélection (![ellipse](./media/hdinsight-apache-spark-create-standalone-application/ellipsis.png)) en regard de **Classe principale**.
    4. Dans la boîte de dialogue **Sélectionner une classe principale**, sélectionnez la classe qui s’affiche par défaut, puis cliquez sur **OK**.
       
        ![Créer un fichier jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-2.png)
    5. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, assurez-vous que l’option **Extract to the target JAR** (Extraire vers le fichier JAR cible) est activée, puis cliquez sur **OK**. Cela crée un fichier JAR contenant toutes les dépendances.
       
        ![Créer un fichier jar](./media/hdinsight-apache-spark-create-standalone-application/create-jar-3.png)
    6. L’onglet Disposition de la sortie répertorie tous les fichiers JAR inclus dans le cadre du projet Maven. Vous pouvez sélectionner et supprimer ceux sur lesquels l’application Scala n’a aucune dépendance directe. Pour l’application que nous créons ici, vous pouvez tous les supprimer sauf le dernier (**SparkSimpleApp compile output**). Sélectionnez les fichiers JAR à supprimer, puis cliquez sur l’icône **Delete** .
       
        ![Créer un fichier jar](./media/hdinsight-apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assurez-vous que la case à cocher **Générer à la création** est activée, ce qui garantit la création du fichier JAR à chaque génération ou mise à jour du projet. Cliquez sur **Appliquer**, puis sur **OK**.
    7. Dans la barre de menus, cliquez sur **Générer**, puis sur **Créer le projet**. Vous pouvez également cliquer sur **Générer les artefacts** pour créer le fichier JAR. Le fichier JAR de sortie est créé sous **\out\artifacts**.
       
        ![Créer un fichier jar](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Exécuter l’application sur le cluster Spark
Pour exécuter l'application sur le cluster, procédez comme suit :

* **Copiez le fichier JAR d'application sur l'objet blob Azure Storage** associé au cluster. Pour ce faire, vous pouvez utiliser l’utilitaire de ligne de commande [**AzCopy**](../storage/storage-use-azcopy.md). De nombreux autres clients permettent également de télécharger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](hdinsight-upload-data.md).
* **Utilisez Livy pour soumettre une tâche de l'application à distance** au cluster Spark. Les clusters Spark sur HDInsight incluent Livy qui expose des points de terminaison REST permettant d’envoyer à distance des travaux Spark. Pour plus d’informations, consultez [Envoi de travaux Spark à distance en utilisant Livy avec les clusters Spark sur HDInsight](hdinsight-apache-spark-livy-rest-interface.md).

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Nov16_HO3-->


