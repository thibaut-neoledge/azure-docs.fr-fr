<properties
	pageTitle="Science des données à l’aide de Scala avec Spark sur Azure | Microsoft Azure"
	description="Comment utiliser Scala pour les tâches d’apprentissage automatique supervisées la bibliothèque d’apprentissage automatique évolutif (MLlib) Spark et les packages SparkML sur un cluster Azure HDInsight Spark."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,deguhath"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="bradsev;"/>


# Science des données à l’aide de Scala avec Spark sur Azure

Cette rubrique montre comment utiliser Scala pour les tâches d’apprentissage automatique supervisées la bibliothèque d’apprentissage automatique évolutif (MLlib) Spark et les packages SparkML sur un cluster Azure HDInsight Spark. Il vous guide à travers les tâches qui constituent le [processus de science des données](http://aka.ms/datascienceprocess) : ingestion et exploration des données, visualisation, ingénierie des fonctionnalités et consommation de modèles. Les modèles conçus incluent la régression logistique, la régression linéaire, les forêts aléatoires et les arbres GBT (Gradient Boosted Tree). Nous allons traiter deux tâches d’apprentissage automatique supervisées courantes :

- Problème de régression : prédiction du montant du pourboire ($)
- Classification binaire : prédiction de la réception d’un pourboire ou non (1/0) pour un trajet en taxi

Le processus de modélisation nécessite une formation et une évaluation sur des jeux de données de test avec des mesures de précision pertinentes. Nous montrons également comment stocker ces modèles dans le stockage d’objets blob Azure (WASB) et comment noter et évaluer leurs performances de prédiction. Une rubrique plus avancée décrit comment optimiser des modèles par validation croisée et balayage hyperparamétrique. Les données utilisées sont un échantillon du jeu de données NYC Taxi Trip and Fare 2013.

[Scala](http://www.scala-lang.org/) est un langage basé sur machine virtuelle Java qui intègre les concepts du langage fonctionnel et orienté objet. C’est un langage évolutif qui convient pour le traitement distribué dans le cloud et s’exécute sur des clusters Azure Spark.

[Spark](http://spark.apache.org/) est une infrastructure de traitement en parallèle open source qui prend en charge le traitement en mémoire pour accroître les performances des applications d’analyse de Big Data. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul distribué en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans l'apprentissage automatique et les calculs de graphiques. Le package [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) fournit un ensemble d’API de haut niveau basées sur des trames de données qui permettent aux utilisateurs de créer et d’ajuster des pipelines d’apprentissage automatique pratique. [MLlib](http://spark.apache.org/mllib/) est la bibliothèque évolutive d’apprentissage automatique de Spark. Elle apporte des fonctionnalités de modélisation à cet environnement distribué.

[HDInsight Spark](../hdinsight/hdinsight-apache-spark-overview.md) est l’offre Azure de Spark Open Source. Il prend également en charge les **notebooks Jupyter Scala** sur le cluster Spark, qui peuvent exécuter des requêtes interactives SQL Spark pour transformer, filtrer et visualiser les données stockées dans les objets blob Azure (WASB). Les extraits de code Scala qui fournissent les solutions et montrent les tracés pertinents permettant de visualiser les données ici s’exécutent dans des notebooks Jupyter installés sur les clusters Spark. Les étapes de modélisation dans ces rubriques contiennent du code qui montre comment former, évaluer, enregistrer et consommer chaque type de modèle.

Les étapes de configuration et le code fournis dans cette procédure concernent HDInsight 3.4 Spark 1.6. Toutefois, le code présenté ici et dans les [notebooks Jupyter Scala](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) pour cette procédure pas à pas est générique et doit fonctionner sur n’importe quel cluster Spark. Les étapes de configuration et de gestion de cluster peuvent être légèrement différentes de celles indiquées ici, si vous n’utilisez pas HDInsight Spark.

> [AZURE.NOTE] Pour une rubrique qui montre comment utiliser Python plutôt que Scala pour effectuer des tâches de processus de science des données de bout en bout, consultez [Science des données avec Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Configuration requise

1\. Avant de commencer ces rubriques, vous devez avoir un abonnement Azure. Si vous n’en avez pas, consultez [Obtenir un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2\. Vous avez besoin d’un cluster HDInsight 3.4 Spark 1.6 pour effectuer cette procédure pas à pas. Pour en créer un, consultez les instructions fournies dans [Prise en main : Créer Apache Spark sur Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Vous spécifiez le type et la version du cluster à partir du menu **Sélectionner le type de cluster**.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Pour obtenir une description des données NYC Taxi Trip Taxi et pour savoir comment exécuter du code à partir d’un notebook Jupyter sur le cluster Spark, consultez les sections pertinentes de [Vue d’ensemble de la science des données utilisant Spark sur Azure HDInsight](machine-learning-data-science-spark-overview.md).


## Exécuter le code Scala à partir d’un notebook Jupyter sur le cluster Spark 

Vous pouvez lancer le notebook Jupyter à partir du portail Azure. Recherchez votre cluster Spark sur votre tableau de bord, puis cliquez dessus pour accéder à la page de gestion de votre cluster. Ensuite, cliquez sur **Tableaux de bord du cluster** -> **Bloc-notes Jupyter** pour ouvrir le notebook associé au cluster Spark.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Vous pouvez également naviguer jusqu’à ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** pour accéder aux notebooks Jupyter. Remplacez simplement la partie CLUSTERNAME de cette URL par le nom de votre propre cluster. Vous aurez besoin du mot de passe de votre compte d’administrateur pour accéder aux notebooks.

![](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Sélectionnez Scala pour afficher un répertoire qui contient quelques exemples de notebooks prédéfinis qui utilisent l’API PySpark. Le notebook [Modélisation d’exploration et notation avec Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala) qui contiennent les exemples de code pour cet ensemble de rubriques Spark sont disponibles sur **Github**


Vous pouvez télécharger le notebook directement de Github sur le serveur de notebooks Jupyter de votre cluster Spark. Dans la page d’accueil de votre Jupyter, cliquez sur le bouton **Télécharger** dans la partie droite de l’écran. Un explorateur de fichiers s’ouvre. Vous pouvez coller l’URL GitHub (contenu brut) du notebook Scala et cliquer sur **Ouvrir**. Le notebook Scala est disponible à l’URL suivante :

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)


## Configuration : contextes prédéfinis, fonctions magiques et bibliothèques Spark

### Contexte Spark et Hive prédéfini

	# SET START TIME
	import java.util.Calendar
	val beginningTime = Calendar.getInstance().getTime()


Les noyaux Spark fournis avec les notebooks Jupyter ont un contexte prédéfini. Vous n’avez pas besoin de définir les contextes Spark ou Hive explicitement avant de commencer à utiliser l’application que vous développez ; ils sont disponibles par défaut. Ces contextes sont les suivants :

- sc : pour SparkContext
- sqlContext : pour HiveContext


### Commandes magiques de Spark

Le noyau Spark fournit certaines « commandes magiques » prédéfinies, qui sont des commandes spéciales que vous pouvez appeler avec %%. Deux de ces commandes sont utilisées dans ces exemples de code.

- **%%local** Indique que le code des lignes suivantes est exécuté localement. Le code doit être un code Scala valide.
- **%%sql -o <nom de variable>** Exécute une requête Hive sur sqlContext. Si le paramètre -o est passé, le résultat de la requête est conservé dans le contexte Scala %%local en tant que tableau de données Spark.

Pour plus d’informations sur les noyaux pour notebooks Jupyter et sur les « commandes magiques » prédéfinies appelées avec %% (par exemple, %%local) qu’ils fournissent, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### Importer les bibliothèques

Importez Spark, MLlib et les autres bibliothèques nécessaires avec le code suivant.

	# IMPORT SPARK AND JAVA LIBRARIES 
	import org.apache.spark.sql.SQLContext
	import org.apache.spark.sql.functions._
	import java.text.SimpleDateFormat
	import java.util.Calendar
	import sqlContext.implicits._
	import org.apache.spark.sql.Row
	
	# SPARK SQL FUNCTIONS
	import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
	import org.apache.spark.sql.functions.rand
	
	# SPARK ML FUNCTIONS
	import org.apache.spark.ml.Pipeline
	import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
	import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
	import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
	import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
	import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}
	
	# SPARK MLLIB FUNCTIONS
	import org.apache.spark.mllib.linalg.{Vector, Vectors}
	import org.apache.spark.mllib.util.MLUtils
	import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
	import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
	import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
	import org.apache.spark.mllib.tree.configuration.BoostingStrategy
	import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
	import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}
	
	# SPECIFY SQL CONTEXT
	val sqlContext = new SQLContext(sc)


## Ingestion de données
 
La première étape du processus de science des données consiste à recevoir les données à partir de sources ou de systèmes externes, et à les analyser dans votre environnement de modélisation et d’exploration de données. Dans cette procédure pas à pas, nous lisons un échantillon joint de 0,1 % des trajets et frais de taxi (stocké sous forme de fichier .tsv). L’environnement de modélisation et d’exploration de données est Spark. Cette section contient le code permettant d’effectuer la série de tâches suivante :

- définir les chemins d’accès pour le stockage des données et du modèle
- lire le jeu de données en entrée (stocké dans un fichier TSV)
- définir un schéma pour les données et nettoyer les données
- créer une trame de données propre, puis la mettre en mémoire cache
- enregistrer les données en tant que table temporaire dans le contexte SQL
- Interrogez la table et importez les résultats dans une trame de données.


### Définir les chemins d’accès aux emplacements de stockage dans WASB

Spark peut lire et écrire dans Azure Blob Storage (également appelé WASB). Donc, vos données stockées dedans sont exploitables par Spark et les résultats peuvent être stockés à nouveau dans WASB.

Pour enregistrer les modèles ou les fichiers dans WASB, le chemin d’accès doit être correctement spécifié. Le conteneur par défaut associé au cluster Spark peut être référencé à l’aide d’un chemin commençant par wasb///. Les autres emplacements sont référencés par wasb://.

L’exemple de code suivant spécifie l’emplacement des données d’entrée pour la lecture et le chemin d’accès à un objet Blob Azure qui est associé au cluster Spark dans lequel le modèle sera enregistré.

	# SET PATHS TO DATA AND MODEL FILE LOCATIONS: 
	# INGEST DATA DATA AND SPECIFY HEADERS FOR COLUMNS
	val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
	val header = taxi_train_file.first;
	
	# SET THE MODEL STORAGE DIRECTORY PATH 
	# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
	val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### Importer des données, créer un objet RDD et définir une trame de données en fonction du schéma 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE SCHEMA BASED ON HEADER OF THE FILE
	val sqlContext = new SQLContext(sc)
	val taxi_schema = StructType(
	    Array(
	        StructField("medallion", StringType, true), 
	        StructField("hack_license", StringType, true),
	        StructField("vendor_id", StringType, true), 
	        StructField("rate_code", DoubleType, true),
	        StructField("store_and_fwd_flag", StringType, true),
	        StructField("pickup_datetime", StringType, true),
	        StructField("dropoff_datetime", StringType, true),
	        StructField("pickup_hour", DoubleType, true),
	        StructField("pickup_week", DoubleType, true),
	        StructField("weekday", DoubleType, true),
	        StructField("passenger_count", DoubleType, true),
	        StructField("trip_time_in_secs", DoubleType, true),
	        StructField("trip_distance", DoubleType, true),
	        StructField("pickup_longitude", DoubleType, true),
	        StructField("pickup_latitude", DoubleType, true),
	        StructField("dropoff_longitude", DoubleType, true),
	        StructField("dropoff_latitude", DoubleType, true),
	        StructField("direct_distance", StringType, true),
	        StructField("payment_type", StringType, true),
	        StructField("fare_amount", DoubleType, true),
	        StructField("surcharge", DoubleType, true),
	        StructField("mta_tax", DoubleType, true),
	        StructField("tip_amount", DoubleType, true),
	        StructField("tolls_amount", DoubleType, true),
	        StructField("total_amount", DoubleType, true),
	        StructField("tipped", DoubleType, true),
	        StructField("tip_class", DoubleType, true)
	        )
	    )
	
	# CAST VARIABLES ACCORDING TO SCHEMA
	val taxi_temp = (taxi_train_file.map(_.split("\t"))
	                        .filter((r) => r(0) != "medallion")
	                        .map(p => Row(p(0), p(1), p(2),
	                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
	                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
	                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
	                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))
	
	
	# CREATE INITIAL DATA-FRAME, DROP COLUMNS, AND CREATE CLEANED DATA-FRAME BY FILTERING FOR UNDESIRED VALUES OR OUTLIERS
	val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
	
	val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
	        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
	        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
	        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
	        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
	        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
	        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
	        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
	        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));
	
	# CACHE AND MATERIALIZE CLEANED DATA-FRAME IN MEMORY
	taxi_df_train_cleaned.cache()
	taxi_df_train_cleaned.count()
	
	# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
	taxi_df_train_cleaned.registerTempTable("taxi_train")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Temps nécessaire pour exécuter la cellule ci-dessus : 8 secondes.


### Interrogez la table et importez les résultats dans une trame de données. 

Interrogez la table pour les données de prix, de passagers et de pourboires, filtrez les données endommagées et éloignées, et imprimez plusieurs lignes.

	# QUERY THE DATA
	val sqlStatement = """
	    SELECT fare_amount, passenger_count, tip_amount, tipped
	    FROM taxi_train 
	    WHERE passenger_count > 0 AND passenger_count < 7
	    AND fare_amount > 0 AND fare_amount < 200
	    AND payment_type in ('CSH', 'CRD')
	    AND tip_amount > 0 AND tip_amount < 25
	"""
	val sqlResultsDF = sqlContext.sql(sqlStatement)
	
	# SHOW ONLY THE TOP THREE ROWS
	sqlResultsDF.show(3)

**SORTIE :**

fare\_amount|passenger\_count|tip\_amount|tipped
-----------|---------------|----------|------
 13,5| 1\.0| 2,9| 1\.0
 16,0| 2\.0| 3\.4| 1\.0
 10,5| 2\.0| 1\.0| 1\.0


## Exploration et visualisation de données 

Une fois les données intégrées dans Spark, l’étape suivante du processus de science des données consiste à mieux comprendre les données par l’exploration et la visualisation. Dans cette section, nous examinons les données de taxi à l’aide de requêtes SQL, puis importons les résultats dans une trame de données pour tracer les variables cibles et les fonctionnalités potentielles pour l’examen visuel à l’aide de la fonctionnalité de visualisation automatique de Jupyter.

### Utiliser les fonctions magiques locales et SQL pour tracer les données

Par défaut, la sortie de tout extrait de code que vous exécutez à partir d’un notebook Jupyter est disponible dans le contexte de la session qui est persistante sur les nœuds de travail. Si vous souhaitez enregistrer un trajet sur les nœuds de travail pour chaque calcul et si toutes les données dont vous avez besoin pour votre calcul sont disponibles localement sur le nœud du serveur Jupyter (qui est le nœud principal), vous pouvez utiliser la fonction magique %%local pour exécuter l’extrait de code sur le serveur Jupyter.

- **Commande magique SQL (`%%sql`)** Le noyau HDInsight Spark prend en charge les requêtes HiveQL inline faciles exécutées sur sqlContext. L’argument (-o nom\_variable) conserve la sortie de la requête SQL en tant que tableau de données Pandas sur le serveur Jupyter. Cela signifie qu’elle sera disponible en mode local.
- La **commande magique `%%local`** est utilisée pour exécuter le code localement sur le serveur Jupyter, qui est le nœud principal du cluster HDInsight. En général, vous utilisez la commande magique `%%local` conjointement avec la commande magique `%%sql` avec le paramètre -o. Le paramètre -o conserve la sortie de la requête SQL localement, puis la commande magique %%local déclenche l’ensemble suivant d’extrait de code pour une exécution locale sur la sortie des requêtes SQL conservées localement.

### Interroger les données avec SQL
Cette requête retrouve les trajets par montant du trajet, nombre de passagers et montant du pourboire.

	# SQL QUERY
	%%sql -q -o sqlResults
	SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

Dans le code ci-dessous, la fonction magique %%local crée une trame de données locale, sqlResults, qui peut être utilisée pour le traçage à l’aide de matplotlib.

> [AZURE.TIP] Cette commande magique locale est utilisée plusieurs fois lors de cette procédure pas à pas. Si la quantité de vis données est élevée, échantillonnez pour créer un tableau de données adapté à la mémoire locale.

### Tracer les données

Vous pouvez tracer à l’aide de code Python une fois la trame de données dans le contexte local en tant que trame de données Pandas.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local
	
	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults


 Le noyau Spark visualise automatiquement la sortie des requêtes SQL (HiveQL) après avoir exécuté le code. Vous pouvez choisir entre plusieurs types de visualisations :

- Table
- Secteurs
- Lignes
- Domaine
- Barres

Voici le code permettant de tracer les données :

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	import matplotlib.pyplot as plt
	%matplotlib inline
	
	# TIP BY PAYMENT TYPE AND PASSENGER COUNT
	ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
	ax1.set_title('Tip amount distribution')
	ax1.set_xlabel('Tip Amount ($)')
	ax1.set_ylabel('Counts')
	plt.suptitle('')
	plt.show()
	
	# TIP BY PASSENGER COUNT
	ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
	ax2.set_title('Tip amount by Passenger count')
	ax2.set_xlabel('Passenger count')
	ax2.set_ylabel('Tip Amount ($)')
	plt.suptitle('')
	plt.show()
	
	# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
	ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
	ax.set_title('Tip amount by Fare amount')
	ax.set_xlabel('Fare Amount ($)')
	ax.set_ylabel('Tip Amount ($)')
	plt.axis([-2, 80, -2, 20])
	plt.show()


**SORTIE :**

![Histogramme de montant de pourboire](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Montant de pourboire par nombre de passagers](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Montant du pourboire par montant de la course](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)



## Création de fonctions et préparation des données pour les fonctions de modélisation 

Pour utiliser les fonctions de modélisation basées sur l’arborescence de Spark ML et MLlib, la cible et les fonctionnalités doivent être préparées à l’aide de diverses techniques appropriées, telles que le placement, l’indexation, l’encodage à chaud et la vectorisation. Voici les procédures que nous décrirons dans cette section :

- Créer une caractéristique en **regroupant** les heures dans des périodes de trafic
- **Indexation et encodage « à chaud »** des fonctionnalités catégorielles
- **Échantillonner et diviser le jeu de données** en fractions de formation et de test
- **Spécifier la variable de formation et les fonctionnalités** et créer des objets RDD ou trames de données d’entrée libellés de formation et de test indexés ou encodés à chaud
- Automatiquement **classer et vectoriser les fonctionnalités et cibles** pour une utilisation en tant qu’entrées pour les modèles ML


### Créer une caractéristique en regroupant les heures dans des périodes de trafic 

Ce code montre comment créer une nouvelle caractéristique en regroupant les heures dans des périodes de trafic et comment mettre en cache la trame de données obtenue en mémoire. Là où les jeux de données distribués résilients (RDD) et les trames de données sont utilisés de manière répétitive, la mise en cache réduit les temps d’exécution. Par conséquent, nous mettons en cache les RDD et les trames de données à plusieurs stades de la procédure.

	# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
	val sqlStatement = """
	    SELECT *,
	    CASE
	     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
	     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
	     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
	     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
	    END as TrafficTimeBins
	    FROM taxi_train 
	"""
	val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
	
	# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY 
	taxi_df_train_with_newFeatures.cache()
	taxi_df_train_with_newFeatures.count()


### Indexation et encodage « à chaud » des fonctionnalités catégorielles 

Cette section montre comment indexer ou encoder les caractéristiques catégorielles à intégrer dans les fonctions de modélisation. Les fonctions de modélisation et de prédiction de MLlib requièrent des caractéristiques avec des données d’entrée catégorielles à indexer ou à encoder avant leur utilisation.

Selon le modèle, vous devez les indexer ou les encoder différemment : Les modèles de régression logistique et linéaire requièrent un encodage linéaire où, par exemple, une fonction avec 3 catégories peut être développée en 3 colonnes de caractéristiques, chacune contenant 0 ou 1 selon la catégorie d’une observation. MLlib fournit la fonction [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) permettant d’effectuer un encodage linéaire. Cet encodeur mappe une colonne d’index de libellé à une colonne de vecteurs binaires, contenant au plus une seule une valeur. Cet encodage autorise les algorithmes qui appliquent des caractéristiques numériques, comme la régression logistique, à des caractéristiques catégorielles.

Ici, nous transformons uniquement quatre variables pour afficher des exemples, qui sont des chaînes de caractères. D’autres variables, telles que le jour de la semaine, représentées par des valeurs numériques, peuvent également être indexées en tant que variables catégorielles.

Pour l’indexation, nous avons utilisé `StringIndexer()`, et pour l’encodage à chaud, nous avons utilisé les fonctions `OneHotEncoder()` de MLlib. Voici le code permettant d’indexer et d’encoder des caractéristiques catégorielles :


	# HERE WE CREATE INDEXES, AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# INDEX AND ENCODE VENDOR_ID
	val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
	val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
	val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
	val encoded1 = encoder.transform(indexed)
	
	# INDEX AND ENCODE RATE_CODE
	val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
	val indexed = stringIndexer.transform(encoded1)
	val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
	val encoded2 = encoder.transform(indexed)
	
	# INDEX AND ENCODE PAYMENT_TYPE
	val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
	val indexed = stringIndexer.transform(encoded2)
	val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
	val encoded3 = encoder.transform(indexed)
	
	# INDEX AND TRAFFIC TIME BINS
	val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
	val indexed = stringIndexer.transform(encoded3)
	val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
	val encodedFinal = encoder.transform(indexed)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 4 secondes.



### Échantillonner et diviser le jeu de données en fractions de formation et de test

Ce code crée un échantillonnage aléatoire des données (25 % utilisé ici). Bien que ce ne soit pas nécessaire dans cet exemple en raison de la taille du jeu de données, nous vous montrons comment créer un échantillon ici afin de savoir comment l’utiliser pour votre problème en cas de nécessité. Lorsque les échantillons sont volumineux, cela permet de gagner beaucoup de temps pendant l’apprentissage des modèles. Ensuite, nous divisons l’échantillon en une partie d’apprentissage (75 % ici) et une partie de test (25 % ici) à utiliser dans la modélisation de la classification et de la régression.

Nous ajoutons un nombre aléatoire (entre 0 et 1) pour chaque ligne (dans une colonne « rand ») qui peut être utilisé pour sélectionner des plis de validation croisée pendant la formation.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# SPECIFY SAMPLING AND SPLITTING FRACTIONS
	val samplingFraction = 0.25;
	val trainingFraction = 0.75; 
	val testingFraction = (1-trainingFraction);
	val seed = 1234;
	val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
	val sampledDFcount = encodedFinalSampledTmp.count().toInt
	
	val generateRandomDouble = udf(() => {
	    scala.util.Random.nextDouble
	})
	
	# ADD RANDOM NUMBER FOR CV
	val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());
	
	# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
	# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
	val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
	val trainData = splits(0)
	val testData = splits(1)
	
	# GET TIME TO RUN THE CELL 
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 2 secondes.


### Spécifiez la variable de formation et les fonctionnalités et créez des objets RDD ou trames de données d’entrée libellés de formation et de test indexés ou encodés à chaud. 

Cette section contient le code qui montre comment indexer des données textuelles catégorielles en type point étiqueté et les encoder afin qu’elles puissent former et tester la régression logistique de MLlib et d’autres modèles de classification. Les objets point étiquetés sont des jeux de données distribués résilients (RDD) mis en forme en tant que données d’entrée utilisables par la plupart des algorithmes ML dans MLlib. Un [point étiqueté](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) est un vecteur local, dense ou fragmenté, associé à un libellé/une réponse.

Dans ce code, nous spécifions la variable et les fonctionnalités la cible (dépendante) à utiliser pour former et créer des objets RDD ou trames de données d’entrée libellés de formation et de test indexés ou encodés à chaud.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS.
	val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
	val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
	val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))
	
	# INDEXED LAELEDPOINT RDD OBJECTS
	val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
	
	# Indexed DFs that can be used for training using Spark ML functions
	val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
	val indexedTESTbinaryDF = indexedTESTbinary.toDF()
	val indexedTRAINregDF = indexedTRAINreg.toDF()
	val indexedTESTregDF = indexedTESTreg.toDF()
	
	# One-hot encoded (vectorized) DFs that can be used for training using Spark ML functions
	val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
	val OneHotTRAIN = assemblerOneHot.transform(trainData) 
	val OneHotTEST = assemblerOneHot.transform(testData)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 4 secondes.


