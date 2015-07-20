<properties
   pageTitle="MapReduce et la connexion SSH avec Hadoop dans HDInsight | Microsoft Azure"
   description="Apprenez à utiliser le protocole SSH pour exécuter des tâches MapReduce avec Hadoop sur HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Utilisation de MapReduce avec Hadoop sur HDInsight avec SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à utiliser le protocole SSH (Secure Shell) pour vous connecter à un Hadoop sur le cluster HDInsight, puis soumettre des tâches MapReduce à l’aide des commandes Hadoop.

> [AZURE.NOTE]Si vous êtes déjà familiarisé avec l’utilisation de serveurs Hadoop sur Linux, mais pas avec HDInsight, consultez la rubrique [Informations sur l’utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Configuration requise

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Linux (Hadoop sur HDInsight)

* Un client SSH. Les systèmes d’exploitation Mac, Linux et Unix doivent être accompagnés d’un client SSH. Les utilisateurs Windows doivent télécharger un client, comme [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Connexion avec SSH

Connectez-vous au nom de domaine complet de votre cluster HDInsight à l’aide de la commande SSH. Le nom de domaine complet est le nom attribué au cluster, suivi de **.azurehdinsight.net**. Par exemple, la commande suivante permettrait de se connecter à un cluster nommé **myhdinsight** :

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez peut-être spécifier l’emplacement de la clé privée sur votre système client, par exemple :

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l’authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez la rubrique [Utilisation de SSH avec Hadoop dans HDInsight sur Linux à partir de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clients Windows)

Windows ne fournit pas de client SSH intégré. Nous vous recommandons d’utiliser **PuTTY**, qui peut être téléchargé à partir de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Pour plus d’informations sur l’utilisation de PuTTY, consultez la rubrique [Utilisation de SSH avec Hadoop Linux dans HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Utilisation de commandes Hadoop

1. Une fois connecté au cluster HDInsight, utilisez la commande **Hadoop** suivante afin de lancer une tâche MapReduce :

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Cela lance la classe **wordcount**, contenue dans le fichier **hadoop-mapreduce-examples.jar**. Comme entrée, elle utilise le document **wasb://example/data/gutenberg/davinci.txt** et la sortie est stockée dans : **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE]Pour plus d’informations sur cette tâche MapReduce et sur les exemples de données, consultez la rubrique [Utilisation de MapReduce sur Hadoop sur HDInsight](hdinsight-use-mapreduce.md).

2. La tâche émet des informations lors de son traitement, avant de renvoyer des informations semblables aux suivantes lorsqu’elle est terminée.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Lorsque la tâche est terminée, utilisez la commande suivante pour répertorier les fichiers de sortie stockés sur **wasb://example/data/WordCountOutput** :

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Cela devrait afficher deux fichiers, **_SUCCESS** et **part-r-00000**. Le fichier **part-r-00000** contient la sortie de cette tâche.

	> [AZURE.NOTE]Certaines tâches MapReduce peuvent fractionner les résultats sur plusieurs fichiers **part-r-#####**. Dans ce cas, utilisez le suffixe ##### pour indiquer l’ordre des fichiers.

4. Pour afficher la sortie, utilisez la commande suivante :

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Cela affiche une liste des mots contenus dans le fichier **wasb://example/data/gutenberg/davinci.txt**, ainsi que le nombre d'occurrences de chaque mot. Voici un exemple des données contenues dans le fichier :

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, les commandes Hadoop fournissent un moyen facile pour exécuter des tâches MapReduce sur un cluster HDInsight avant d’afficher le résultat de la tâche.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur les tâches MapReduce dans HDInsight :

* [Utilisation de MapReduce dans Hadoop HDInsight](hdinsight-use-mapreduce.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)

<!---HONumber=July15_HO2-->