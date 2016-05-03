<properties
	pageTitle="Exploration et modélisation de données avec Spark | Microsoft Azure"
	description="Présente les fonctionnalités de modélisation et d’exploration de données du kit d’outils Spark MLlib."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="deguhath;bradsev" />

# Exploration et modélisation des données avec Spark

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

## Introduction 

Cette procédure utilise HDInsight Spark pour effectuer l’exploration des données ainsi que les tâches de classification binaire et de modélisation de la régression sur un échantillon du jeu de données NYC Taxi Trip and Fare 2013. Elle vous guide tout au long des étapes du [processus de science des données](http://aka.ms/datascienceprocess), à l’aide d’un cluster HDInsight Spark pour le traitement et d’objets blob Azure pour stocker les données et les modèles. Le processus explore et visualise les données importées à partir d’un objet blob Azure Storage, puis prépare les données pour créer des modèles prédictifs. Ces modèles sont créés à l’aide de la boîte à outils Spark MLlib pour effectuer des tâches de classification binaire et de modélisation de régression.

- La **classification binaire** consiste à prédire si le trajet va faire l’objet d’un pourboire. 
- La tâche de **régression** consiste à prédire le montant du pourboire en fonction d’autres critères. 

Les étapes de modélisation contiennent également du code déterminant comment former, évaluer et enregistrer chaque type de modèle. Python a été utilisé pour coder la solution et montrer les tracés correspondants.

Les modèles que nous utilisons incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree) :