### Automatiquement classer et vectoriser les fonctionnalités et cibles pour une utilisation en tant qu’entrées pour les modèles ML

Classez correctement la cible et les fonctionnalités à utiliser dans les fonctions de modélisation en arborescence dans Spark ML. Le code effectue deux tâches :

1. Crée une cible binaire pour la classification en affectant une valeur de 0 ou 1 pour chaque point de données compris entre 0 et 1 avec une valeur de seuil de 0,5.
2. Classe automatiquement les fonctionnalités. Si le nombre de valeurs numériques distinctes pour une des fonctionnalités est < 32, cette fonctionnalité est classée.

Voici le code de ces deux tâches.

	# CATEGORIZE FEATURES AND BINARIZE TARGET FOR BINARY CLASSIFICATION PROBLEM

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
	val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
	val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)
	
	# CATEGORIZE FEATURES FOR REGRESSION PROBLEM
	# CREATE PROPERLY INDEXED AND CATEGORIED DFS FOR TREE-BASED MODELS

	# TRAIN DATA
	val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
	val indexerModel = indexer.fit(indexedTRAINregDF)
	val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)
	
	# TEST DATA
	val indexerModel = indexer.fit(indexedTESTbinaryDF)
	val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)


## Classification binaire : Prédire si un pourboire a été payé ou non

