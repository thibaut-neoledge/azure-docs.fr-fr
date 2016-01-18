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
	ms.date="01/04/2016" 
	ms.author="nitinme"/>


# Machine Learning : analyse prédictive des données d’inspections alimentaires à l’aide de MLlib avec Spark sous HDInsight (Linux)

> [AZURE.TIP]Ce didacticiel est également disponible en tant que bloc-notes Jupyter sur un cluster Spark (Linux) que vous créez dans HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour lire le didacticiel au sein d’un bloc-notes, créez un cluster Spark, lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), puis exécutez le bloc-notes **Spark Machine Learning - Analyse prédictive des données d’inspections alimentaires MLLib .ipynb** dans le dossier **Python**.


Cet article explique comment utiliser **MLLib**, les bibliothèques Machine Learning intégrées de Spark, pour réaliser une analyse prédictive simple sur un jeu de données ouvertes. MLLib est une bibliothèque Spark principale qui fournit un certain nombre d’utilitaires qui sont utiles pour les tâches Machine Learning, y compris les utilitaires qui conviennent pour :

* Classification

* Régression

* Clustering

* Modélisation de rubrique

* Décomposition de valeur singulière (SVD) et analyse des composants principaux (PCA)

* Hypothèse de test et de calcul des exemples de statistiques

Cet article présente une approche simple de *classification* via la régression logistique.

## Qu’est-ce que la classification et la régression logistique ?

La *classification*, une tâche Machine Learning très courante, correspond au classement des données d’entrée dans des catégories. L’algorithme de classification doit déterminer comment attribuer les « étiquettes » aux données d’entrée que vous fournissez. Par exemple, vous pouvez utiliser un algorithme Machine Learning qui accepte les informations de stock en tant qu’entrée et divise le stock en deux catégories : ce qu’il faut vendre et ce qu’il faut conserver.

