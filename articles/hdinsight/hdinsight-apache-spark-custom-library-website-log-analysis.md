<properties 
	pageTitle="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark pour analyser des journaux de site Web | Microsoft Azure" 
	description="Utiliser des bibliothèques personnalisées avec un cluster HDInsight Spark pour analyser des journaux de site Web" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="jhubbard" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="nitinme"/>

# Analyse des journaux de site web à l'aide d'une bibliothèque personnalisée avec un cluster Apache Spark sous HDInsight Linux

Ce bloc-notes montre comment analyser les données de journal à l'aide d'une bibliothèque personnalisée avec Spark sur HDInsight. La bibliothèque personnalisée que nous utilisons est une bibliothèque Python appelée **iislogparser.py**.

> [AZURE.TIP] Ce didacticiel est également disponible en tant que bloc-notes Jupyter sur un cluster Spark (Linux) que vous créez dans HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour effectuer le didacticiel au sein d’un bloc-notes, créez un cluster Spark, lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **Analyse des journaux avec Spark à l’aide d’une bibliothèque personnalisée au format .ipynb** sous le dossier **PySpark**.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark sur HDInsight Linux. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Enregistrer des données brutes en tant que RDD

Dans cette section, nous utilisons le bloc-notes [Jupyter](https://jupyter.org) associé à un cluster Apache Spark dans HDInsight pour exécuter les travaux traitant vos exemples de données brutes et les enregistrer dans une table Hive. L’exemple de données est un fichier .csv (hvac.csv), qui est disponible par défaut sur tous les clusters.

Une fois vos données enregistrées dans une table Hive, nous allons nous connecter, dans la prochaine section, à la table Hive à l’aide d’outils décisionnels comme Power BI et Tableau.

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE] Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

	![Fournir un nom pour le bloc-notes](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

4. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.


		from pyspark.sql import Row
		from pyspark.sql.types import *


5. Créez un RDD à l’aide de l’exemple de données de journal déjà disponible sur le cluster. Vous pouvez accéder aux données dans le compte de stockage par défaut associé au cluster dans **\\HdiSamples\\HdiSamples\\WebsiteLogSampleData\\SampleLog\\909f2b.log**.


		logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Récupérez un exemple de jeu de journal pour vérifier que l'étape précédente a été correctement effectuée.

		logs.take(5)

	Le résultat doit ressembler à ce qui suit :

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[u'#Software: Microsoft Internet Information Services 8.0',
		 u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
		 u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## Analysez les données de journal à l'aide d'une bibliothèque Python personnalisée

7. Dans la sortie ci-dessus, les premières lignes comprennent les informations d'en-tête et chaque ligne restante correspond au schéma décrit dans l'en-tête. L’analyse de tels journaux peut s’avérer complexe. Par conséquent, nous utilisons une bibliothèque Python personnalisée (**iislogparser.py**) qui facilite beaucoup l'analyse de tels journaux. Par défaut, cette bibliothèque est incluse avec votre cluster Spark sur HDInsight dans **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

	Toutefois, cette bibliothèque n'est pas dans le `PYTHONPATH`. Par conséquent, nous ne pouvons pas l'utiliser à l'aide d'une instruction d'importation telle que `import iislogparser`. Pour utiliser cette bibliothèque, nous devons la distribuer à tous les nœuds de travail. Exécutez l'extrait de code suivant.


		sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser` fournit une fonction `parse_log_line` qui retourne `None` si une ligne de journal est une ligne d'en-tête et retourne une instance de la classe `LogLine` si elle rencontre une ligne de journal. Utilisez la classe `LogLine` pour extraire uniquement les lignes de journal du RDD :

		def parse_line(l):
		    import iislogparser
		    return iislogparser.parse_log_line(l)
		logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Récupérez quelques lignes de journal extraites pour vérifier que l'étape a été correctement effectuée.

		logLines.take(2)

	Le résultat doit ressembler à ce qui suit :

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
 		2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. La classe `LogLine`, quant à elle, dispose de méthodes utiles, telles que `is_error()`, qui retourne si une entrée de journal présente un code d’erreur. Elle permet de calculer le nombre d'erreurs dans les lignes de journal extraites, puis de consigner toutes les erreurs dans un fichier différent.

		errors = logLines.filter(lambda p: p.is_error())
		numLines = logLines.count()
		numErrors = errors.count()
		print 'There are', numErrors, 'errors and', numLines, 'log entries'
		errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

	Un résultat similaire à ce qui suit s’affiche normalement :

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		There are 30 errors and 646 log entries

12. **Matplotlib** permet également de construire une visualisation des données. Par exemple, si vous souhaitez isoler la cause des requêtes qui s'exécutent pendant une longue période, il se peut que vous souhaitiez trouver les fichiers qui mettent le plus de temps à répondre en moyenne. L’extrait de code ci-dessous récupère les 25 ressources qui mettent le plus de temps à répondre à une requête.

		def avgTimeTakenByKey(rdd):
		    return rdd.combineByKey(lambda line: (line.time_taken, 1),
		                            lambda x, line: (x[0] + line.time_taken, x[1] + 1),
		                            lambda x, y: (x[0] + y[0], x[1] + y[1]))\
		              .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
		    
		avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

	Un résultat similaire à ce qui suit s’affiche normalement :

		# -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[(u'/blogposts/mvc4/step13.png', 197.5),
		 (u'/blogposts/mvc2/step10.jpg', 179.5),
		 (u'/blogposts/extractusercontrol/step5.png', 170.0),
		 (u'/blogposts/mvc4/step8.png', 159.0),
		 (u'/blogposts/mvcrouting/step22.jpg', 155.0),
		 (u'/blogposts/mvcrouting/step3.jpg', 152.0),
		 (u'/blogposts/linqsproc1/step16.jpg', 138.75),
		 (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
		 (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
		 (u'/blogposts/nested/step2.jpg', 126.0),
		 (u'/blogposts/adminpack/step1.png', 124.0),
		 (u'/BlogPosts/datalistpaging/step2.png', 118.0),
		 (u'/blogposts/mvc4/step35.png', 117.0),
		 (u'/blogposts/mvcrouting/step2.jpg', 116.5),
		 (u'/blogposts/aboutme/basketball.jpg', 109.0),
		 (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
		 (u'/blogposts/mvc4/step12.png', 106.0),
		 (u'/blogposts/linq8/step0.jpg', 105.5),
		 (u'/blogposts/mvc2/step18.jpg', 104.0),
		 (u'/blogposts/mvc2/step11.jpg', 104.0),
		 (u'/blogposts/mvcrouting/step1.jpg', 104.0),
		 (u'/blogposts/extractusercontrol/step1.png', 103.0),
		 (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
		 (u'/blogposts/mvcrouting/step21.jpg', 101.0),
		 (u'/blogposts/mvc4/step1.png', 98.0)]


13. Vous pouvez également présenter ces informations sous forme de diagramme. La première étape pour créer un tracé consiste à créer un tableau temporaire **AverageTime**. Le tableau regroupe les journaux par heure pour voir si des pics de latence inhabituels sont survenus à un moment donné.

		avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
		schema = StructType([StructField('Minutes', IntegerType(), True),
		                     StructField('Time', FloatType(), True)])
		                     
		avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
		avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Vous pouvez ensuite exécuter la requête SQL suivante pour obtenir tous les enregistrements du tableau **AverageTime**.

		%%sql -o averagetime
		SELECT * FROM AverageTime

	La méthode magique `%%sql` suivie par `-o averagetime` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (généralement le nœud principal du cluster). Le résultat est conservé sous forme d’un tableau de données [Pandas](http://pandas.pydata.org/) avec le nom spécifié **averagetime**.

	Un résultat similaire à ce qui suit s’affiche normalement :

	![Sortie de requête SQL](./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Sortie de requête SQL")

	Pour plus d'informations sur la méthode magique `%%sql`, ainsi que les autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les ordinateurs portables Jupyter avec clusters Spark HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Vous pouvez désormais utiliser Matplotlib, une bibliothèque permettant de construire une visualisation des données, pour créer un tracé. Étant donné que le tracé doit être créé à partir du tableau de données **averagetime** conservé localement, l'extrait de code doit commencer par la méthode magique `%%local`. Cela garantit l’exécution locale du code sur le serveur Jupyter.

		%%local
		%matplotlib inline
		import matplotlib.pyplot as plt
		
		plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
		plt.xlabel('Time (min)')
		plt.ylabel('Average time taken for request (ms)')

	Un résultat similaire à ce qui suit s’affiche normalement :

	![Sortie Matplotlib](./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Sortie Matplotlib")

16. Une fois que vous avez terminé l’exécution de l’application, vous devez arrêter le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.
	

## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scénarios

* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

### Création et exécution d’applications

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Outils et extensions

* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utiliser des packages externes avec les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Gestion des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight)](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->