Dans cette section, nous créons trois types de modèles de classification binaire pour prédire si un pourboire est payé :

- un **modèle de régression logistique** à l’aide de la fonction `LogisticRession()` de SparkML
- un **modèle de classification par forêts aléatoires** à l’aide de la fonction `RandomForestClassifier()` de SparkML
- un **modèle de classification par arbres Gradient Boosting** à l’aide de la fonction `GradientBoostedTrees()` de MLlib

### Créer un modèle de régression logistique

Ici, nous créons un modèle de régression logistique à l’aide de la fonction `LogisticRession()` de SparkML. Le code de génération de modèle dans cette section se compose d’une série d’étapes :

1. Données de **formation du modèle** avec un jeu de paramètres
2. **Évaluation de modèle** sur un jeu de données de test avec mesures
3. **Enregistrement du modèle** dans l’objet blob en vue d’une utilisation ultérieure
4. **Notation du modèle** sur les données de test
5. **Traçage des résultats** - les courbes ROC

Voici le code pour ces procédures.

	# CREATE LOGISTIC REGRESSION MODEL 
	val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	val lrModel = lr.fit(OneHotTRAIN)
	
	# PREDICT ON TEST DATA SET
	val predictions = lrModel.transform(OneHotTEST)
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)
	
	# SAVE THE MODEL
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LogisticRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);

