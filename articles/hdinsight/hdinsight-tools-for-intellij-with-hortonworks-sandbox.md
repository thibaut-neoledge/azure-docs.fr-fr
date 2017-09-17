---
title: Utiliser le Kit de ressources Azure pour IntelliJ avec Hortonworks Sandbox | Microsoft Docs
description: "Découvrez comment utiliser HDInsight Tools dans le Kit de ressources Azure pour IntelliJ avec Hortonworks Sandbox."
keywords: "outils Hadoop, requête hive, intellij, hortonworks sandbox, kit de ressources azure pour intellij"
services: HDInsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: b587cc9b-a41a-49ac-998f-b54d6c0bdfe0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 5c42b3e519781fa25fe92b286fa65898a7aac173
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox

Découvrez comment utiliser HDInsight Tools pour IntelliJ pour développer des applications Apache Scala et tester les applications sur [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) en cours d’exécution sur votre station de travail. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) est un environnement de développement Java intégré (IDE) pour développer des logiciels. Après avoir développé et testé vos applications sur Hortonworks Sandbox, vous pouvez les déplacer vers [Azure HDInsight](hdinsight-hadoop-introduction.md).

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Hortonworks Data Platform (HDP) 2.4 on Hortonworks Sandbox s’exécutant sur votre environnement local. Pour configurer HDP, consultez [Prise en main de l’écosystème Hadoop avec un bac à sable (sandbox) Hadoop sur une machine virtuelle](hdinsight-hadoop-emulator-get-started.md). 
    >[!NOTE]
    >Les outils HDInsight pour IntelliJ ont seulement été testés avec HDP 2.4. Pour obtenir HDP 2.4, développez **Hortonworks Sandbox Archive** sur le [site de téléchargement Hortonworks Sandbox](http://hortonworks.com/downloads/#sandbox).

- [Kit de développeur Java (JDK) version 1.8 ou ultérieure](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK est requis par le Kit de ressources Azure pour IntelliJ.

- [Édition communautaire d’IntelliJ IDEA](https://www.jetbrains.com/idea/download) avec les plug-ins [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) et [Kit de ressources Azure pour IntelliJ](../azure-toolkit-for-intellij.md). HDInsight Tools pour IntelliJ est disponible dans le cadre du Kit de ressources Azure pour IntelliJ. 

  Pour installer les plug-ins, effectuez les actions suivantes :

  1. Ouvrez IntelliJ IDEA.
  2. Sur l’écran d’**accueil**, sélectionnez **Configurer**, puis **Plug-ins**.
  3. Sélectionnez **Installer le plug-in JetBrains** dans le coin inférieur gauche.
  4. Utilisez la fonction de recherche pour rechercher **Scala**, puis sélectionnez **Installer**.
  5. Sélectionnez **Redémarrer IntelliJ IDEA** pour terminer l’installation.
  6. Répétez les étapes 4 et 5 pour installer le **Kit de ressources Azure pour IntelliJ**. Pour plus d’informations, consultez [Install the Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md)(Installer le Kit de ressources Azure pour IntelliJ).

## <a name="create-a-spark-scala-application"></a>Créer une application Spark Scala

Dans cette section, vous créez un exemple de projet Scala à l’aide d’IntelliJ IDEA. Dans la section suivante, vous liez IntelliJ IDEA à Hortonworks Sandbox (émulateur) avant d’envoyer le projet.

1. Ouvrez IntelliJ IDEA à partir de votre station de travail. Dans la boîte de dialogue **New Project** (Nouveau projet), suivez ces étapes :

   a. Sélectionnez **HDInsight** > **Spark sur HDInsight (Scala)**.

   b. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

    * **Maven**, pour la prise en charge de l’Assistant de création de projets Scala
    * **SBT**, pour gérer les dépendances et la génération du projet Scala

   ![Boîte de dialogue Nouveau projet](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Sélectionnez **Suivant**.

3. Dans la boîte de dialogue suivante **New Project** (Nouveau projet), effectuez ces étapes :

    ![Créer les propriétés du projet IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    a. Dans la zone **Nom du projet**, entrez un nom de projet.

    b. Dans la zone **Emplacement du projet**, entrez un emplacement de projet.

    c. En regard de la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Nouveau**, **JDK**, puis spécifiez le dossier Java JDK version 1.7 ou ultérieure. Sélectionnez **Java 1.8** pour le cluster Spark 2.x, ou **Java 1.7** pour le cluster Spark 1.x. L’emplacement par défaut est C:\Program Files\Java\jdk1.8.x_xxx.

    d. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le kit de développement logiciel Spark et le kit de développement logiciel Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. La version utilisée dans cet exemple est Spark 1.6.2 (Scala 2.10.5). Assurez-vous que vous utilisez le référentiel marqué Scala 2.10.x. N’utilisez pas le référentiel marqué Scala 2.11.x.

4. Sélectionnez **Terminer**.

5. Si la vue **Projet** n’est pas déjà ouverte, appuyez sur **Alt+1** pour l’ouvrir.

6. Dans l’**Explorateur de projets**, développez le projet, puis sélectionnez **src**.

7. Cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis sélectionnez **Scala class** (Classe Scala).

8. Entrez un nom dans la zone **Name**, et dans la zone **Kind** (Type), sélectionnez **Object** (Objet), puis **OK**.

    ![Fenêtre Créer une classe Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. Dans le fichier.scala, collez le code suivant :

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. Dans le menu **Générer**, sélectionnez **Générer un projet**. Vérifiez que la compilation a été terminée avec succès.


## <a name="link-to-the-hortonworks-sandbox"></a>Lien vers Hortonworks Sandbox

Vous devez disposer d’une application IntelliJ existante avant de pouvoir établir un lien vers un Hortonworks Sandbox (émulateur).

Pour établir un lien vers un émulateur, effectuez les étapes suivantes :

1. Ouvrez le projet dans IntelliJ.

2. Dans le menu **Affichage**, sélectionnez **Fenêtres d’outil**, puis **Azure Explorer**.

3. Développez **Azure**, cliquez avec le bouton droit sur **HDInsight**, puis sélectionnez **Lier un émulateur**.

4. Dans la fenêtre **Link A New Emulator** (Lier un nouvel émulateur), saisissez le mot de passe que vous avez configuré pour le compte racine du Hortonworks Sandbox, puis entrez des valeurs semblables à celles de la capture d’écran ci-dessous et sélectionnez **OK**. 

   ![Fenêtre « Link a New Emulator » (Lier un nouvel émulateur)](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Sélectionnez **Oui** pour configurer l’émulateur.

Lorsque l’il est connecté avec succès, l’émulateur (Hortonworks Sandbox) est répertorié sous le nœud HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Envoyer l’application Spark Scala au Hortonworks Sandbox

Après avoir lié IntelliJ IDEA à l’émulateur, vous pouvez envoyer votre projet.

Pour envoyer un projet à un émulateur, procédez comme suit :

1. Dans l’**Explorateur de projets**, cliquez avec le bouton droit sur le nom du projet et sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).

2. Effectuez les actions suivantes :

    a. Dans la liste déroulante **Cluster Spark (Linux uniquement)**, sélectionnez votre Hortonworks Sandbox local.

    b. Dans la zone **Nom de la classe principale**, sélectionnez ou entrez le nom de la classe principale. Pour ce didacticiel, il s’agit de **GroupByTest**.

3. Sélectionnez **Envoyer**. Les journaux d’envoi de travaux sont affichés dans la fenêtre d’outil d’envoi Spark.

## <a name="next-steps"></a>Étapes suivantes

- Pour apprendre à créer des applications pour HDInsight à l’aide des outils HDInsight pour IntelliJ Spark, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster Linux HDInsight Spark](hdinsight-apache-spark-intellij-tool-plugin.md).

- Pour visionner une vidéo des outils HDInsight pour IntelliJ, consultez [Introduce HDInsight Tools for IntelliJ for Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Présentation des outils HDInsight pour le développement d’IntelliJ pour Spark.

- Pour apprendre à déboguer des applications Spark en utilisant le kit de ressources à distance sur HDInsight via SSH, consultez [Déboguer des applications Spark à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Pour apprendre à déboguer des applications Spark en utilisant le kit de ressources à distance sur HDInsight via VPN, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance sur un cluster Linux Spark HDInsight](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Pour savoir comment utiliser les outils HDInsight pour Eclipse pour créer l’application Spark, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).

- Pour visionner une vidéo de HDInsight Tools pour Eclipse, consultez [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Utiliser HDInsight Tools pour Eclipse afin de créer des applications Spark).

