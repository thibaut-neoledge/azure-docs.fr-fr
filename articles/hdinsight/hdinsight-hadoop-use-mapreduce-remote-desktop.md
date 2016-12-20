---
title: "MapReduce et le Bureau à distance avec Hadoop dans HDInsight | Microsoft Docs"
description: "Apprenez à utiliser le Bureau à distance pour vous connecter à Hadoop sur HDInsight et exécuter des tâches MapReduce."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 9d3a7b34-7def-4c2e-bb6c-52682d30dee8
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: a79f10cfb0ca882b00eb4a969fbf1d397ebdcf2f


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight-with-remote-desktop"></a>Utilisation de MapReduce dans Hadoop sur HDInsight avec le Bureau à distance
[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à vous connecter à un Hadoop sur le cluster HDInsight en utilisant le Bureau à distance et exécuter les tâches MapReduce à l’aide de la commande Hadoop.

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Configuration requise
Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Windows (Hadoop sur HDInsight)
* Un ordinateur client avec Windows 10, Windows 8 ou Windows 7

## <a name="a-idconnectaconnect-with-remote-desktop"></a><a id="connect"></a>Connexion avec le Bureau à distance
Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Se connecter à des clusters HDInsight avec RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a name="a-idhadoopause-the-hadoop-command"></a><a id="hadoop"></a>Utilisation de la commande Hadoop
Une fois connecté au bureau pour le cluster HDInsight, procédez comme suit pour exécuter une tâche MapReduce à l’aide de la commande Hadoop :

1. À partir du bureau HDInsight, démarrez la **ligne de commande Hadoop**. Cela ouvre une nouvelle invite de commandes dans le répertoire **c:\apps\dist\hadoop-&lt;numéro de version>**.

   > [!NOTE]
   > Le numéro de version change à mesure que Hadoop est mis à jour. La variable d’environnement **HADOOP_HOME** peut être utilisée pour rechercher le chemin d’accès. Par exemple, `cd %HADOOP_HOME%` permet de basculer vers le répertoire Hadoop sans qu'il soit nécessaire de connaître le numéro de version.
   >
   >
2. Pour utiliser la commande **Hadoop** pour exécuter une tâche MapReduce d’exemple, utilisez la commande suivante :

        hadoop jar hadoop-mapreduce-examples.jar wordcount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/WordCountOutput

    Cela lance la classe **wordcount**, contenue dans le fichier **hadoop-mapreduce-examples.jar** du répertoire actif. En tant qu’entrée, elle utilise le document **wasb://example/data/gutenberg/davinci.txt** et la sortie est stockée dans **wasb:///example/data/WordCountOutput**.

   > [!NOTE]
   > Pour plus d'informations sur cette tâche MapReduce et sur les exemples de données, consultez <a href="hdinsight-use-mapreduce.md">Utilisation de MapReduce dans HDInsight Hadoop</a>.
   >
   >
3. La tâche émet des informations lors de son traitement, avant de renvoyer des informations semblables aux suivantes lorsqu’elle est terminée.

        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623
4. Une fois la tâche terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockés sur **wasb://example/data/WordCountOutput** :

        hadoop fs -ls wasbs:///example/data/WordCountOutput

    Cela devrait afficher deux fichiers, **_SUCCESS** et **part-r-00000**. Le fichier **part-r-00000** contient la sortie de cette tâche.

   > [!NOTE]
   > Certaines tâches MapReduce peuvent fractionner les résultats sur plusieurs fichiers **part-r-#####** . Dans ce cas, utilisez le suffixe ##### pour indiquer l’ordre des fichiers.
   >
   >
5. Pour afficher la sortie, utilisez la commande suivante :

        hadoop fs -cat wasbs:///example/data/WordCountOutput/part-r-00000

    Cela affiche une liste de mots contenus dans le fichier **wasb://example/data/gutenberg/davinci.txt**, ainsi que le nombre d’occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Résumé
Comme vous pouvez le voir, la commande Hadoop fournit un moyen facile pour exécuter des tâches MapReduce sur un cluster HDInsight avant d’afficher le résultat de la tâche.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)



<!--HONumber=Nov16_HO3-->