Puis chargez, notez et enregistrez les résultats.

	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()

	# LOAD SAVED MODEL AND SCORE THE TEST DATA SET
	val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON THE TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
	predictions.registerTempTable("testResults")
	
	# SELECT BinaryClassificationEvaluator() TO COMPUTE TEST ERROR
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC RESULTS
	println("ROC on test data = " + ROC)


**SORTIE :**

ROC sur les données de test = 0,9827381497557599


Utilisez Python sur des trames de données Pandas locales pour tracer la courbe ROC.


	# QUERY RESULTS
	%%sql -q -o sqlResults
	SELECT tipped, probability from testResults


	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	%matplotlib inline
	from sklearn.metrics import roc_curve,auc
	
	sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
	predictions_pddf = sqlResults[["tipped","probFloat"]]
	
	# ROC CURVE
	# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
	prob = predictions_pddf["probFloat"] 
	fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
	roc_auc = auc(fpr, tpr)
	
	#PLOT
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


**SORTIE :**

![Courbe ROC de remise de pourboire ou non](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### Créer un modèle de classification par forêts aléatoires

Ici, nous créons un modèle de classification de forêt aléatoire à l’aide de la fonction Spark ML `RandomForestClassifier()` et évaluons le modèle de données de test.


	# RECORD START TIME 
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE THE RANDOM FOREST CLASSIFIER MODEL
	val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)
	
	# FIT THE MODEL
	val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
	val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)
	
	# EVALUATE THE MODEL
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(predictions)
	println("F1 score on test data: " + Test_f1Score);
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# CALCULATE BINARY CLASSIFICATION EVALUATION METRICSS
	val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
	val ROC = evaluator.evaluate(predictions)
	println("ROC on test data = " + ROC)


