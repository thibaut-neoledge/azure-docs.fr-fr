<properties
   pageTitle="MapReduce avec Hadoop dans HDInsight | Azure"
   description="Apprenez à utiliser le protocole SSH pour exécuter des tâches MapReduce avec Hadoop sur HDInsight."
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

# Utilisation de Hive avec Hadoop sur HDInsight avec SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à utiliser le protocole SSH pour vous connecter à un Hadoop sur le cluster HDInsight, puis soumettre des tâches MapReduce à l'aide de la commande Hadoop.

> [AZURE.NOTE] Si vous vous êtes déjà familiarisé avec l'utilisation de serveurs Hadoop sous Linux, mais que vous découvrez HDInsight, consultez <a href="../hdinsight-hadoop-linux-information/" target="_blank">Ce qu'il faut savoir sur Hadoop dans HDInsight sous Linux</a>.

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* un cluster HDInsight basé sur Linux (Hadoop sur HDInsight)

* un client SSH. Mac OS, Linux et Unix doivent être accompagnés d'un client SSH. Les utilisateurs Windows doivent télécharger un client, comme <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Connexion avec SSH

Connectez-vous au nom de domaine complet (FQDN) de votre cluster HDInsight à l'aide de la commande SSH. Le nom de domaine complet est le nom attribué au cluster, suivi de **.azurehdinsight.net**. Par exemple, les éléments suivants se connecteraient à un cluster nommé **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Si vous avez fourni une clé de certificat pour l'authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez spécifier l'emplacement de la clé privée sur votre système client.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Si vous avez fourni un mot de passe pour l'authentification SSH** lorsque vous avez créé le cluster HDInsight, vous devez fournir le mot de passe lorsque vous y êtes invité.

###Putty (clients Windows)

Windows ne fournit aucun client SSH intégré. Nous vous recommandons d'utiliser **Putty**, qui peut être téléchargé à partir de <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Pour plus d'informations sur l'utilisation de Putty, consultez la section **Utilisation de Putty pour se connecter à un ordinateur Linux** de la rubrique <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilisation de SSH avec Linux sur Azure</a>.

> [AZURE.NOTE] Si vous avez utilisé un certificat pour l'authentification SSH pour le cluster HDInsight, vous devez également consulter la section **Créer une clé privée pour Putty** de la rubrique <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Utilisation de SSH avec Linux sur Azure</a>.

##<a id="hadoop"></a>Utiliser la commande Hadoop

1. Une fois connecté au cluster HDInsight, utilisez les éléments suivants pour utiliser la commande **Hadoop** afin de lancer une tâche MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Cela lance la classe **wordcount**, contenue dans le fichier **hadoop-mapreduce-examples.jar**. En tant qu'entrée, elle utilise le document **wasb://example/data/gutenberg/davinci.txt** et la sortie est stockée dans **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Pour plus d'informations sur cette tâche MapReduce et sur les exemples de données, consultez <a href="../hdinsight-use-mapreduce/" target="_blank">Utilisation de MapReduce dans HDInsight Hadoop</a>.

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
<!--HONumber=47-->
