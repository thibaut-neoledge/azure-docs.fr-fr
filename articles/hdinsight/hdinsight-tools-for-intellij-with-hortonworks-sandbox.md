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
ms.date: 05/25/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: d252a4a8b811f966098348866cc498e2be2924f2
ms.contentlocale: fr-fr
ms.lasthandoff: 07/17/2017

---
#<a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox

Découvrez comment utiliser HDInsight Tools pour IntelliJ pour développer des applications Apache Scala et tester les applications sur [Hortonworks Sandbox](http://hortonworks.com/products/sandbox/) en cours d’exécution sur votre station de travail. [IntelliJ IDEA](https://www.jetbrains.com/idea/) est un environnement de développement Java intégré (IDE) pour développer des logiciels. Après avoir développé et testé vos applications sur Hortonworks Sandbox, vous pouvez les déplacer vers [Azure HDInsight](hdinsight-hadoop-introduction.md).

##<a name="prerequisites"></a>Composants requis

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- HDP 2.4 on Hortonworks Sandbox s’exécutant sur votre environnement local. Pour le configurer, consultez [Prise en main de l’écosystème Hadoop avec un bac à sable (sandbox) Hadoop sur une machine virtuelle](hdinsight-hadoop-emulator-get-started.md). Notez que HDInsight Tools pour IntelliJ ont seulement été testés avec HDP 2.4. À partir du [site de téléchargement Hortonworks Sandbox](http://hortonworks.com/downloads/#sandbox), développez **Hortonworks Sandbox Archive** pour l’obtenir.
- [Kit de développeur Java (JDK) version 1.8 ou ultérieure](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). JDK est requis par le Kit de ressources Azure pour IntelliJ.
- [Édition communautaire d’IntelliJ IDEA](https://www.jetbrains.com/idea/download) avec les plug-ins [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) et [Kit de ressources Azure pour IntelliJ](../azure-toolkit-for-intellij.md). HDInsight Tools pour IntelliJ est disponible dans le cadre du Kit de ressources Azure pour IntelliJ. 

  **Pour installer les plug-ins :**

  1. Ouvrez IntelliJ IDEA.
  2. À partir de l’écran d’accueil, cliquez sur **Configurer**, puis sur **Plug-ins**.
  3. Cliquez sur **Installer le plug-in JetBrains** dans le coin inférieur gauche.
  4. Utilisez la fonction de recherche pour rechercher **Scala**, puis cliquez sur **Installer**.
  5. Cliquez sur **Redémarrer IntelliJ IDEA** pour terminer l’installation.
  6. Répétez les étapes 4 et 5 pour installer le **Kit de ressources Azure pour IntelliJ**. Pour plus d’informations, consultez [Install the Azure Toolkit for IntelliJ](../azure-toolkit-for-intellij-installation.md)(Installer le Kit de ressources Azure pour IntelliJ).

## <a name="create-a-spark-scala-application"></a>Créer une application Spark Scala

Dans cette section, vous créez un exemple de projet Scala à l’aide d’IntelliJ IDEA. Dans la section suivante, vous lizr IntelliJ IDEA à Hortonworks Sandbox (émulateur) avant d’envoyer le projet.

1. Ouvrez IntelliJ IDEA à partir de votre station de travail.
2. Cliquez sur **Créer nouveau projet**.
3. Cliquez sur **HDInsight** dans le volet gauche, sur **Spark sur HDInsight(Scala)** dans le volet droit, puis sur **Suivant**.

    ![Créer le projet IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)
    - Outil de génération : l’Assistant de création de projets Scala prend en charge Maven ou SBT pour la gestion des dépendances et la génération des projets scala. Sélectionnez l’outil qui répond à vos besoins.
4. Entrez les informations suivantes :

    ![Créer les propriétés du projet IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)

    - **Nom du projet** : indiquez un nom de projet.
    - **Emplacement de projet** : indiquez un emplacement de projet.
    - **Kit de développement logiciel (SDK) de projet** : cliquez sur **Nouveau**, **JDK**, puis spécifiez le dossier du JDK Java version 7 ou ultérieure. Utilisez Java 1.8 pour un cluster spark 2.x et Java 1.7 pour un cluster spark 1.x. L’emplacement par défaut est C:\Program Files\Java\jdk1.8.x_xxx.
    - **Version Spark**: l’Assistant de création de projets Scala intègre la version correcte du Kit de développement logiciel (SDK) Spark et du Kit de développement logiciel (SDK) Scala. Si la version du cluster Spark est inférieure à la version 2.0, choisissez spark 1.x. Sinon, sélectionnez spark 2.x. Cet exemple utilise Spark 1.6.2 (Scala 2.10.5). Assurez-vous également que vous utilisez le référentiel identifié en tant que Scala 2.10.x (n’utilisez pas le référentiel identifié en tant que Scala 2.11.x).
5. Cliquez sur **Terminer**.
6. Appuyez sur **[ALT]+1** pour ouvrir la Vue du projet si ce n’est pas déjà fait.
7. À partir de l’**Explorateur de projets**, développez le projet, puis cliquez sur **src**.
8. Cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis cliquez sur **Scala class** (Classe Scala).
9. Entrez un nom, sélectionnez **Objet** dans **Type**, puis cliquez sur **OK**.

    ![Créer une nouvelle classe IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)
10. Dans le fichier.scala, collez le code suivant :

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

11. Dans le menu **Générer**, cliquez sur **Générer un projet**. Vérifiez que la compilation est terminée avec succès.


## <a name="link-to-the-hortonworks-sandbox"></a>Lien vers HortonWorks Sandbox

Vous devez disposer d’une application IntelliJ existante avant de pouvoir établir un lien vers un Hortonworks Sandbox (émulateur).

**Pour établir un lien vers un émulateur**

1. Ouvrez le projet dans IntelliJ si ce n’est pas déjà fait.
2. Dans le menu **Affichage**, cliquez sur **Fenêtres d’outil**, puis sur **Azure Explorer**.
3. Développez **Azure**, cliquez avec le bouton droit sur **HDInsight**, puis cliquez sur **Lier un émulateur**.
4. Entrez le mot de passe que vous avez configuré pour le compte racine du Hortonworks Sandbox et le reste des valeurs semblables à la capture d’écran suivante, puis cliquez sur **OK**. 

  ![Lien IntelliJ vers un émulateur](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Cliquez sur **Oui** pour configurer l’émulateur.

  Lorsqu’il est connecté avec succès, l’émulateur est affiché (Hortonworks Sandbox) répertorié sous le nœud HDInsight.

## <a name="submit-the-spark-scala-application-to-the-sandbox"></a>Envoyer l’application Spark Scala au bac à sable

Après avoir lié IntelliJ IDEA à l’émulateur, vous pouvez envoyer votre projet.

**Pour envoyer un projet à un émulateur**

1. Dans **l’Explorateur de projets**, cliquez avec le bouton droit sur le projet et cliquez sur **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).
2. Spécifiez les champs suivants :

    - **Cluster Spark (Linux uniquement)** : sélectionnez votre Hortonworks Sandbox local.
    - **Nom de la classe principale** : sélectionnez ou entrez le nom de la classe principale.  Pour ce didacticiel, il s’agit de **GroupByTest**.
3. Cliquez sur **Envoyer**. Les journaux d’envoi de travaux sont affichés dans la fenêtre d’outil d’envoi Spark.

## <a name="next-steps"></a>Étapes suivantes :

- Pour apprendre à créer des applications pour HDInsight à l’aide des outils HDInsight pour IntelliJ Spark, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster Linux HDInsight Spark](hdinsight-apache-spark-intellij-tool-plugin.md).
- Pour visionner une vidéo des outils HDInsight pour IntelliJ, consultez [Introduce HDInsight Tools for IntelliJ for Spark Development](https://www.youtube.com/watch?v=YTZzYVgut6c) (Présentation des outils HDInsight pour le développement d’IntelliJ pour Spark.
- Pour apprendre à déboguer des applications Spark en utilisant le kit de ressources à distance sur HDInsight via SSH, consultez [Déboguer des applications Spark à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md).
- Pour apprendre à déboguer des applications Spark en utilisant le kit de ressources à distance sur HDInsight via VPN, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance sur un cluster Linux Spark HDInsight](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).
- Pour savoir comment utiliser les outils HDInsight pour Eclipse pour créer l’application Spark, consultez [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](hdinsight-apache-spark-eclipse-tool-plugin.md).
- Pour visionner une vidéo de HDInsight Tools pour Eclipse, consultez [Use HDInsight Tool for Eclipse to create Spark applications](https://mix.office.com/watch/1rau2mopb6fha) (Utiliser HDInsight Tools pour Eclipse afin de créer des applications Spark).