**SORTIE :**

ROC sur les données de test = 0.9847103571552683


### Créer un modèle de classification par arbres Gradient Boosting

Ici, nous créons un modèle de classification par arbres Gradient Boosting avec la fonction `GradientBoostedTrees()` de MLlib et évaluons le modèle sur des données de test.


	# TRAIN A GBT CLASSIFICATION MODEL USING MLIB AND LABELEDPOINT

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE THE GBT CLASSIFICATION MODEL
	val boostingStrategy = BoostingStrategy.defaultParams("Classification")
	boostingStrategy.numIterations = 20
	boostingStrategy.treeStrategy.numClasses = 2
	boostingStrategy.treeStrategy.maxDepth = 5
	boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	# TRAIN THE MODEL
	val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "GBT_Classification__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	gbtModel.save(sc, filename);
	
	# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
	val labelAndPreds = indexedTESTbinary.map { point =>
	  val prediction = gbtModel.predict(point.features)
	  (point.label, prediction)
	}
	val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
	//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
	println("Test Error = " + testErr)
	
	# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
	val metrics = new MulticlassMetrics(labelAndPreds)
	println(s"Precision: ${metrics.precision}")
	println(s"Recall: ${metrics.recall}")
	println(s"F1 Score: ${metrics.fMeasure}")
	
	val metrics = new BinaryClassificationMetrics(labelAndPreds)
	println(s"Area under PR curve: ${metrics.areaUnderPR}")
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT THE ROC METRIC
	println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**SORTIE :**