- La [régression linéaire avec SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) est un modèle de régression linéaire qui utilise la méthode SGD (Stochastic Gradient Descent), l’optimisation et la mise à l’échelle des caractéristiques pour prédire le montant des pourboires payés. 
- La [régression logistique avec LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS), ou régression « logit », est un modèle de régression qui s’utilise lorsque la variable dépendante est catégorielle, pour la classification des données. LBFGS est un algorithme d’optimisation de Quasi-Newton qui correspond approximativement à l’algorithme BFGS (Broyden–Fletcher–Goldfarb–Shanno) avec une quantité limitée de mémoire informatique et qui est largement utilisé dans l’apprentissage automatique (Machine Learning).
- Les [forêts aléatoires](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sont des ensembles d’arbres de décision. Elles combinent plusieurs arbres de décision pour réduire le risque de sur-ajustement. Utilisées pour la régression et la classification, les forêts aléatoires gèrent les caractéristiques catégorielles, prennent en compte le paramètre de classification multiclasse, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Les forêts aléatoires constituent l’un des modèles Machine Learning les plus performants pour la classification et la régression.
- Les arbres GBT ([Gradient Boosting Tree](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)) sont des ensembles d’arbres de décision. Ils aident les arbres de décision à minimiser itérativement une fonction de perte. Utilisés pour la régression et la classification, les arbres GBT gèrent les caractéristiques catégorielles, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Ils s’utilisent également dans le paramétrage de classification multiclasse.


>REMARQUE AZURE : Bien que la boîte à outils Spark MLlib soit conçue pour des jeux de données volumineux, nous utilisons par souci de commodité un échantillon relativement petit (environ 30 Mo sur 170 000 lignes, soit 0,1 % du jeu de données NYC d’origine) afin d’illustrer ses fonctionnalités de modélisation. Le cas de figure présenté ici fonctionne efficacement sur un cluster HDInsight à 2 nœuds de travail (en 10 minutes environ). Le même code permet de traiter des jeux de données plus volumineux, avec quelques modifications mineures concernant la mise en cache des données dans la mémoire ou l’adaptation de la taille du cluster.

## Composants requis

Vous avez besoin d’un compte Azure et d’un cluster HDInsight Spark pour commencer cette procédure. Consultez [Vue d’ensemble de la science des données utilisant Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md) pour en savoir plus sur ces exigences, pour obtenir une description des données NYC 2013 Taxi utilisées ici et pour savoir comment exécuter du code à partir d’un notebook Jupyter sur le cluster Spark. Le notebook **machine-learning-data-science-spark-data-exploration-modeling.ipynb** contenant les exemples de code de cette rubrique est disponible dans [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Python).


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Configuration : emplacements de stockage, bibliothèques et contexte Spark

Spark peut lire et écrire dans Azure Blob Storage (également appelé WASB). Donc, vos données stockées dedans sont exploitables par Spark et les résultats peuvent être stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut associé au cluster Spark peut être référencé à l’aide d’un chemin commençant par wasb///. Les autres emplacements sont référencés par wasb://.

Le programme d’installation requiert également l’importation des bibliothèques nécessaires et la définition du contexte Spark.

### Définir les chemins d’accès aux emplacements de stockage dans WASB

L’exemple de code suivant spécifie l’emplacement des données à lire et le chemin d’accès au répertoire de stockage dans lequel la sortie du modèle sera enregistrée.


	# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

	# LOCATION OF TRAINING DATA
	taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

	# SET THE MODEL STORAGE DIRECTORY PATH 
	# Note that the final backslash in the path is needed.
	modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### Importer les bibliothèques nécessaires et définir le contexte Spark 

Définissez le contexte Spark et importez les bibliothèques nécessaires à l’aide du code suivant.


	# IMPORT LIBRARIES
	import pyspark
	from pyspark import SparkConf
	from pyspark import SparkContext
	from pyspark.sql import SQLContext
	%matplotlib inline
	import matplotlib
	import matplotlib.pyplot as plt
	from pyspark.sql import Row
	from pyspark.sql.functions import UserDefinedFunction
	from pyspark.sql.types import *
	import atexit
	from numpy import array
	import numpy as np
	import datetime
	datetime.datetime.now()
	
	# SET SPARK CONTEXT
	sc = SparkContext(conf=SparkConf().setMaster('yarn-client'))
	sqlContext = SQLContext(sc)
	atexit.register(lambda: sc.stop())
	
	sc.defaultParallelism

**Output:**

4


## Ingestion de données

Cette section contient le code d’une série de tâches nécessaires à l’ingestion de l’échantillon de données à modéliser. Lisez un échantillon de 0,1 % du fichier de trajets et de tarifs de taxi (stocké dans un fichier TSV), formatez et nettoyez les données, créez et mettez en cache une trame de données en mémoire, puis enregistrez-le en tant que table temporaire dans le contexte SQL.

La première étape du processus de science des données consiste à recevoir les données à partir de sources ou de systèmes externes, et à les analyser dans votre environnement de modélisation et d’exploration de données. Dans cette procédure, cet environnement est Spark. Cette section contient le code permettant d’effectuer une série de tâches :

- recevoir l’échantillon de données à modéliser
- lire le jeu de données en entrée (stocké dans un fichier TSV)
- formater et nettoyer les données
- créer et mettre en cache des objets (RDD ou trames de données) en mémoire
- enregistrer les données en tant que table temporaire dans le contexte SQL.

Voici le code pour l’ingestion de données.

	# INGEST DATA

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# IMPORT FILE FROM PUBLIC BLOB
	taxi_train_file = sc.textFile(taxi_train_file_loc)
	
	# GET SCHEMA OF THE FILE FROM HEADER
	schema_string = taxi_train_file.first()
	fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
	fields[7].dataType = IntegerType() #Pickup hour
	fields[8].dataType = IntegerType() # Pickup week
	fields[9].dataType = IntegerType() # Weekday
	fields[10].dataType = IntegerType() # Passenger count
	fields[11].dataType = FloatType() # Trip time in secs
	fields[12].dataType = FloatType() # Trip distance
	fields[19].dataType = FloatType() # Fare amount
	fields[20].dataType = FloatType() # Surcharge
	fields[21].dataType = FloatType() # Mta_tax
	fields[22].dataType = FloatType() # Tip amount
	fields[23].dataType = FloatType() # Tolls amount
	fields[24].dataType = FloatType() # Total amount
	fields[25].dataType = IntegerType() # Tipped or not
	fields[26].dataType = IntegerType() # Tip class
	taxi_schema = StructType(fields)
	
	# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
	taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
	taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
	        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
	                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
	                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
	
	    
	# CREATE DATA FRAME
	taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
	    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
	    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
	    .drop('direct_distance').drop('surcharge')\
	    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
	
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Durée d’exécution de la cellule ci-dessus : 19,8 secondes


## Exploration et visualisation de données

Une fois les données intégrées dans Spark, l’étape suivante du processus de science des données consiste à mieux comprendre les données par l’exploration et la visualisation. Dans cette section, nous examinons les données des taxis à l’aide de requêtes SQL et traçons les variables cibles et les caractéristiques prospectives à vérifier visuellement. Plus précisément, nous traçons la fréquence des nombres de passagers dans les trajets en taxi, la fréquence des montants des pourboires et la variation des pourboires par type et par montant.

### Tracer un histogramme des fréquences de nombres de passagers dans l’échantillon des courses de taxi

Ce code utilise une requête SQL pour échantillonner les données et convertit les résultats en une trame de données Pandas à tracer.

	# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

	# SQL SQUERY
	sqlStatement = """
	    SELECT passenger_count, COUNT(*) as trip_counts 
	    FROM taxi_train 
	    WHERE passenger_count > 0 and passenger_count < 7
	    GROUP BY passenger_count 
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	#CONVERT TO PANDAS DATA FRAME FOR PLOTTING IN PYTHON
	resultsPDDF = sqlResults.toPandas()
	
	# PLOT PASSENGER NUMBER VS. TRIP COUNTS
	x_labels = resultsPDDF['passenger_count'].values
	fig = resultsPDDF[['trip_counts']].plot(kind='bar', facecolor='lightblue')
	fig.set_xticklabels(x_labels)
	fig.set_title('Frequency of trips by passenger count')
	plt.show()

**Output:**

![Fréquence des voyages par nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

	
### Tracez un histogramme du montant des pourboires et montrez comment le montant du pourboire varie selon le type et le montant du paiement.

Ce code utilise une requête SQL pour échantillonner les données et convertit les résultats en une trame de données Pandas à tracer.

	#PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# SQL SQUERY
	sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	sqlResults = sqlContext.sql(sqlStatement)
	
	#CONVERT TO PANDAS DATA FRAME FOR PLOTTING IN PYTHON
	resultsPDDF= sqlResults.toPandas()
	
	# HISTOGRAM OF TIP AMOUNTS
	ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY PASSENGER COUNT
	ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 120, -2, 30])
	plt.show()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**Output:**

![Distribution des montants de pourboire](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Montant de pourboire par nombre de passagers](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Montant du pourboire par montant de la course](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

Durée d’exécution de la cellule ci-dessus : 10,61 secondes


## Conception des caractéristiques, transformation et préparation des données à modéliser
Cette section décrit et fournit le code des procédures servant à préparer les données à utiliser dans la modélisation ML. Elle montre comment effectuer les opérations suivantes :

- Créer une caractéristique en regroupant les heures dans des périodes de trafic
- Indexer et encoder des fonctionnalités catégorielles
- Créer des objets point étiquetés à intégrer dans les fonctions ML
- Créer un sous-échantillonnage aléatoire des données et le diviser en un jeu de formation et un jeu de test
- Mise à l’échelle des caractéristiques
- Mettre en cache des objets en mémoire


### Créer une caractéristique en regroupant les heures dans des périodes de trafic

Ce code montre comment créer une nouvelle caractéristique en regroupant les heures dans des périodes de trafic et comment mettre en cache la trame de données obtenue en mémoire. Là où les jeux de données distribués résilients (RDD) et les trames de données sont utilisés de manière répétitive, la mise en cache réduit les temps d’exécution. Par conséquent, nous mettons en cache les RDD et les trames de données à plusieurs stades de la procédure.

	## CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	## CACHE DATA FRAME IN MEMORY & MATERIALIZE IT IN MEMORY
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()

**Output:**

126050

### Indexer et encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation

Cette section montre comment indexer ou encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation. Les fonctions de modélisation et de prédiction de MLlib requièrent des caractéristiques avec des données d’entrée catégorielles à indexer ou à encoder avant leur utilisation. Selon le modèle, vous devez les indexer ou les encoder différemment :

- La **modélisation arborescente** requiert l’encodage des catégories en tant que valeurs numériques (par exemple, une caractéristique avec 3 catégories peut être encodée par 0, 1, 2). C’est ce que permet la fonction [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) de MLlib. Cette fonction encode une colonne de libellés en une colonne d’index de libellé, classés par fréquence de libellé. Bien qu’indexés par des valeurs numériques pour le traitement des entrées et des données, les algorithmes arborescents peuvent être configurés pour les traiter comme des catégories. 

- Les **modèles de régression logistique et linéaire** requièrent un encodage linéaire où, par exemple, une fonction avec 3 catégories peut être développée en 3 colonnes de caractéristiques, chacune contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) permettant d’effectuer un encodage linéaire. Cet encodeur mappe une colonne d’index de libellé à une colonne de vecteurs binaires, contenant au plus une seule une valeur. Cet encodage autorise les algorithmes qui appliquent des caractéristiques numériques continues, comme la régression logistique, à des caractéristiques catégorielles.

Voici le code permettant d’indexer et d’encoder des caractéristiques catégorielles :


	# INDEX AND ENCODE CATEGORICAL FEATURES

	# RECORD START TIME
	timestart = datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES	
	from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
	
	# INDEX AND ENCODE VENDOR_ID
	stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
	model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
	indexed = model.transform(taxi_df_train_with_newFeatures)
	encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
	encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
	model = stringIndexer.fit(encoded1)
	indexed = model.transform(encoded1)
	encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
	encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
	model = stringIndexer.fit(encoded2)
	indexed = model.transform(encoded2)
	encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
	encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
	model = stringIndexer.fit(encoded3)
	indexed = model.transform(encoded3)
	encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
	encodedFinal = encoder.transform(indexed)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Durée d’exécution de la cellule ci-dessus : 1,42 seconde

### Créer des objets point étiquetés à intégrer dans les fonctions ML

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type point étiqueté et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) mis en forme en tant que données d’entrée utilisables par la plupart des algorithmes ML dans MLlib. Un [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est un vecteur local, dense ou fragmenté, associé à un libellé/une réponse.

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) composés d’un libellé (variable cible/réponse) et du vecteur de caractéristique. Ce format est nécessaire en entrée par de nombreux algorithmes ML de MLlib.


	# FUNCTIONS FOR BINARY CLASSIFICATION

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from numpy import array

	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingBinary(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt
	
	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
	def parseRowOneHotBinary(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tipped, features)
	    return  labPt


	# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

	# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
	def parseRowIndexingRegression(line):
	    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
	                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
	                         line.trip_distance, line.fare_amount])

	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt
	
	# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
	def parseRowOneHotRegression(line):
	    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
	                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
	                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
	                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
	    labPt = LabeledPoint(line.tip_amount, features)
	    return  labPt


### Créer un sous-échantillonnage aléatoire des données et le diviser en un jeu de formation et un jeu de test

Ce code crée un échantillonnage aléatoire des données (25 % utilisé ici). Bien que ce ne soit pas nécessaire dans cet exemple en raison de la taille du jeu de données, nous vous montrons comment créer un échantillon ici afin de savoir comment l’utiliser pour votre problème en cas de nécessité. Lorsque les échantillons sont volumineux, cela permet de gagner beaucoup de temps pendant l’apprentissage des modèles. Ensuite, nous divisons l’échantillon en une partie d’apprentissage (75 % ici) et une partie de test (25 % ici) à utiliser dans la modélisation de la classification et de la régression.


	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.sql.functions import rand

	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	samplingFraction = 0.25;
	trainingFraction = 0.75; testingFraction = (1-trainingFraction);
	seed = 1234;
	encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
	dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
	trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
	indexedTESTbinary = testData.map(parseRowIndexingBinary)
	oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
	oneHotTESTbinary = testData.map(parseRowOneHotBinary)
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg = trainData.map(parseRowIndexingRegression)
	indexedTESTreg = testData.map(parseRowIndexingRegression)
	oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
	oneHotTESTreg = testData.map(parseRowOneHotRegression)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Durée d’exécution de la cellule ci-dessus : 0,26 seconde


### Mise à l’échelle des caractéristiques

La mise à l’échelle des caractéristiques, également appelée normalisation des données, garantit que les caractéristiques aux valeurs très dispersées sont pondérées dans la fonction cible. Le code de mise à l’échelle des caractéristiques utilise [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) pour mettre à l’échelle les fonctionnalités à la variance d’unité. MLlib le fournit en vue d’une utilisation dans une régression linéaire avec SGD (Stochastic Gradient Descent), un algorithme populaire permettant de former une large gamme d’autres modèles Machine Learning, tels que les régressions régularisées ou les machines à vecteurs de support (SVM).

>REMARQUE AZURE : Nous avons découvert que l’algorithme LinearRegressionWithSGD est sensible à la mise à l’échelle des caractéristiques.


	# RECORD START TIME
	timestart = datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint
	from pyspark.mllib.linalg import Vectors
	from pyspark.mllib.feature import StandardScaler, StandardScalerModel
	from pyspark.mllib.util import MLUtils
	
	# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
	label = oneHotTRAINreg.map(lambda x: x.label)
	features = oneHotTRAINreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	label = oneHotTESTreg.map(lambda x: x.label)
	features = oneHotTESTreg.map(lambda x: x.features)
	scaler = StandardScaler(withMean=False, withStd=True).fit(features)
	dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
	oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Durée d’exécution de la cellule ci-dessus : 6,63 secondes


### Mettre en cache des objets en mémoire

La durée d’apprentissage et de test des algorithmes ML peut être réduite par la mise en cache d’objets de trame de données utilisés pour la classification, la régression et les caractéristiques mises à l’échelle.

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.cache()
	indexedTESTbinary.cache()
	oneHotTRAINbinary.cache()
	oneHotTESTbinary.cache()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.cache()
	indexedTESTreg.cache()
	oneHotTRAINreg.cache()
	oneHotTESTreg.cache()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.cache()
	oneHotTESTregScaled.cache()
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Durée d’exécution de la cellule ci-dessus : 0,11 seconde


## Prédire si un pourboire a été payé avec des modèles de classification binaires

Cette section montre comment utiliser trois modèles de tâche de classification binaire pour prédire si un pourboire est payé pour une course en taxi. Les modèles présentés sont les suivants :

- Régression logistique régularisée 
- Modèle de forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Chaque section de code générateur de modèle est divisée en étapes :

1. Données d’**apprentissage du modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un jeu de données de test avec des mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure

### Classification par régression logistique

Le code de cette section montre comment former, évaluer et enregistrer un modèle de régression logistique avec [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.


	#PREDICT WHETHER A TIP IS PAID OR NOT USING LOGISTIC REGRESSION WITH LBFGS

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
	from sklearn.metrics import roc_curve,auc
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	
	# CREATE MODEL WITH ONE SET OF PARAMETERS
	logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
	                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
	                                               tolerance=0.0001, validateData=True, numClasses=2)
	
	# PREDICT ON TEST DATA WITH MODEL
	predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
	
	# INSTANTIATE METRICS OBJECT
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# AREA UNDER PRECISION-RECALL CURVE
	print("Area under PR = %s" % metrics.areaUnderPR)

	# AREA UNDER ROC CURVE
	print("Area under ROC = %s" % metrics.areaUnderROC)
	metrics = MulticlassMetrics(predictionAndLabels)

	# OVERALL STATISTICS
	precision = metrics.precision()
	recall = metrics.recall()
	f1Score = metrics.fMeasure()
	print("Summary Stats")
	print("Precision = %s" % precision)
	print("Recall = %s" % recall)
	print("F1 Score = %s" % f1Score)
	
	# CREATE A PANDAS DATA-FRAME AND PLOT ROC-CURVE, FROM PREDICTED PROBS AND LABELS                                     
	logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
	predictionAndLabelsDF = predictionAndLabels.toDF()
	test_predictions = predictionAndLabelsDF.toPandas()
	predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
	
	prob = predictions_pddf["probability"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)

	# PLOT ROC CURVE
	plt.figure(figsize=(5,5))
	plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
	plt.plot([0, 1], [0, 1], 'k--')
	plt.xlim([0.0, 1.0])
	plt.ylim([0.0, 1.05])
	plt.xlabel('False Positive Rate')
	plt.ylabel('True Positive Rate')
	plt.title('ROC Curve')
	plt.legend(loc="lower right")
	plt.show()
	
	## SAVE MODEL WITH DATE-STAMP
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
	dirfilename = modelDir + logisticregressionfilename;
	
	logitModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Aire sous PR = 0,985319161941 Aire sous ROC = 0,983511076103 Précision des statistiques de résumé = 0,984187223276 Rappel = 0,984187223276 F1 Score = 0,984187223276

![Logistic regression ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)

Durée d’exécution de la cellule ci-dessus : 26,63 secondes

### Classement selon le modèle des forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêts aléatoires qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.
	
	#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import BinaryClassificationMetrics
	from pyspark.mllib.evaluation import MulticlassMetrics
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	# TRAIN RANDOMFOREST MODEL
	rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
	                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                       numTrees=25, featureSubsetStrategy="auto",
	                                       impurity='gini', maxDepth=5, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRINT TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# PRINT TEST METRICS
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfclassificationfilename = "RandomForestClassification_" + datestamp;
	dirfilename = modelDir + rfclassificationfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**Output:**

Aire sous ROC = 0,985240932843 Durée d’exécution ce la cellule ci-dessus : 25,62 seconds


### Classification par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT qui prédit si un pourboire est payé pour un trajet dans le jeu de données des courses et tarifs de taxi à New York.

	#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	
	# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	
	gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
	## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
	#print('Learned classification GBT model:')
	#print(bgtModel.toDebugString())
	
	# PREDICT ON TEST DATA AND EVALUATE
	predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
	predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
	
	# AREA UNDER ROC CURVE
	metrics = BinaryClassificationMetrics(predictionAndLabels)
	print("Area under ROC = %s" % metrics.areaUnderROC)
	
	# PERSIST MODEL IN A BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
	dirfilename = modelDir + btclassificationfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


## Prédire le montant des pourboires de courses de taxi avec les modèles de régression

Cette section montre comment utiliser trois modèles pour la tâche de régression qui consiste à prédire le montant du pourboire versé pour une course de taxi en fonction d’autres caractéristiques de pourboire. Les modèles présentés sont les suivants :

- Régression linéaire régularisée
- Modèle de forêts aléatoires
- Arbres GBT (Gradient Boosting Tree)

Ces modèles sont décrits dans l’introduction. Chaque section de code générateur de modèle est divisée en étapes :

1. Données d’**apprentissage du modèle** avec un jeu de paramètres
2. **Évaluation du modèle** sur un jeu de données de test avec des mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure

### Régression linéaire avec SGD 

Le code de cette section montre comment utiliser des caractéristiques mises à l’échelle pour former une régression linéaire utilisant utilise la descente de gradient stochastique (SGD) à des fins d’optimisation et comment noter, évaluer et enregistrer le modèle dans Azure Blob Storage (WASB).

>REMARQUE AZURE : selon notre expérience, il peut y avoir des problèmes fréquents avec la convergence des modèles LinearRegressionWithSGD, et les paramètres doivent être modifiés/optimisés avec soin pour obtenir un modèle valide. La mise à l’échelle des variables est très utile (voir ci-dessous).

	#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

	# RECORD START TIME
	timestart = datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
	from pyspark.mllib.evaluation import RegressionMetrics
	from scipy import stats
	
	# USE SCALED FEATURES TO TRAIN MODEL
	linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)
	
	# SCORE ON SCALED TEST DATA-SET & EVALUATE
	predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
	testMetrics = RegressionMetrics(predictionAndLabels)
	
	# PRINT TEST METRICS
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
	dirfilename = modelDir + linearregressionfilename;
	
	linearModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


### Régression par forêts aléatoires

Le code de cette section montre comment former, évaluer et enregistrer un modèle de forêts aléatoires qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.


	#PREDICT TIP AMOUNTS USING RANDOM FOREST

	# RECORD START TIME
	timestart= datetime.datetime.now()

	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import RandomForest, RandomForestModel
	from pyspark.mllib.util import MLUtils
	from pyspark.mllib.evaluation import RegressionMetrics
	
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                    numTrees=25, featureSubsetStrategy="auto",
	                                    impurity='variance', maxDepth=10, maxBins=32)
	## UN-COMMENT IF YOU WANT TO PRING TREES
	#print('Learned classification forest model:')
	#print(rfModel.toDebugString())
	
	## PREDICT AND EVALUATE ON TEST DATA-SET
	predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)
	testMetrics = RegressionMetrics(predictionAndLabels)

	# PRINT TEST METRICS
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	rfregressionfilename = "RandomForestRegression_" + datestamp;
	dirfilename = modelDir + rfregressionfilename;
	
	rfModel.save(sc, dirfilename);
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


### Régression par arbres GBT (Gradient Boosting Tree)

Le code de cette section montre comment former, évaluer et enregistrer un modèle d’arbres GBT, qui prédit le montant d’un pourboire pour les données sur les courses de taxi à New York.


	#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

	# RECORD START TIME
	timestart= datetime.datetime.now()
	
	# LOAD PYSPARK LIBRARIES
	from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
	from pyspark.mllib.util import MLUtils
	
	## TRAIN MODEL
	categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
	gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
	                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
	
	## EVALUATE A TEST DATA-SET
	predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
	predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)
	testMetrics = RegressionMetrics(predictionAndLabels)

	# PRINT TEST METRICS
	print("RMSE = %s" % testMetrics.rootMeanSquaredError)
	print("R-sqr = %s" % testMetrics.r2)
	
	# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
	test_predictions = sqlContext.createDataFrame(predictionAndLabels)
	test_predictions_pddf = test_predictions.toPandas()
	
	ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
	plt.axis([-1, 20, -1, 20])
	plt.show(ax)
	
	# SAVE MODEL IN BLOB
	datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
	btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
	dirfilename = modelDir + btregressionfilename;
	
	gbtModel.save(sc, dirfilename)
	
	# PRINT ELAPSED TIME
	timeend = datetime.datetime.now()
	timedelta = round((timeend-timestart).total_seconds(), 2) 
	print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**OUTLOOK**

RMSE = 0,962160568829

Racine carrée = 0,717354800581

![Actual-vs-predicted-tip-amounts](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

	
## Nettoyage d’objets de la mémoire

Utilisez `unpersist()` pour supprimer les objets mis en cache en mémoire.
		
	# REMOVE ORIGINAL DFs
	taxi_df_train_cleaned.unpersist()
	taxi_df_train_with_newFeatures.unpersist()
	
	# FOR BINARY CLASSIFICATION TRAINING AND TESTING
	indexedTRAINbinary.unpersist()
	indexedTESTbinary.unpersist()
	oneHotTRAINbinary.unpersist()
	oneHotTESTbinary.unpersist()
	
	# FOR REGRESSION TRAINING AND TESTING
	indexedTRAINreg.unpersist()
	indexedTESTreg.unpersist()
	oneHotTRAINreg.unpersist()
	oneHotTESTreg.unpersist()
	
	# SCALED FEATURES
	oneHotTRAINregScaled.unpersist()
	oneHotTESTregScaled.unpersist()


## Enregistrer les emplacements de stockage des modèles à des fins de consommation et de notation

Pour consommer et noter un jeu de données indépendant décrit dans la rubrique [Noter et évaluer des modèles Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md), vous devez copier et coller ces noms de fichier contenant les modèles enregistrés créés ici dans le Notebook Jupyter de consommation. Voici le code permettant d’imprimer les chemins d’accès aux fichiers de modèle dont vous avez besoin ici.

	# MODEL FILE LOCATIONS FOR CONSUMPTION
	print "logisticRegFileLoc = modelDir + "" + logisticregressionfilename + """;
	print "linearRegFileLoc = modelDir + "" + linearregressionfilename + """;
	print "randomForestClassificationFileLoc = modelDir + "" + rfclassificationfilename + """;
	print "randomForestRegFileLoc = modelDir + "" + rfregressionfilename + """;
	print "BoostedTreeClassificationFileLoc = modelDir + "" + btclassificationfilename + """;
	print "BoostedTreeRegressionFileLoc = modelDir + "" + btregressionfilename + """;


## Et ensuite ?

Maintenant que vous avez créé des modèles de régression et de classification avec la bibliothèque MlLib Spark, vous êtes prêt à apprendre à noter et évaluer ces modèles.

**Consommation de modèles :** pour apprendre à noter et évaluer les modèles de classification et de régression créés dans cette rubrique, consultez [Noter et évaluer des modèles Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md).

<!---HONumber=AcomDC_0420_2016-->