La régression logistique correspond à l’algorithme que vous utilisez pour la classification. L’API de régression logistique de Spark est utile pour la *classification binaire* ou pour classer les données d’entrée dans un des deux groupes. Pour plus d’informations sur la régression logistique, consultez [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En résumé, le processus de régression logistique génère une *fonction logistique* qui peut être utilisée pour prédire la probabilité qu’un vecteur d’entrée appartienne à un groupe ou à l’autre.

## Quel est l’objectif des actions de cet article ?

Spark vous permet d’effectuer une analyse prédictive sur les données d’inspection alimentaires (**Food\_Inspections1.csv**) qui ont été acquises par le biais du [portail de données de la ville de Chicago](https://data.cityofchicago.org/). Ce jeu de données contient des informations sur les inspections de produits alimentaires qui ont été effectuées à Chicago, y compris des informations sur chaque installation alimentaire qui a été inspectée, sur les violations détectées (le cas échéant) et sur les résultats de l’inspection.

Dans la procédure ci-dessous, vous développez un modèle pour voir ce qui est nécessaire à la réussite ou à l’échec d’une inspection de produits alimentaires.

## Création d’une application Machine Learning à l’aide de Spark MLlib

1. Dans le tableau d’accueil du [portail Azure en version préliminaire](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglée au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

	> [AZURE.NOTE]Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez __CLUSTERNAME__ par le nom de votre cluster.
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **Python 2**.

	![Créer un bloc-notes Jupyter](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Créer un bloc-notes Jupyter")

3. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.

	![Fournir un nom pour le bloc-notes](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Fournir un nom pour le bloc-notes")

3. Commencez à créer votre application d’apprentissage automatique. Vous devez commencer par configurer l’environnement Pyspark. Pour ce faire, placez le curseur dans la cellule, puis appuyez sur **MAJ + ENTRÉE**.


		import pyspark
		from pyspark import SparkConf
		from pyspark import SparkContext
		from pyspark.sql import SQLContext
		%matplotlib inline
		import matplotlib.pyplot as plt
		from pyspark.ml import Pipeline
		from pyspark.ml.classification import LogisticRegression
		from pyspark.ml.feature import HashingTF, Tokenizer
		from pyspark.sql import Row
		from pyspark.sql.functions import UserDefinedFunction
		from pyspark.sql.types import *
		import atexit
		
		sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
		sqlContext = SQLContext(sc)
		atexit.register(lambda: sc.stop())


## Construire une trame de données d’entrée

Nous disposons déjà d’un SQLContext que nous pouvons utiliser pour effectuer des transformations sur des données structurées. La première tâche consiste à charger les exemples de données ((**Food\_Inspections1.csv**)) dans une *trame de données* SQL Spark. Les extraits de code ci-dessous supposent que les données sont déjà téléchargées vers le conteneur de stockage par défaut associé au cluster Spark.

1. Étant donné que les données brutes sont au format CSV, nous devons utiliser le contexte Spark pour extraire chaque ligne du fichier en mémoire sous forme de texte non structuré. Ensuite, utilisez la bibliothèque CSV de Python pour analyser chaque ligne individuellement. 


		def csvParse(s):
		    import csv
		    from StringIO import StringIO
		    sio = StringIO(s)
		    value = csv.reader(sio).next()
		    sio.close()
		    return value
		
		inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
		                .map(csvParse)


2. Nous avons maintenant le fichier CSV sous forme de RDD. Extrayons une ligne du RDD pour comprendre le schéma de données.


		inspections.take(1)


	Un résultat similaire à ce qui suit s’affiche normalement :

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		[['413707',
	      'LUNA PARK INC',
	      'LUNA PARK  DAY CARE',
	      '2049789',
	      "Children's Services Facility",
	      'Risk 1 (High)',
	      '3250 W FOSTER AVE ',
	      'CHICAGO',
	      'IL',
	      '60625',
	      '09/21/2010',
	      'License-Task Force',
	      'Fail',
	      '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
	      '41.97583445690982',
	      '-87.7107455232781',
	      '(41.97583445690982, -87.7107455232781)']]


3. Le résultat ci-dessus nous donne une idée du schéma du fichier d’entrée. Le fichier inclut le nom de chaque établissement, le type de l’établissement, l’adresse, les données des inspections et l’emplacement, entre autres choses. Nous allons sélectionner quelques colonnes qui seront utiles pour notre analyse prédictive et regrouper les résultats dans une trame de données.


		schema = StructType([
		        StructField("id", IntegerType(), False), 
		        StructField("name", StringType(), False), 
		        StructField("results", StringType(), False), 
		        StructField("violations", StringType(), True)])
		
		df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)

4. Nous avons maintenant une *trame de données*, `df` sur laquelle nous pouvons effectuer l’analyse. Nous avons inclus 4 colonnes d’intérêt dans la trame de données : **id**, **nom**, **résultats** et **violations**. Prenons un petit échantillon de données :


		df.show(5)

	Un résultat similaire à ce qui suit s’affiche normalement :

	    # -----------------
		# THIS IS AN OUTPUT
		# -----------------

		+------+--------------------+-------+--------------------+
	    |    id|                name|results|          violations|
	    +------+--------------------+-------+--------------------+
	    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
	    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
	    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
	    |413708|BENCHMARK HOSPITA...|   Pass|                    |
	    |413722|           JJ BURGER|   Pass|                    |
	    +------+--------------------+-------+--------------------+

## Comprendre les données

Commençons par nous faire une idée de ce que notre jeu de données contient. Par exemple, quelles sont les différentes valeurs dans la colonne **résultats** ?


	df.select('results').distinct().show()

	
Un résultat similaire à ce qui suit s’affiche normalement :

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	+--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    
Un aperçu rapide peut nous aider à examiner la distribution de ces résultats.

	countResults = df.groupBy('results').count().collect()
	labels = [row.results for row in countResults]
	sizes = [row.count for row in countResults]
	colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


Un résultat similaire à ce qui suit s’affiche normalement :

    
![Résultats](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


Vous pouvez voir qu’il existe 5 résultats d’inspection distincts.

* Entreprise introuvable 
* Échec
* Réussite
* Réussite avec conditions
* Cessation d’activités 

Développons un modèle capable de deviner le résultat d’une inspection alimentaire, en fonction des violations. Étant donné que la régression logistique est une méthode de classification binaire, il est judicieux de regrouper les données en deux catégories : **échec** et **réussite**. Une « Réussite avec conditions » reste une Réussite. Par conséquent, lorsque nous formons le modèle, nous devons considérer ces deux résultats au même titre. Les données contenant les autres résultats (« Entreprise introuvable », « Cessation d’activités ») ne sont pas utiles. Nous pouvons donc les supprimer de notre jeu d’apprentissage. Cela ne devrait pas poser de problème, puisque ces deux catégories ne représentent qu’un faible pourcentage du résultat total.

À présent, convertissons notre trame de données existante (`df`) en une nouvelle trame de données dans laquelle chaque inspection est représentée par une paire de violations étiquetée. Dans notre cas, une étiquette `0.0` représente un échec, une étiquette `1.0` représente une réussite et une étiquette `-1.0` représente d’autres résultats. Nous filtrerons ces autres résultats lors du calcul de la nouvelle trame de données.


	def labelForResults(s):
	    if s == 'Fail':
	        return 0.0
	    elif s == 'Pass w/ Conditions' or s == 'Pass':
	        return 1.0
	    else:
	        return -1.0
	label = UserDefinedFunction(labelForResults, DoubleType())
	labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


Récupérons une ligne de données étiquetées pour voir à quoi elle ressemble.


	labeledData.take(1)


Un résultat similaire à ce qui suit s’affiche normalement :

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	[Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## Créer un modèle de régression logistique à partir de la trame de données d’entrée

La dernière tâche consiste à convertir les données étiquetées dans un format qui peut être analysé par régression logistique. L’entrée dans un algorithme de régression logistique doit correspondre à un jeu de *paires de vecteur de fonctionnalité d’étiquette*, où « vecteur de fonctionnalité » est un vecteur de nombres qui représente le point d’entrée d’une certaine façon. Nous devons donc trouver un moyen de convertir la colonne « violations », qui est semi-structurée et qui contient un grand nombre de commentaires en texte libre, en un tableau de nombres réels qu’une machine peut facilement comprendre.

Une approche Machine Learning standard pour le traitement du langage naturel consiste à assigner un « index » à chaque mot distinct et à transmettre un vecteur à l’algorithme de Machine Learning, de manière à ce que la valeur de chaque index contienne la fréquence relative de ce mot dans la chaîne de texte.

MLLib permet d’effectuer cette opération en toute simplicité. Tout d’abord, nous allons « tokéniser » chaque chaîne de violations pour obtenir les mots individuels dans chaque chaîne, puis nous allons utiliser un `HashingTF` pour convertir chaque jeu de jetons dans un vecteur de fonctionnalité qui peut ensuite être transmis à l’algorithme de régression logistique pour construire un modèle. Nous allons effectuer toutes ces étapes de manière séquentielle, à l’aide d’un « pipeline ».


	tokenizer = Tokenizer(inputCol="violations", outputCol="words")
	hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
	lr = LogisticRegression(maxIter=10, regParam=0.01)
	pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
	
	model = pipeline.fit(labeledData)


## Évaluer le modèle sur un jeu de données de test séparé

Nous pouvons utiliser le modèle que nous avons créé précédemment pour *prédire* les résultats des nouvelles inspections, en fonction des violations qui ont été observées. Nous avons formé ce modèle sur le jeu de données **Food\_Inspections1.csv**. Utilisons un deuxième jeu de données, **Food\_Inspections2.csv**, pour *évaluer* la puissance de ce modèle sur les nouvelles données. Ce deuxième jeu de données (**Food\_Inspections2.csv**) doit être déjà dans le conteneur de stockage par défaut associé au cluster.

L’extrait de code ci-dessous crée une nouvelle trame de données, **predictionsDf**, qui contient la prédiction générée par le modèle.


	testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
	             .map(csvParse) \
	             .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
	testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
	predictionsDf = model.transform(testDf)
	predictionsDf.columns


Un résultat similaire à ce qui suit s’affiche normalement :

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------
	
	['id',
     'name',
     'results',
     'violations',
     'words',
     'features',
     'rawPrediction',
     'probability',
     'prediction']

Observez une des prédictions. Exécutez cet extrait de code :

	predictionsDf.take(1)

Vous verrez la prédiction pour la première entrée dans le jeu de données de test.

La méthode `model.transform()` applique la même transformation à toutes les nouvelles données possédant le même schéma, pour obtenir une prédiction permettant de classer les données. Nous pouvons faire des statistiques simples pour avoir une idée de la précision de nos prédictions :


	numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
	                                      (prediction = 1 AND (results = 'Pass' OR 
	                                                           results = 'Pass w/ Conditions'))""").count()
	numInspections = predictionsDf.count()
	
	print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
	print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

La sortie a l'aspect suivant :

    # -----------------
	# THIS IS AN OUTPUT
	# -----------------

	There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate


À l’aide de la régression logistique, Spark fournit un modèle précis de la relation entre les descriptions des violations en anglais et indique si une entreprise données échoue ou réussit l’inspection alimentaire. Nous pouvons créer une visualisation finale pour nous aider à examiner les résultats de ce test :

	
	failSuccess = predictionsDf.where("prediction = 0 AND results = 'Fail'").count()
	failFailure = predictionsDf.where("prediction = 0 AND results <> 'Fail'").count()
	passSuccess = predictionsDf.where("prediction = 1 AND results <> 'Fail'").count()
	passFailure = predictionsDf.where("prediction = 1 AND results = 'Fail'").count()
	labels = ['True positive', 'False positive', 'True negative', 'False negative']
	sizes = [failSuccess, failFailure, passSuccess, passFailure]
	plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
	plt.axis('equal')


La sortie suivante s'affiche.

![Prédictions](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


Dans ce graphique, un résultat « positif » fait référence à l’inspection de produits alimentaires ayant échoué, tandis qu’un résultat négatif fait référence à une inspection réussie. Cela correspond (à peu près) à un taux de faux négatifs de l’ordre de 12,6 % et un taux de 16,0 % de faux positifs.

## Arrêtez le bloc-notes

Une fois l’exécution de l’application terminée, arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.


## <a name="seealso"></a>Voir aussi


* [Vue d’ensemble : Apache Spark sur Azure HDInsight](hdinsight-apache-spark-overview.md)

### Scénarios

* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Streaming Spark : utilisez Spark dans HDInsight pour créer des applications de streaming en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Créer et exécuter des applications

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Exécuter des tâches à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)

### Extensions

* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gérer des ressources

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0107_2016-->