Zone sous courbe ROC = 0,9846895479241554


## Régression : prédire le montant du pourboire 

Dans cette section, nous créons deux types de modèles de régression pour prédire le montant du pourboire :

- un **modèle de régression linéaire régularisée** à l’aide de la fonction `LinearRegression()` de Spark, avec enregistrement du modèle et évaluation du modèle sur des données de test.
- un **modèle de régression par arbres Gradient Boosting** à l’aide de la fonction `GBTRegressor() ` de Spark ML


### Créer un modèle de régression linéaire régularisée

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE REGULARIZED LINEAR REGRESSION MODEL USING SPARK ML FUNCTION AND DATA-FRAME
	val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
	
	# FIT THE MODEL USING DATA-FRAME
	val lrModel = lr.fit(OneHotTRAIN)
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SUMMARIZE THE MODEL OVER THE TRAIINNG SET AND PRINT OUT SOME METRICS
	val trainingSummary = lrModel.summary
	println(s"numIterations: ${trainingSummary.totalIterations}")
	println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
	trainingSummary.residuals.show()
	println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
	println(s"r2: ${trainingSummary.r2}")
	
	# SAVE THE MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "LinearRegression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	lrModel.save(filename);
	
	# PRINT COEFFICIENTS
	println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = lrModel.transform(OneHotTEST)
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 13 secondes.


	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET. 

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# LOAD A SAVED LINEARREGRESSION MODEL FROM BLOB STORAGE
	val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
	println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")
	
	# SCORE THE MODEL ON TEST DATA
	val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
	predictions.registerTempTable("testResults")
	
	# EVALUATE THE MODEL ON TEST DATA
	val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
	val r2 = evaluator.evaluate(predictions)
	println("R-sqr on test data = " + r2)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("R-sqr on test data = " + r2)


**SORTIE :**

R-sqr sur les données de test = 0,5960320470835743


Ensuite, interrogez les résultats de test en tant que trame de données et visualisez-les avec autoviz de Jupyter & matplotlib de Python.


	# SQL QUERY
	%%sql -q -o sqlResults
	select * from testResults

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
	%%local

	# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
	# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
	sqlResults

Le code crée un tableau de données local à partir de la sortie de la requête et il trace les données. La commande magique `%%local` crée un tableau de données local, `sqlResults`, qui peut être utilisé pour le tracé avec matplotlib.

>[AZURE.NOTE] Cette commande magique Spark est utilisée plusieurs fois lors de cette procédure pas à pas. Si la quantité de données est élevée, vous devez échantillonner pour créer un tableau de données adapté à la mémoire locale.

