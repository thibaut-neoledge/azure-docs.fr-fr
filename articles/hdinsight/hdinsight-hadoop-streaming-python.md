<properties
   pageTitle="Développement de travaux MapReduce Python avec HDInsight | Microsoft Aure"
   description="Découvrez comment créer et exécuter des tâches MapReduce Python sur des clusters HDInsight Linux."
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
   ms.date="05/19/2015"
   ms.author="larryfr"/>

#Développement de programmes de diffusion en continu Python pour HDInsight

Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d'écrire des fonctions de mappage et de réduction dans d'autres langages que Java. Cet article explique comment utiliser Python pour effectuer des opérations MapReduce.

> [AZURE.NOTE]Il est basé sur des informations et des exemples publiés par Michael Noll à l’adresse [http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/](Rédaction d’un programme MapReduce Hadoop dans Python).

##Composants requis

Pour effectuer les étapes présentées dans cet article, vous avez besoin des éléments suivants :

* Un cluster Hadoop Linux sur HDInsight

* Un éditeur de texte

* Pour les clients Windows, PuTTY et PSCP. Ces utilitaires sont disponibles à partir de la <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Page de téléchargement de PuTTY</a>.

##Nombre de mots

Pour cet exemple, vous implémenterez un nombre de mots de base à l’aide d’un mappeur et d’un raccord de réduction. Le mappeur sépare les phrases en mots individuels, et le raccord de réduction rassemble les mots et les nombres pour produire la sortie.

L’organigramme suivant illustre ce qui se passe durant les phases de mappage et de réduction.

