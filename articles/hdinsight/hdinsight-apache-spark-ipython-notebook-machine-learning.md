<properties 
	pageTitle="Utilisation d'Apache Spark pour créer des applications d'apprentissage automatique sur HDInsight | Microsoft Azure" 
	description="Instructions pas à pas sur l’utilisation de blocs-notes avec Apache Spark pour créer des applications d’apprentissage automatique" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2015" 
	ms.author="nitinme"/>


# Créer des applications Machine Learning à l’aide d’Apache Spark sur Azure HDInsight

Découvrez comment créer une application d’apprentissage automatique à l’aide d’un cluster Apache Spark dans HDInsight. Cet article montre comment utiliser le bloc-notes Jupyter disponible avec le cluster pour générer et tester notre application. L’application utilise l’exemple de données HVAC.csv, qui est disponible par défaut sur tous les clusters.

**Configuration requise :**

Vous devez disposer des éléments suivants :

- Un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Un cluster Apache Spark. Pour obtenir les instructions, consultez [Approvisionner les clusters Apache Spark dans Azure HDInsight](hdinsight-apache-spark-provision-clusters.md). 

##<a name="data"></a>Afficher les données

Avant de créer l’application, nous devons comprendre la structure des données et le type d’analyse que nous allons effectuer sur ces données.

Dans cet article, nous utilisons le fichier exemple de données **HVAC.csv**, qui est disponible par défaut sur tous les clusters HDInsight dans **\\HdiSamples\\SensorSampleData\\hvac**. Téléchargez et ouvrez le fichier CSV pour obtenir un instantané des données.

![HVAC, instantané des données](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.png "Instantané des données HVAC")

Les données indiquent la température cible et la température réelle d’un bâtiment dans lequel un système HVAC est installé. Supposons que la colonne **System** représente l’ID système et que la colonne **SystemAge** représente le nombre d’années écoulées depuis la mise en place du système HVAC dans le bâtiment.

Nous utilisons ces données pour prédire si un bâtiment sera plus chaud ou plus froid en fonction de la température cible, étant donné un ID système et une ancienneté du système.

##<a name="app"></a>Écrire une application d’apprentissage automatique à l’aide de Spark MLlib

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE]Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster :
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **Python 2**.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.CreateNotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

	![Fournir un nom pour le bloc-notes](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Note.Jupyter.Notebook.Name.png "Fournir un nom pour le bloc-notes")

3. Commencez à créer votre application d’apprentissage automatique. Dans cette application, nous utilisons un pipeline Spark ML pour effectuer une classification de documents. Dans le pipeline, nous fractionnons le document en mots, que nous convertissons en vecteur de fonctionnalité numérique avant de créer un modèle de prédiction utilisant les vecteurs et étiquettes de fonctionnalité.

	Pour créer l’application, commencez par importer les modules requis et affecter des ressources à l’application. Dans la cellule vide du nouveau bloc-notes, collez l’extrait suivant, puis appuyez sur **Maj + Entrée**.


		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row, SQLContext
		
		import os
		import sys
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		from pyspark.sql.types import *
		
		from pyspark.mllib.classification import LogisticRegressionWithSGD
		from pyspark.mllib.regression import LabeledPoint
		from numpy import array
		
		# Assign resources to the application
		conf = SparkConf()
		conf.setMaster('spark://headnodehost:7077')
		conf.setAppName('pysparkregression')
		conf.set("spark.cores.max", "4")
		conf.set("spark.executor.memory", "4g")
		
		sc = SparkContext(conf=conf)
		sqlContext = SQLContext(sc)

	À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **Python 2** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

	 ![État d’un travail de bloc-notes Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Job.Status.png "État d’un travail de bloc-notes Jupyter")
 
4. À présent, vous devez charger les données (hvac.csv), les analyser et les utiliser pour effectuer l’apprentissage du modèle. Pour ce faire, vous devez définir une fonction qui vérifie si la température réelle du bâtiment est supérieure à la température cible. Si la température réelle est supérieure, le bâtiment est chaud, ce qui est indiqué par la valeur **1.0**. Si la température réelle est inférieure, le bâtiment est froid, ce qui est indiqué par la valeur **0.0**.

	Collez l’extrait suivant dans une cellule vide, puis appuyez sur **Maj + Entrée**.

		
		# List the structure of data for better understanding. Becuase the data will be
		# loaded as an array, this structure makes it easy to understand what each element
		# in the array corresponds to

		# 0 Date
		# 1 Time
		# 2 TargetTemp
		# 3 ActualTemp
		# 4 System
		# 5 SystemAge
		# 6 BuildingID

		LabeledDocument = Row("BuildingID", "SystemInfo", "label")

		# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
		
		def parseDocument(line):
    		values = [str(x) for x in line.split(',')]
    		if (values[3] > values[2]):
        		hot = 1.0
    		else:
        		hot = 0.0        
    
    		textValue = str(values[4]) + " " + str(values[5])
    
    		return LabeledDocument((values[6]), textValue, hot)

		# Load the raw HVAC.csv file, parse it using the function
		data = sc.textFile("wasb:///HdiSamples/SensorSampleData/hvac/HVAC.csv")

		documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
		training = documents.toDF()


