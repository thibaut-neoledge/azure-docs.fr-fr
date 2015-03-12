<properties
   pageTitle="MapReduce avec Hadoop dans HDInsight | Azure"
   description="Apprenez à utiliser le Bureau à distance pour vous connecter à Hadoop sur HDInsight et exécuter des tâches MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Utilisation de MapReduce dans Hadoop sur HDInsight avec le Bureau à distance

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à vous connecter à un Hadoop sur le cluster HDInsight en utilisant le Bureau à distance et exécuter les tâches MapReduce à l'aide de la commande Hadoop.

##<a id="prereq"></a>Conditions préalables

Pour réaliser les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Windows (Hadoop sur HDInsight)

* un client Windows 7, 8 ou 10

##<a id="connect"></a>une connexion de Bureau à distance

Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous en suivant les instructions sur la page <a href="http://azure.microsoft.com/ documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l'aide de RDP</a>.

##<a id="hadoop"></a>Utilisez la commande Hadoop

Une fois connecté au bureau pour le cluster HDInsight, procédez comme suit pour exécuter une tâche MapReduce à l'aide de la commande Hadoop.

1. À partir du bureau HDInsight, démarrez la **ligne de commande Hadoop**. Cela ouvrira une nouvelle invite de commandes dans le répertoire **c:\apps\dist\hadoop-&lt;numéro de version>**.

	> [AZURE.NOTE] Le numéro de version change à mesure que Hadoop est mis à jour. La variable d'environnement **HADOOP_HOME** peut être utilisée pour rechercher le chemin d'accès. Par exemple, `cd % HADOOP_HOME%` changera les répertoires dans le répertoire Hadoop, sans avoir à connaître le numéro de version.

2. Pour utiliser la commande **Hadoop** commande pour exécuter une tâche MapReduce d'exemple, utilisez le code suivant.

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Cela lance la classe **wordcount**, contenue dans le fichier **hadoop-mapreduce-examples.jar** du répertoire actif. En tant qu'entrée, elle utilise le document **wasb://example/data/gutenberg/davinci.txt** et la sortie est stockée dans **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] pour plus d'informations sur cette tâche MapReduce et les données d'exemple, consultez <a href="../hdinsight-use-mapreduce/">Utilisation de MapReduce dans Hadoop HDInsight</a>.

2. La tâche émettra des informations lors de son traitement, avant de renvoyer des d'informations semblables aux suivantes lorsqu'elle est terminée.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Une fois la tâche terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockés sur **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Cela devrait afficher deux fichiers, **_SUCCESS** et **part-r-00000**. Le fichier **part-r-00000** contient la sortie de cette tâche.

	> [AZURE.NOTE] Certaines tâches MapReduce peuvent fractionner les résultats sur plusieurs fichiers **part-r-#####**. Dans ce cas, utilisez le suffixe ##### pour indiquer l'ordre des fichiers.

4. Pour afficher la sortie, utilisez la commande suivante.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Cela affichera une liste de mots contenus dans le fichier **wasb://example/data/gutenberg/davinci.txt**, ainsi que le nombre d'occurrences de chaque mot.  Voici un exemple des données contenues dans le fichier.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Hadoop fournit un moyen facile pour exécuter des tâches MapReduce sur un cluster HDInsight avant d'afficher le résultat de la tâche.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight.

* [Utilisation de MapReduce dans Hadoop HDInsight](../hdinsight-use-mapreduce/)

Pour plus d'informations sur d'autres méthodes de travail avec Hadoop sur HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](../hdinsight-use-hive/)

* [Utilisation de Pig avec Hadoop sur HDInsight](../hdinsight-use-pig/)
<!--HONumber=45--> 