![illustration du mappage de réduction](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Pourquoi Python

Python est un langage de programmation d'usage général et de haut niveau, qui, comparé à de nombreux autres langages, vous permet d'exprimer des concepts avec beaucoup moins de lignes de code. Des données scientifiques l’ont récemment fait connaître en tant que langage de prototypage, du fait de sa nature interprétée, sa saisie dynamique et sa syntaxe élégante qui lui permettent de développer très rapidement des applications.

Python est installé sur tous les clusters HDInsight.

##Diffusion en continu de MapReduce

Hadoop vous permet de spécifier un fichier qui dispose de la logique de mappage et de réduction utilisée par un travail. Parmi les exigences spécifiques de mappage et de réduction, on retrouve les éléments suivants :

* **Entrée** : les composants de mappage et de réduction doivent lire les données d’entrée depuis STDIN.

* **Sortie** : les composants de mappage et de réduction doivent écrire les données de sortie vers STDOUT.

* **Format de données** : les données consommées et produites doivent représenter une paire clé/valeur, séparée par un caractère de tabulation.

Python peut facilement gérer ces exigences en utilisant le module **sys** pour lire depuis STDIN, et **print** pour imprimer vers STDOUT. Le travail restant consiste à disposer un caractère de tabulation (`\t`) entre la clé et la valeur pour vous permettre d’effectuer, si vous le souhaitez, le formatage de ces données.

##Création du mappeur et du raccord de réduction

Le mappeur et le raccord de réduction ne sont que des fichiers texte, ici **mapper.py** et **reducer.py**. De ce fait, nous savons exactement qui fait quoi. Vous pouvez les créer à l’aide de l’éditeur de votre choix.

###Mapper.py

Créez un fichier nommé **mapper.py** et utilisez le code suivant comme contenu :

	#!/usr/bin/env python

	# Use the sys module
	import sys

	# 'file' in this case is STDIN
	def read_input(file):
		# Split each line into words
		for line in file:
			yield line.split()

	def main(separator='\t'):
		# Read the data using read_input
		data = read_input(sys.stdin)
		# Process each words returned from read_input
		for words in data:
			# Process each word
			for word in words:
				# Write to STDOUT
				print '%s%s%d' % (word, separator, 1)

	if __name__ == "__main__":
		main()

Prenez quelques instants pour lire le code et comprendre à quoi il sert.

###Reducer.py

Créez un fichier nommé **reducer.py** et utilisez le code suivant comme contenu :

	#!/usr/bin/env python
	
	# import modules
	from itertools import groupby
	from operator import itemgetter
	import sys
	
	# 'file' in this case is STDIN
	def read_mapper_output(file, separator='\t'):
		# Go through each line
	    for line in file:
			# Strip out the separator character
	        yield line.rstrip().split(separator, 1)
	
	def main(separator='\t'):
	    # Read the data using read_mapper_output
	    data = read_mapper_output(sys.stdin, separator=separator)
		# Group words and counts into 'group'
		#   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
	    for current_word, group in groupby(data, itemgetter(0)):
	        try:
				# For each word, pull the count(s) for the word
				#   from 'group' and create a total count
	            total_count = sum(int(count) for current_word, count in group)
				# Write to stdout
	            print "%s%s%d" % (current_word, separator, total_count)
	        except ValueError:
	            # Count was not a number, so do nothing
	            pass
	
	if __name__ == "__main__":
	    main()

##Téléchargement des fichiers

Les deux fichiers **mapper.py** et **reducer.py** doivent se trouver sur le nœud principal du cluster pour pouvoir être exécutés. Pour les télécharger, nous vous recommandons d’utiliser **scp** (**pscp** si vous utilisez un client Windows).

À partir du client, dans le même répertoire que **mapper.py** et **reducer.py**, utilisez la commande suivante : Remplacez **username** par un utilisateur SSH et **clustername** par le nom de votre cluster.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

De cette façon, les fichiers du système local sont copiés dans le nœud principal.

> [AZURE.NOTE]Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d’accès à la clé privée, par exemple, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

##Exécution de MapReduce

1. Connectez-vous au cluster à l’aide de SSH :

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE]Si vous utilisez un mot de passe pour sécuriser votre compte SSH, vous serez invité à le saisir. Si vous utilisez une clé SSH, vous devrez peut-être utiliser le paramètre `-i` et le chemin d’accès à la clé privée, par exemple, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. Exécutez la commande suivante pour démarrer la tâche MapReduce :

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/davinci.txt -output wasb:///example/wordcountout

	Cette commande dispose des éléments suivants :

	* **hadoop-streaming.jar** : utilisé lors de l’exécution d’opérations de diffusion en contenu MapReduce. Il établit un lien entre Hadoop et le code externe MapReduce que vous fournissez
	
	* **-files** : indique à Hadoop que les fichiers spécifiés sont nécessaires pour effectuer cette tâche MapReduce, et qu’ils doivent être copiés sur tous les nœuds de travail.

	* **-mapper** : indique à Hadoop quel fichier doit être utilisé comme mappeur.

	* **-reducer** : indique à Hadoop quel fichier doit être utilisé comme raccord de réduction.

	* **-input** : le fichier d’entrée à partir duquel nous devrions compter les mots.

	* **-output** : le répertoire sur lequel la sortie sera écrite

		> [AZURE.NOTE]Ce répertoire sera créé par la tâche.

Vous devriez voir apparaître des instructions **INFO** à mesure que la tâche s’exécute, et des opérations de **mappage** et de **réduction** s’afficher sous forme de pourcentages.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Lorsque le travail se termine, vous recevez les informations sur son état.

##Affichage du résultat

Une fois le travail terminé, utilisez la commande suivante pour afficher le résultat :

	hadoop fs -text /example/wordcountout/part-00000

Celle-ci énumère les mots qui sont utilisés, et elle vous indique, par la même occasion, le nombre de fois où ils apparaissent. Voici un exemple de données de sortie :

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##Étapes suivantes

Maintenant que vous avez découvert comment utiliser des travaux de diffusion en continu MapReduce avec HDInsight, cliquez sur les liens suivants pour explorer d’autres façons d’utiliser Azure HDInsight.

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation des tâches MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->