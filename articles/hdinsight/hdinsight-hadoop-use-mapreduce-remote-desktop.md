<properties
   pageTitle="MapReduce et le Bureau à distance avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser le Bureau à distance pour vous connecter à Hadoop sur HDInsight et exécuter des tâches MapReduce."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/22/2016"
   ms.author="larryfr"/>

# Utilisation de MapReduce dans Hadoop sur HDInsight avec le Bureau à distance

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à vous connecter à un Hadoop sur le cluster HDInsight en utilisant le Bureau à distance et exécuter les tâches MapReduce à l’aide de la commande Hadoop.

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Windows (Hadoop sur HDInsight)

* Un ordinateur client avec Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Connexion avec le Bureau à distance

Activez le Bureau à distance pour le cluster HDInsight, puis connectez-vous à lui en suivant les instructions fournies dans [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="hadoop"></a>Utilisation de la commande Hadoop

Une fois connecté au bureau pour le cluster HDInsight, procédez comme suit pour exécuter une tâche MapReduce à l’aide de la commande Hadoop :

1. À partir du bureau HDInsight, démarrez la **ligne de commande Hadoop**. Cela ouvre une nouvelle invite de commandes dans le répertoire **c:\\apps\\dist\\hadoop-&lt;numéro de version>**.

	> [AZURE.NOTE] Le numéro de version change à mesure que Hadoop est mis à jour. La variable d’environnement **HADOOP\_HOME** peut être utilisée pour rechercher le chemin d’accès. Par exemple, `cd %HADOOP_HOME%` permet de basculer vers le répertoire Hadoop sans qu'il soit nécessaire de connaître le numéro de version.

2. Pour utiliser la commande **Hadoop** pour exécuter une tâche MapReduce d’exemple, utilisez la commande suivante :

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Cela lance la classe **wordcount**, contenue dans le fichier **hadoop-mapreduce-examples.jar** du répertoire actif. Comme entrée, elle utilise le document ****wasb://example/data/gutenberg/davinci.txt** et la sortie est stockée dans : ****wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Pour plus d'informations sur cette tâche MapReduce et sur les exemples de données, consultez <a href="hdinsight-use-mapreduce.md">Utilisation de MapReduce dans HDInsight Hadoop</a>.

2. La tâche émet des informations lors de son traitement, avant de renvoyer des informations semblables aux suivantes lorsqu’elle est terminée.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Lorsque la tâche est terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockés sur ****wasb://example/data/WordCountOutput** :

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Cela devrait afficher deux fichiers, **\_SUCCESS** et **part-r-00000**. Le fichier **part-r-00000** contient la sortie de cette tâche.

	> [AZURE.NOTE] Certaines tâches MapReduce peuvent fractionner les résultats sur plusieurs fichiers **part-r-#####**. Dans ce cas, utilisez le suffixe ##### pour indiquer l’ordre des fichiers.

4. Pour afficher la sortie, utilisez la commande suivante :

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Cela affiche une liste des mots contenus dans le fichier ****wasb://example/data/gutenberg/davinci.txt**, ainsi que le nombre d'occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande Hadoop fournit un moyen facile pour exécuter des tâches MapReduce sur un cluster HDInsight avant d’afficher le résultat de la tâche.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0427_2016-->