5. Configurez le pipeline Spark ML qui est composé de trois étapes : Tokenizer, HashingTF et LogisticRegression. Pour plus d’informations sur le pipeline et sur son fonctionnement, consultez le <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">pipeline d’apprentissage automatique Spark</a> (en anglais).

	Collez l’extrait suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

		tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
		hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
		lr = LogisticRegression(maxIter=10, regParam=0.01)
		pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajustez le pipeline au document d’apprentissage. Collez l’extrait suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

		model = pipeline.fit(training)

7. Vérifiez le document d’apprentissage pour contrôler votre progression dans l’application. Collez l’extrait suivant dans une cellule vide, puis appuyez sur **Maj + Entrée**.

		training.show()

	Le résultat doit ressembler à ce qui suit :

		BuildingID SystemInfo label
		4          13 20      0.0  
		17         3 20       0.0  
		18         17 20      1.0  
		15         2 23       0.0  
		3          16 9       1.0  
		4          13 28      0.0  
		2          12 24      0.0  
		16         20 26      1.0  
		9          16 9       1.0  
		12         6 5        0.0  
		15         10 17      1.0  
		7          2 11       0.0  
		15         14 2       1.0  
		6          3 2        0.0  
		20         19 22      0.0  
		8          19 11      0.0  
		6          15 7       0.0  
		13         12 5       0.0  
		4          8 22       0.0  
		7          17 5       0.0

	Revenez en arrière et vérifiez le résultat par rapport au fichier CSV brut. Par exemple, la première ligne du fichier CSV contient les données suivantes :

	![HVAC, instantané des données](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.ML.Show.Data.First.Row.png "Instantané des données HVAC")

	Notez que la température réelle est inférieure à la température cible, suggérant que le bâtiment est froid. De ce fait, dans le résultat, l’**étiquette** de la première ligne a pour valeur **0.0**, ce qui signifie que le bâtiment n’est pas chaud.

8.  Préparez un jeu de données sur lequel exécuter le modèle d’apprentissage. Pour ce faire, nous allons transmettre un ID système et une ancienneté du système (indiqués en tant que **SystemInfo** dans le résultat). Le modèle prédira si le bâtiment pourvu de cet ID système et de cette ancienneté du système sera plus chaud (indiqué par la valeur 1.0) ou plus froid (indiqué par la valeur 0.0).

	Collez l’extrait suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.
		
		# SystemInfo here is a combination of system ID followed by system age
		Document = Row("id", "SystemInfo")
		test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
    		.map(lambda x: Document(*x)).toDF() 

9. Pour finir, effectuez des prédictions sur les données de test. Collez l’extrait suivant dans une cellule vide, puis appuyez sur **Maj + Entrée**.

		# Make predictions on test documents and print columns of interest
		prediction = model.transform(test)
		selected = prediction.select("SystemInfo", "prediction", "probability")
		for row in selected.collect():
		    print row

10. Le résultat doit ressembler à ce qui suit :

		Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
		Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
		Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
		Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
		Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
		Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

	Dans la première ligne de la prédiction, vous pouvez voir que pour un système HVAC pourvu de l’ID 20 et d’une ancienneté du système de 25 ans, le bâtiment sera chaud (**prediction=1.0**). La première valeur de DenseVector (0.49999) correspond à la prédiction 0.0, et la seconde valeur (0.5001) correspond à la prédiction 1.0. Dans le résultat, même si la seconde valeur n’est que légèrement supérieure, le modèle indique **prediction=1.0**.

11. À présent, vous pouvez quitter le bloc-notes en redémarrant le noyau. Dans la barre de menus supérieure, cliquez successivement sur **Kernel**, **Restart** et **Restart** de nouveau quand vous y êtes invité.

	![Redémarrer le noyau Jupyter](./media/hdinsight-apache-spark-ipython-notebook-machine-learning/HDI.Spark.Jupyter.Restart.Kernel.png "Redémarrer le noyau Jupyter")
	  	   

##<a name="anaconda"></a>Utiliser la bibliothèque scikit-learn Anaconda pour l’apprentissage automatique

Les clusters Apache Spark sur HDInsight incluent des bibliothèques Anaconda, notamment la bibliothèque **scikit-learn** pour l’apprentissage automatique. Cette bibliothèque contient également différents jeux de données qui vous permettent de créer des exemples d’application directement à partir d’un bloc-notes Jupyter. Pour obtenir des exemples d’utilisation de la bibliothèque scikit-learn, reportez-vous à [http://scikit-learn.org/stable/auto\_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Voir aussi

* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Approvisionner un cluster Spark sur HDInsight](hdinsight-apache-spark-provision-clusters.md)
* [Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools.md)
* [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-storage]: ../hdinsight-use-blob-storage/


[hdinsight-weblogs-sample]: ../hdinsight-hive-analyze-website-log/
[hdinsight-sensor-data-sample]: ../hdinsight-hive-analyze-sensor-data/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_1203_2015-->