Créez des tracés avec matplotlib de Python.

	# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
	%%local
	sqlResults
	%matplotlib inline
	import numpy as np
	
	# PLOT THE RESULTS
	ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
	fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
	ax.set_title('Actual vs. Predicted Tip Amounts ($)')
	ax.set_xlabel("Actual")
	ax.set_ylabel("Predicted")
	#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
	plt.axis([-1, 15, -1, 8])
	plt.show(ax)

**SORTIE :**

![Montant du pourboire : réel vs. prédit](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### Créer un modèle de régression par arbres Gradient Boosting

Ici, nous créons un modèle de régression par arbres Gradient Boosting avec la fonction `ML GBTRegressor()` de Spark et évaluons le modèle sur des données de test.

Les arbres GBT ([Gradient Boosting Tree](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts)) sont des ensembles d’arbres de décision. Ils aident les arbres de décision à minimiser itérativement une fonction de perte. Utilisés pour la régression et la classification, les arbres GBT gèrent les caractéristiques catégorielles, ne requièrent aucune mise à l’échelle des caractéristiques et peuvent capturer les non-linéarités ainsi que les interactions entre les caractéristiques. Ils s’utilisent également dans le paramétrage de classification multiclasse.


	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# TRAIN A GBT REGRESSION MODEL
	val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
	val gbtModel = gbt.fit(indexedTRAINwithCatFeat)
	
	# MAKE PREDICTIONS
	val predictions = gbtModel.transform(indexedTESTwithCatFeat)
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(predictions)
	
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	# PRINT RESULTS
	println("Test R-sqr is: " + Test_R2);


**SORTIE :**

Le R-sqr de test est : 0,7655383534596654



## Utilitaires de modélisation avancée pour l’optimisation

Dans cette section, nous montrons comment utiliser les utilitaires ML qui sont fréquemment employés pour l’optimisation des modèles. Plus précisément, nous présentons trois façons d’optimiser les modèles ML à l’aide du balayage paramétrique et de la validation croisée :

1\. Fractionnez les données en ensembles de formation et de validation, optimisez le modèle à l’aide de l’hyper balayage paramétrique sur un jeu formation et l’évaluation sur un jeu de validation (régression linéaire)

2\. Optimisez le modèle à l’aide de la validation croisée et l’hyper balayage paramétrique, à l’aide de la fonction CrossValidator de Spark ML (Classification binaire)

3\.Optimisez le modèle en utilisant un code de validation croisée et de balayage paramétrique pour utiliser toute fonction de ML et tout jeu de paramètres (régression linéaire)


La **validation croisée** est une technique qui évalue la généralisation d’un modèle formé sur un jeu connu de données pour la prédiction des caractéristiques d’un jeu de données sur lequel il n’a pas été formé. L’idée derrière cette technique est de former un modèle sur un jeu de données connues, puis d’évaluer la précision de ses prédictions sur un jeu de données indépendant. Une implémentation commune utilisée ici consiste à diviser un jeu de données en plis « K », puis de former le modèle par la méthode tourniquet (round robin) sur tous les plis sauf un.

**L’optimisation hyperparamétrique** consiste à choisir un jeu d’hyperparamètres pour un algorithme d’apprentissage, généralement dans le but d’optimiser la mesure des performances de l’algorithme sur un jeu de données indépendant. Les **hyperparamètres** sont des valeurs qui doivent être spécifiées en dehors de la procédure de formation de modèle. Les hypothèses concernant ces valeurs peuvent avoir un impact sur la flexibilité et la précision des modèles. Les arbres de décision ont des hyperparamètres, tels que la profondeur voulue et le nombre de feuilles de l’arbre. Les machines à vecteurs de support nécessitent la configuration d’un terme de pénalité en cas d’erreur de classification.

Une façon courante d’effectuer l’optimisation hyperparamétrique, utilisée ici, est la recherche par grille, ou **un balayage paramétrique**. Elle consiste en une recherche exhaustive d’un algorithme d’apprentissage sur les valeurs d’un sous-ensemble spécifié de l’espace hyperparamétrique. La validation croisée peut fournir une mesure de performance permettant de trier les résultats optimaux produits par l’algorithme de recherche par grille. La validation croisée, utilisée avec le balayage hyperparamétrique, limite les problèmes tels que le surajustement d’un modèle aux données de formation ; le modèle peut ainsi être appliqué au jeu de données général à partir duquel les données de formation ont été extraites.

### Optimiser le modèle de régression linéaire avec le balayage paramétrique

Fractionner les données en ensembles de formation et de validation, optimiser le modèle à l’aide de l’hyper balayage paramétrique sur un jeu formation et l’évaluation sur un jeu de validation (régression linéaire)

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# RENAME tip_amount AS LABEL
	val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label") 
	val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
	OneHotTRAINLabeled.cache()
	OneHotTESTLabeled.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION: THE LinearRegression() FUNCTION
	val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()
	
	# DEFINE PIPELINE WITH TRAIN-TEST VALIDATION SPLIT, WITH 75% IN THE TRAIING SET, SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
	val trainPct = 0.75
	val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = trainValidationSplit.fit(OneHotTRAINLabeled)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")
	
	# COMPUTE TEST SET R2
	val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
	val Test_R2 = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");
	
	println("Test R-sqr is: " + Test_R2);


**SORTIE :**

Le R-sqr de test est : 0,6226484708501209


### Optimiser le modèle de classification binaire à l’aide de la validation croisée et de l’hyper balayage paramétrique

Ici, nous montrons comment optimiser le modèle de classification binaire à l’aide de la validation croisée et de l’hyper balayage paramétrique. Cet exemple utilise la fonction CrossValidator de Spark ML.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# CREATE DATA-FRAMES WITH PROPERLY LABELED COLUMNS FOR USE WITH TRAIN/TEST SPLIT
	val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
	indexedTRAINwithCatFeatBinTargetRF.cache()
	indexedTESTwithCatFeatBinTargetRF.cache()
	
	# DEFINE THE ESTIMATOR FUNCTION
	val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)
	
	# DEFINE THE PARAMETER GRID
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()
	
	# SPECIFY THE NUMBER OF FOLDS
	val numFolds = 3
	
	# DEFINE THE TRAIN-TEST VALIDATION SPLIT WITH 75% IN THE TRAIING SET
	val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)
	
	# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
	val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)
	
	# MAKE PREDICTIONS ON THE TEST DATA USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORM THE BEST
	val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")
	
	# COMPUTE TEST F1 SCORE
	val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
	val Test_f1Score = evaluator.evaluate(testResults)
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 33 secondes.


### Optimiser le modèle de régression linéaire à l’aide de code personnalisé de validation croisée et de balayage paramétrique

Ici, nous optimisons le modèle à l’aide de code personnalisé et identifions les meilleurs paramètres de modèle à l’aide du critère de précision le plus élevé. Puis nous créons le modèle final, évaluons le modèle sur des données de test et enregistrons le modèle dans un stockage d’objets Blob. Enfin, nous chargeons le modèle, notons les données de test et évaluons la précision.

	# RECORD START TIME
	val starttime = Calendar.getInstance().getTime()
	
	# DEFINE PARAMETER GRID AND THE NUMBER OF FOLDS
	val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()
	
	val nFolds = 3
	val numModels = paramGrid.size
	val numParamsinGrid = 2
	
	# SPECIFY THE NUMBER OF CATEGORIES FOR THE CATEGORIAL VARIBLES
	val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))
	
	var maxDepth = -1
	var numTrees = -1
	var param = ""
	var paramval = -1
	var validateLB = -1.0
	var validateUB = -1.0
	val h = 1.0 / nFolds;
	val RMSE  = Array.fill(numModels)(0.0)
	
	# CREATE K FOLDS
	val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)
	
	
	# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY ACCURACY
	for (i <- 0 to (nFolds-1)) {
	    validateLB = i * h
	    validateUB = (i + 1) * h
	    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
	    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
	    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
	    validationLabPt.cache()
	    trainCVLabPt.cache()
	
	    for (nParamSets <- 0 to (numModels-1)) {
	        for (nParams <- 0 to (numParamsinGrid-1)) {
	            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
	            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
	            if (param == "maxDepth") {maxDepth = paramval}
	            if (param == "numTrees") {numTrees = paramval}
	        }
	        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=numTrees, maxDepth=maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	        val labelAndPreds = validationLabPt.map { point =>
	                                                 val prediction = rfModel.predict(point.features)
	                                                 ( prediction, point.label )
	                                                }
	        val validMetrics = new RegressionMetrics(labelAndPreds)
	        val rmse = validMetrics.rootMeanSquaredError
	        RMSE(nParamSets) += rmse
	    }
	    validationLabPt.unpersist();
	    trainCVLabPt.unpersist();
	}
	val minRMSEindex = RMSE.indexOf(RMSE.min)
	
	# GET BEST PARAMETERS FROM CV AND PARAMETER SWEEP 
	var best_maxDepth = -1
	var best_numTrees = -1
	for (nParams <- 0 to (numParamsinGrid-1)) {
	    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
	    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
	    if (param == "maxDepth") {best_maxDepth = paramval}
	    if (param == "numTrees") {best_numTrees = paramval}
	}
	
	# CREATE BEST MODEL WITH BEST PARAMETERS AND FULL TRAIING DATASET
	val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
	                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
	                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
	
	# SAVE BEST RF MODEL IN BLOB STORAGE
	val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
	val modelName = "BestCV_RF_Regression__"
	val filename = modelDir.concat(modelName).concat(datestamp)
	best_rfModel.save(sc, filename);
	
	# PREDICT ON TRAINING SET WITH BEST MODEL AND EVALUATE
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = best_rfModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2
	
	# GET TIME TO RUN THE CELL
	val endtime = Calendar.getInstance().getTime()
	val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
	println("Time taken to run the above cell: " + elapsedtime + " seconds.");


	# LOAD THE MODEL
	val savedRFModel = RandomForestModel.load(sc, filename)
	
	val labelAndPreds = indexedTESTreg.map { point =>
	                                        val prediction = savedRFModel.predict(point.features)
	                                        ( prediction, point.label )
	                                       }
	# TEST THE MODEL
	val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
	val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**SORTIE :**

Durée d’exécution de la cellule ci-dessus : 61 secondes.


## Utiliser des modèles ML basés sur Spark générés automatiquement avec Scala

La procédure utilisant du code Scala pour charger automatiquement et noter les nouveaux jeux de données avec des modèles ML basés sur Spark et enregistrés dans des objets Blob Azure est fournie dans la rubrique [Noter les modèles Machine Learning créés avec Spark](machine-learning-data-science-spark-model-consumption.md). Les utilisateurs peuvent suivre les instructions fournies et simplement remplacer le code Python avec le code Scala fourni ci-dessus pour activer la consommation automatisée.

## Étapes suivantes

Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de science des données dans Azure, consultez [processus de science des données pour les équipes](http://aka.ms/datascienceprocess).

Pour une description des procédures pas à pas détaillées qui illustrent les étapes du processus TDSP (Team Data Science Process) pour **des scénarios spécifiques** et expliquent comment combiner les outils et services dans le cloud et en local dans un flux de travail ou un pipeline pour créer une application intelligente, consultez les [Procédures pas à pas du processus TDSP (Team Data Science Process)](data-science-process-walkthroughs.md).

<!---HONumber=AcomDC_0803_2016-->