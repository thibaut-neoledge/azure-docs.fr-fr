<properties 
	pageTitle="Processus d'analyse avancé et technologie en action : utilisation des clusters Hadoop HDInsight sur le groupe de données Criteo de 1 To | Azure" 
	description="Utilisation du processus d'analyse avancé et technologie (ADAPT) pour un scénario de bout en bout employant un cluster Hadoop HDInsight pour créer et déployer un modèle à l'aide d'un groupe de données volumineux (1 To), disponible publiquement." 
	metaKeywords="" 
	services="machine-learning,hdinsight" 
	solutions="" 
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
	ms.date="07/21/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Processus d'analyse avancé et technologie en action : utilisation des clusters Hadoop Azure HDInsight sur un groupe de données de 1 To

Dans cette procédure pas à pas, nous vous indiquons comment utiliser le processus d'analyse avancé et la technologie (ADAPT) de bout en bout avec un [cluster Hadoop Azure HDInsight](http://azure.microsoft.com/services/hdinsight/) pour stocker, explorer, concevoir des fonctionnalités et réduire l’échantillon des données d'un des groupes de données[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), disponibles publiquement. Nous utilisons Azure Machine Learning pour créer un modèle de classification binaire sur ces données. Nous vous expliquons également comment publier un de ces modèles en tant que service Web.

Il est également possible d'utiliser un interpréteur IPython notebook pour accomplir les tâches présentées dans cette procédure pas à pas. Les utilisateurs qui souhaitent essayer cette approche doivent consulter la rubrique [Procédure pas à pas Criteo à l'aide d'une connexion Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Description du groupe de données Criteo

Les données Criteo représentent un groupe de données de prédiction de clic d'environ 370 Go de fichiers TSV compressés au format gzip (~1,30 To de fichiers non compressés), comprenant plus de 4,3 milliards d'enregistrements. Elles proviennent de 24 jours de données de clic, disponibles via [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Pour la commodité des scientifiques de données, nous avons décompressé les données mises à notre disposition pour pouvoir les expérimenter.

Chaque enregistrement de ce groupe de données est constitué de 40 colonnes :

- la première colonne est une colonne étiquette qui vous indique si un utilisateur clique sur un ajout (valeur 1) ou non (valeur 0) 
- Les 13 colonnes suivantes sont numériques, et 
- les 26 dernières sont des colonnes catégorielles 

Les colonnes sont anonymes et utilisent une série de noms énumérés : « Col1 » (pour la colonne étiquette) à « Col40 » (pour la dernière colonne catégorielle).

Voici un extrait des 20 premières colonnes de deux observations (lignes) provenant de ce groupe de données :

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Des valeurs sont manquantes dans les colonnes numériques et catégorielles de ce groupe de données. Retrouvez, ci-dessous, une méthode simple qui vous permet de gérer les valeurs manquantes. Des informations supplémentaires relatives aux données sont disponibles ci-dessous lorsque nous les stockons dans les tables Hive.

**Définition  :** *Taux de clic :* pourcentage de clics effectués dans les données. Dans ce groupe de données Criteo, le taux de clic est d’environ 3,3 %, soit de 0,033.

## <a name="mltasks"></a>Exemples de tâches de prédiction
Cette procédure pas à pas aborde deux exemples de problèmes de prédiction :

1. **Classification binaire** : prédit qu’un utilisateur a cliqué ou non sur un ajout :
	- Classe 0 : aucun clic
	- Classe 1 : clic

2. **Régression** : prédit la probabilité d'un clic effectué sur une annonce à partir de fonctionnalités utilisateur.


## <a name="setup"></a>Configuration d’un cluster Hadoop HDInsight pour la science des données

**Remarque :** il s'agit généralement d’une tâche d’**administration**.

Configurez votre environnement de science des données Azure pour créer des solutions d'analyse prédictives avec les clusters HDInsight en trois étapes :

1. [Création d’un compte de stockage](storage-whatis-account.md) : ce compte de stockage est utilisé pour stocker des données dans Azure Blob Storage. Les données utilisées dans les clusters HDInsight sont stockées ici.

2. [Personnalisation des clusters Hadoop Azure HDInsight pour la science des données](machine-learning-data-science-customize-hadoop-cluster.md) : cette étape crée un cluster Hadoop Azure HDInsight avec Anaconda Python 2.7 64 bits installé sur tous les nœuds. Deux étapes importantes (décrites dans cette rubrique) doivent être suivies lors de la personnalisation du cluster HDInsight.

	* Vous devez lier le compte de stockage créé à l'étape 1 à votre cluster HDInsight, une fois sa création terminée. Ce compte de stockage est utilisé pour accéder aux données qui peuvent être traitées au sein du cluster.

	* Vous devez activer l'accès à distance au nœud principal du cluster après que celui-ci soit créée. N'oubliez pas les informations d'identification de l'accès à distance que vous spécifiez ici (différentes de celles qui sont spécifiées pour le cluster lors de sa création) : vous en aurez besoin juste après.

3. [Création d’un espace de travail Azure ML](machine-learning-create-workspace.md) : espace de travail Azure Machine Learning utilisé pour créer des modèles d’apprentissage automatique après avoir exploré des données initiales et réduit l’échantillon sur le cluster HDInsight.

## <a name="getdata"></a>Récupération et utilisation des données provenant d’une source publique

Pour accéder au groupe de données [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), cliquez sur le lien, acceptez les conditions d'utilisation et saisissez un nom. Vous trouverez ci-dessous une capture instantanée vous indiquant à quoi cela ressemble :

![Accepter les conditions de Criteo](http://i.imgur.com/hLxfI2E.png)

Cliquez sur **Poursuivre le téléchargement** pour en savoir plus sur le groupe de données et sa disponibilité.

Les données résident dans un emplacement de [stockage d'objets blob Azure](storage-dotnet-how-to-use-blobs.md) : wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. « wasb » fait référence à l'emplacement de stockage d'objets blob Azure.

1. Les données de ce stockage public d'objets blob sont constituées de trois sous-dossiers de données décompressées.
		
	1. Le sous-dossier *raw/count/* contient les 21 premiers jours de données, de day_00 à day_20
	2. Le sous-dossier *raw/train/* est constitué d'un seul jour de données, le day_21
	3. Le sous-dossier *brut/test/* est constitué de deux jours de données, le day_22 et le day_23

2. Pour les personnes souhaitant démarrer avec les données brutes gzip, celles-ci sont également disponibles dans le dossier principal *raw/* en tant que day_NN.gz, où NN va de 00 à 23.

Une autre approche vous permettant d’accéder, d’explorer et de modéliser ces données ne nécessitant aucun téléchargement local est expliquée plus loin dans cette procédure pas à pas lors de la création de tables Hive.

## <a name="login"></a>Connexion au nœud principal du cluster

Pour vous connecter au nœud principal du cluster, utilisez le portail de [gestion Azure](manage.windowsazure.com) afin de localiser le cluster. Cliquez sur l'icône d'éléphant HDInsight située sur la gauche et double-cliquez ensuite sur le nom de votre cluster. Accédez à l’onglet **Configuration**, double-cliquez sur l'icône CONNECTER en bas de la page et entrez les informations d'identification de votre accès à distance lorsque vous y êtes invité. Vous accédez ainsi au nœud principal du cluster.

Une première connexion au nœud principal de cluster ressemble généralement à ceci :

![Connexion au cluster](http://i.imgur.com/Yys9Vvm.png)

Sur la gauche, la « ligne de commande Hadoop », qui nous permettra d'explorer les données. De même que deux URL : « État Yarn Hadoop » et « Nœud de nom Hadoop ». L'URL de l'état Yarn indique la progression de la tâche et l'URL du nœud de nom vous fournit des informations relatives à la configuration du cluster.

Nous sommes désormais prêts à entamer la première partie de la procédure pas à pas : l’exploration de données à l'aide de Hive et la préparation de données pour Azure Machine Learning.

## <a name="hive-db-tables"></a>Création de la base de données et des tables Hive

Pour créer des tables Hive pour notre groupe de données Criteo, ouvrez la ***Ligne de commande Hadoop*** sur le bureau du nœud principal et saisissez le répertoire Hive en entrant la commande

    cd %hive_home%\bin

**REMARQUE IMPORTANTE** : **exécutez, dans cette procédure pas à pas, toutes les commandes Hive depuis l’invite de l’emplacement/du répertoire Hive mentionnée ci-dessus. Il se chargera automatiquement de tout problème lié au chemin d'accès. Nous utiliserons les termes « Invite du répertoire Hive », « Invite de l’emplacement/du répertoire Hive » et « Ligne de commande Hadoop » de manière interchangeable.**

**REMARQUE IMPORTANTE 2** : **pour exécuter une requête Hive, effectuez toujours l'action suivante :** cd %hive_home%\bin hive

Lorsque Hive REPL apparaît avec un signe « hive > », coupez-collez simplement la requête pour l'exécuter.

Le code ci-dessous crée une base de données « criteo » et génère ensuite 4 tables :


* une *table pour la génération de nombres* reposant sur les jours day_00 à day_20 ; 
* une *table à utiliser comme jeu de données d'apprentissage* reposant sur day_21 ; et 
* deux *tables à utiliser comme jeux de données de test* reposant sur day_22 et day_23 respectivement. 

Nous séparons notre groupe de données de test en deux tables bien distinctes, car l’une des journées est un jour férié, et nous voulons déterminer si le modèle peut différer un jour férié d’un jour ordinaire à partir du taux de clic.

Le script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) est affiché ci-dessous par commodité :

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Nous remarquons que toutes ces tables sont externes puisque nous désignons simplement les emplacements de stockage d'objets blob Azure (wasb).

**Il existe deux manières d'exécuter une requête Hive dont nous sommes en train de parler.**

1. **Utilisation de la ligne de commande Hive REPL**: la première consiste à émettre une commande « Hive » et à copier-coller la requête ci-dessus dans la ligne de commande Hive REPL. Pour ce faire, procédez comme suit :

		cd %hive_home%\bin
		hive

 	Dans la ligne de commande REPL, coupez-collez la requête qu’elle exécute.

2. **Enregistrement des requêtes dans un fichier et exécution de la commande** : la seconde consiste à enregistrer les requêtes dans un fichier .hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)), puis à utiliser la commande suivante pour exécuter la requête :

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Confirmation de la création de la base de données et des tables

Nous confirmons ensuite la création de la base de données en exécutant la commande ci-dessous à partir de l’invite de l'emplacement/du répertoire Hive :

		hive -e "show databases;"

Cela donne :

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Ceci confirme la création de la nouvelle base de données, « criteo ».

Pour découvrir quelles tables nous avons créées, nous exécutons simplement la commande ci-dessous à partir de l’invite de l’emplacement/du répertoire Hive :

		hive -e "show tables in criteo;"

Le résultat suivant s'affiche ensuite :

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a>Exploration des données dans Hive

Nous sommes désormais prêts à effectuer une exploration de données de base dans Hive. Nous commençons par compter le nombre d'exemples dans les tables de données de formation et de test.

### Nombre d'exemples de formation

Le contenu de [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) est indiqué ci-dessous :

		SELECT COUNT(*) FROM criteo.criteo_train;

Cela donne :

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

Vous pouvez également exécuter la commande ci-dessous à partir de l’invite de l’emplacement/du répertoire Hive :

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Nombre d'exemples de test dans les deux groupes de données de test

Nous comptons à présent le nombre d'exemples de test dans les deux groupes de données de test. Le contenu de [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) est indiqué ci-dessous :

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Cela donne :
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Comme d'habitude, nous pouvons également appeler le script à partir de l’invite de l’emplacement/du répertoire Hive en exécutant la commande suivante :

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Puis, nous examinons enfin le nombre d'exemples de test dans le groupe de données de test basé sur le day_23.

La commande à exécuter est semblable à celle mentionnée ci-dessus (consultez [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)) :

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Cela donne :
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Distribution d'étiquette dans le groupe de données de formation

La distribution d'étiquette dans le groupe de données de formation est intéressante. Pour la découvrir, nous vous indiquons le contenu de [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql) :

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Cela génère la distribution d'étiquette suivante :

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Notez que le pourcentage d'étiquettes positives est d’environ 3,3 % (cohérent avec le groupe de données d'origine).
		
### Les distributions de l’histogramme de certaines variables numériques dans le groupe de données de formation

Nous pouvons utiliser la fonction native « histogram_numeric » de Hive pour découvrir à quoi ressemble la distribution des variables numériques. Le contenu de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) est indiqué ci-dessous :

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Cela génère le résultat suivant :

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

La combinaison LATERAL VIEW - explode dans Hive permet de générer une sortie similaire à SQL au lieu de la liste habituelle. Notez que dans le tableau ci-dessus, la première colonne correspond au centre de l'emplacement et le second à la fréquence de l'emplacement.

### Les centiles approximatifs de certaines variables numériques dans le groupe de données de formation

Le calcul de centiles approximatifs avec des variables numériques est également intéressant. Le « percentile_approx» natif de Hive le fait pour nous. [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) contient :

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Cela donne :

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Nous constatons que, de manière générale, la distribution de centiles est étroitement liée à la distribution de l'histogramme de n'importe quelle variable numérique.

### Recherchez le nombre de valeurs uniques pour certaines colonnes catégorielles dans le groupe de données de formation

Pour poursuivre l'exploration de données, nous recherchons, pour certaines colonnes catégorielles, le nombre de valeurs uniques qu'ils utilisent. Pour ce faire, nous affichons le contenu de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql) :

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Cela donne :

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Nous constatons que Col15 possède des valeurs uniques 19M ! L’utilisation des techniques naïves, telles que « l’encodage à chaud » pour encoder des variables catégorielles de grande dimension, est tout bonnement impossible. Nous mentionnons notamment une technique puissante et robuste appelée [Apprentissage à l’aide de compteurs](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pour résoudre ce problème de manière efficace.

Nous terminons cette sous-rubrique en examinant le nombre de valeurs uniques pour d'autres colonnes catégorielles. [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) contient :

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

Cela donne :

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

Là encore, nous remarquons que, à l'exception de Col20, toutes les autres colonnes possèdent des valeurs uniques.

### Nombre de cooccurences de paires de variables catégorielles dans le groupe de données de formation

Le nombre de cooccurences de paires de variables catégorielles dans le groupe de données de formation est également intéressant. Il peut être déterminé à l’aide du code de [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql) :

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Nous inversons l’ordre des nombres par leur occurrence et nous nous penchons, dans ce cas, sur les 15 meilleurs. Cela nous donne :

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Réduction de l’échantillon des groupes de données pour Azure Machine Learning

Après avoir exploré les groupes de données et vous avoir expliqué comment nous pouvons effectuer ce type d’exploration pour toutes les variables (y compris les combinaisons), nous réduisons désormais l’échantillon des groupes de données pour pouvoir créer des modèles dans Azure Machine Learning N'oubliez pas le problème auquel nous sommes confrontés : dans un ensemble d'exemples d’attributs (valeurs de fonctionnalité de Col2 à Col40), nous prédisons si Col1 est défini par la valeur 0 (aucun clic) ou la valeur 1 (clic).

Pour réduire l’échantillon de nos groupes de données de formation et de test à 1 % de leur taille d'origine, nous utilisons la fonction RAND() native de Hive. Le script suivant, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql), effectue cette opération pour le groupe de données de formation :

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Cela donne :

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

Le script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) effectue ceci pour les données de test, du day_22 :

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Cela donne :

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Puis, le script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) effectue ceci pour les données de test du day_23 :

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Cela donne :

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

Nous sommes ainsi prêts à utiliser nos groupes de données de formation et de test à échantillon réduit pour créer des modèles dans Azure Machine Learning.

N’oublions pas de mentionner un composant très important qui concerne la table de comptage, avant d’aborder Azure Machine Learning. Dans la rubrique suivante, nous vous expliquons tout cela en détail.

##<a name="count"></a> Une brève discussion sur la table de comptage

Comme nous avons pu le remarquer, plusieurs variables catégorielles ont une dimensionnalité très élevée. Dans notre procédure pas à pas, nous vous présentons une technique puissante appelée [Apprentissage à l’aide de compteurs](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pour encoder ces variables de manière fiable et efficace. Plus d'informations sur cette technique sont indiquées dans le lien fourni.

**Remarque :** dans cette procédure pas à pas, nous nous penchons sur l'utilisation de tables de comptage nous permettant de produire des représentations compactes de fonctionnalités catégorielles de grande dimension. Ce n'est certainement pas la seule manière d’encoder des fonctionnalités catégorielles ; pour plus d'informations sur les autres techniques, les utilisateurs intéressés peuvent consulter les rubriques [Encodage à chaud](http://en.wikipedia.org/wiki/One-hot) et [Hachage de caractéristiques](http://en.wikipedia.org/wiki/Feature_hashing).

Pour créer des tables de comptage sur les données numériques, nous utilisons les données dans le dossier raw/count. Dans la rubrique de modélisation, nous expliquons aux utilisateurs comment créer de toutes pièces ces tables de comptage pour fonctionnalités catégorielles, ou également comment utiliser une table de comptage prédéfinie pour leurs explorations. Lorsque nous utilisons le terme de « tables de comptage prédéfinies », nous entendons par là l’utilisation des tables de comptage que nous fournissons. Des instructions détaillées sur l’accès à ces tables sont mentionnées ci-dessous.

## <a name="aml"></a>Création de modèles dans Azure Machine Learning

Notre processus de création de modèles dans Azure Machine Learning se déroulera de la manière suivante :

1. [Récupération des données des tables Hive dans Azure Machine Learning](#step1)
2. [Création de l'expérience : nettoyage des données, sélection d’un apprenant et caractérisation des tables de comptage](#step2)
3. [Formation du modèle](#step3)
4. [Notation du modèle sur les données de test](#step4)
5. [Évaluation du modèle](#step5)
6. [Publication du modèle en tant que service Web à utiliser](#step6)

Nous sommes désormais prêts à créer des modèles dans Azure Machine Learning Studio. Nos données à échantillon réduit sont enregistrées en tant que tables Hive dans le cluster. Nous utiliserons le module **Lecteur** d'Azure Machine Learning pour lire ces données. Les informations d'identification permettant d'accéder au compte de stockage de ce cluster sont indiquées ci-dessous.

### <a name="step1"></a> Étape 1 : Récupérer des données des tables Hive dans Azure Machine Learning à l'aide du module Lecteur et l’utiliser pour une expérience d'apprentissage automatique

Commencez par sélectionner **+NOUVEAU** -> **EXPÉRIENCE** -> **Nouvelle expérience vide**. Ensuite, dans la zone **Recherche** en haut à gauche, recherchez « Reader » (Lecteur). Effectuez un glisser-déplacer du module **Lecteur** sur la zone de dessin d'expérience (partie centrale de l'écran) pour utiliser le module afin d'accéder aux données.

Voici à quoi ressemble le **Lecteur** lors de la récupération des données d’une table Hive :

![Lecteur récupère les données](http://i.imgur.com/i3zRaoj.png)

Pour le module **Lecteur**, les valeurs des paramètres qui sont fournies dans le graphique servent uniquement d'exemples. Voici quelques instructions générales portant sur le « remplissage » des paramètres pour le module **Lecteur**.

1. Choisissez « Requête Hive » pour la **source de données**.
2. Dans la zone de **requête de base de données Hive**, une simple opération SELECT * FROM <nom_de_votre_base_de_données.nom_de_votre_table> - suffit.
3. **URI du serveur Hcatalog** : si votre cluster se nomme « abc », vous aurez donc : https://abc.azurehdinsight.net
4. **Nom du compte utilisateur Hadoop** : nom d'utilisateur choisi lors de la mise en service du cluster. PAS le nom d'utilisateur à distance.
5. **Nom du compte utilisateur Hadoop** : mot de passe associé au nom d'utilisateur choisi lors de la mise en service du cluster. PAS le mot de passe de l'accès à distance.
6. **Emplacement des données de sortie** : choisissez « Azure »
7. **Nom du compte de stockage Azure** : le compte de stockage associé au cluster.
8. **Clé du compte de stockage Azure** : la clé du compte de stockage associé au cluster.
9. **Nom du conteneur Azure** : si le nom du cluster est « abc », il se nommera tout simplement « abc ». 


Dès lors que le **Lecteur** a récupéré les données (vous apercevez la coche verte sur le module), enregistrez-les en tant que groupe de données (avec le nom de votre choix). Cela ressemble à :

![Lecteur enregistre les données](http://i.imgur.com/oxM73Np.png)


Cliquez avec le bouton droit sur le port de sortie du module **Lecteur**. Ceci fait apparaître une option **Enregistrer en tant que groupe de données** et une option **Visualiser**. L’option **Visualiser**, si vous cliquez dessus, fera apparaître les 100 lignes de données, ainsi qu’un panneau, situé à droite, qui sera très utile pour certaines statistiques de résumé. Pour enregistrer les données, sélectionnez simplement **Enregistrer en tant que groupe de données** et suivez les instructions.

Pour sélectionner le groupe de données enregistré et l’utiliser dans une expérience d'apprentissage automatique, recherchez les groupes de données à l'aide de la zone **Recherche** mentionnée ci-dessous. Puis tapez simplement une partie du nom attribué au groupe de données pour accéder à celui-ci et faites-le glisser sur le panneau principal. En le déposant sur le panneau principal, ce groupe de données est sélectionné pour être utilisé dans la modélisation de l’apprentissage automatique.

![](http://i.imgur.com/cl5tpGw.png)

***REMARQUE IMPORTANTE :*** **Réalisez cette opération pour les groupes de données d'apprentissage et de test. En outre, n'oubliez pas d'utiliser le nom de la base de données et les noms de tables attribués à cet effet. Les valeurs utilisées dans l'illustration sont uniquement à des fins d'illustration.**
 
### <a name="step2"></a> Étape 2 : Créer une expérience simple dans Azure Machine Learning pour prédire les clics effectués/non effectués

Notre expérience Azure ML ressemble à ce qui suit :

![](http://i.imgur.com/xRpVfrY.png)

Examinons maintenant les composants clés de cette expérience. En guise de rappel, nous devons d'abord faire glisser nos jeux de données d'apprentissage et de test sur la zone de dessin de l'expérience.

#### Nettoyage des données manquantes

Le module **Nettoyer des données manquantes**, comme son nom l’indique, nettoie les données manquantes via des méthodes qui peuvent être spécifiées par l'utilisateur. Dans ce module, nous pouvons voir ceci :

![Nettoyage des données manquantes](http://i.imgur.com/0ycXod6.png)

Nous avons décidé ici de remplacer toutes les valeurs manquantes par 0. D'autres options, mentionnées dans les listes déroulantes du module, sont également proposées.

#### Conception de fonctionnalités sur les données

Certaines fonctionnalités catégorielles de jeux de données volumineux peuvent rassembler des millions de valeurs uniques. L'utilisation des techniques naïves, telles que l'encodage à chaud pour représenter des fonctionnalités catégorielles de grande dimension, est tout bonnement impossible. Dans cette procédure pas à pas, nous démontrons comment utiliser les fonctionnalités de comptage à l'aide de modules Azure Machine Learning intégrés pour générer des représentations compactes de ces variables catégorielles de grande dimension. Le résultat final est un modèle de plus petite taille, un apprentissage plus rapide et des mesures de performances tout à fait comparables à l'utilisation d'autres techniques.

##### Création de transformations de comptage

Pour créer des fonctionnalités de comptage, nous utilisons le module **Créer une transformation de comptage** qui est disponible dans Azure Machine Learning. Le module se présente ainsi :

![](http://i.imgur.com/e0eqKtZ.png) ![](http://i.imgur.com/OdDN0vw.png)

**Remarque importante** : dans la zone **Nombre de colonnes**, nous entrons les colonnes sur lesquelles nous souhaitons effectuer un comptage. En règle générale, il s'agit de colonnes catégorielles de grande dimension (comme indiqué). Au début, nous avons mentionné que le jeu de données Criteo possède 26 colonnes catégorielles : de Col15 à Col40. Ici, nous effectuons un comptage sur chacune d'elles et donnons leurs index (de 15 à 40 séparés par des virgules, comme indiqué).

Pour utiliser le module en mode MapReduce (adapté aux grands ensembles de données), nous devons accéder à un cluster HDInsight Hadoop (celui utilisé pour l'exploration de la fonctionnalité ci-dessus peut être réutilisé à cet effet) et ses informations d'identification. Les valeurs renseignées (remplacez les valeurs fournies à titre d'illustration avec celles adaptées à votre propre cas d'utilisation) sont représentées dans les figures ci-dessus.

![](http://i.imgur.com/05IqySf.png)

Dans la figure ci-dessus, nous montrons comment entrer l'emplacement de l'objet blob en entrée. Cet emplacement comporte les données réservées pour la création de tables de comptage.


Une fois l'exécution de ce module terminée, nous pouvons enregistrer la transformation pour une utilisation ultérieure en cliquant avec le bouton droit sur le module et en sélectionnant l'option **Enregistrer en tant que transformation** :

![](http://i.imgur.com/IcVgvHR.png)

Dans notre architecture d'expérience illustrée ci-dessus, le jeu de données « ytransform2 » correspond précisément à une transformation de nombre enregistrée. Pour le reste de cette expérience, nous partons du principe que le lecteur a utilisé un module **Créer une transformation de comptage** sur certaines données pour générer des nombres et peut ensuite utiliser ces nombres pour générer des fonctionnalités de comptage sur les jeux de données d'apprentissage et de test.

##### Sélection des fonctionnalités de comptage à inclure dans les jeux de données d'apprentissage et de test

Lorsqu'une transformation de nombre est prête, l'utilisateur peut choisir les fonctionnalités à inclure dans les jeux de données d'apprentissage et de test à l'aide du module **Modifier les paramètres de la table de comptage**. Nous montrons ce module ci-dessous par souci d'exhaustivité, mais dans un souci de simplification nous ne l'utilisons pas dans notre expérience.

![](http://i.imgur.com/PfCHkVg.png)

Dans ce cas, comme vous pouvez le constater, nous avons choisi d'utiliser uniquement les probabilités de journalisation et d'ignorer la colonne d'interruption. Nous pouvons également définir des paramètres, tels que le seuil d'emplacement de la corbeille, le nombre de pseudo-exemples précédents à ajouter pour le lissage et s'il faut utiliser le bruit Laplacien ou non. Il s'agit là de fonctionnalités avancées et il est à noter que les valeurs par défaut sont un bon point de départ pour les utilisateurs qui débutent dans ce type de génération de fonctionnalité.

##### Transformation des données avant la génération des fonctionnalités de comptage

Nous nous concentrons maintenant sur un point important de la transformation de nos données d'apprentissage et de test avant de générer réellement les fonctions de comptage. Notez qu'il existe deux modules **Exécuter le script R** utilisés avant que nous appliquions la transformation de nombre à nos données.

![](http://i.imgur.com/aF59wbc.png)

Voici le premier script R :

![](http://i.imgur.com/3hkIoMx.png)

Dans ce script R, nous renommons nos colonnes « Col1 » à « Col40 ». La transformation de nombre attend des noms dans ce format.

Dans le deuxième script R, nous équilibrons la distribution entre les classes positives et négatives (classes 1 et 0 respectivement) en sous-échantillonnant la classe négative. Le script R ci-dessous montre comment procéder :

![](http://i.imgur.com/91wvcwN.png)

Dans ce script R simple, nous utilisons « pos_neg_ratio » pour définir l'équilibre entre les classes positive et négatives. Ceci est important car l'amélioration du déséquilibre des classes a, en général, des avantages en matière de performances pour les problèmes de classification où la distribution des classes est déséquilibrée (n'oubliez pas que dans notre cas, nous avons 3,3 % de classe positive et 96,7 % de classe négative).

##### Application de la transformation de nombre à nos données

Enfin, nous pouvons utiliser le module **Appliquer la transformation** pour appliquer les transformations de nombre à nos jeux de données d'apprentissage et de test. Ce module prend la transformation de nombre enregistrée comme une entrée et les jeux de données d'apprentissage ou de test comme l'autre entrée, et il renvoie les données avec des fonctionnalités de nombre. Consultez l’illustration ci-dessous :

![](http://i.imgur.com/xnQvsYf.png)

##### Un exemple de l'aspect des fonctionnalités de nombre

Il est intéressant de voir à quoi les fonctionnalités de nombre ressemblent dans notre cas. Vous en trouverez un exemple ci-dessous :

![](http://i.imgur.com/FO1nNfw.png)

Dans cet exemple, nous montrons que pour les colonnes sur lesquelles nous avons exécuté le comptage, nous obtenons les nombres et les probabilités de journalisation, en plus des interruptions pertinentes.

Nous sommes maintenant prêts à construire un modèle Azure Machine Learning à l'aide de ces jeux de données transformés. Dans la section suivante, nous expliquons comment procéder.

#### Création de modèle Azure Machine Learning

##### Choix de l'apprenant

Nous devons tout d'abord choisir un apprenant. Nous utiliserons un arbre de décision optimisé à deux classes comme apprenant. Voici les options par défaut pour cet apprenant :

![](http://i.imgur.com/bH3ST2z.png)

Pour notre expérience, nous allons simplement choisir les valeurs par défaut. Vous constaterez que les valeurs par défaut sont généralement significatives et permettent d'obtenir rapidement des lignes de base pour les performances. Vous pouvez améliorer les performances en balayant les paramètres si vous le souhaitez, une fois que vous avez obtenu une ligne de base.

#### Formation du modèle

Pour l'apprentissage, nous appelons simplement un module **Former le modèle**. Les deux entrées dans celui-ci sont l'apprenant Arbre de décision optimisé à deux classes et notre jeu de données d'apprentissage. Consultez l'illustration ci-dessous :

![](http://i.imgur.com/2bZDZTy.png)

#### Notation du modèle

Une fois que nous avons formé un modèle, nous sommes prêts à noter le jeu de données de test et à évaluer ses performances. Pour cela, nous utilisons le module **Noter le modèle** ci-dessous, ainsi qu'un module **Évaluer le modèle** :

![](http://i.imgur.com/fydcv6u.png)

### <a name="step5"></a> Étape 5 : Évaluer le modèle

Pour finir, nous souhaiterions analyser les performances du modèle. Pour les deux problèmes de classification (binaire) à deux classes, l’ASC est généralement une très bonne mesure. Pour visualiser ceci, nous raccordons le module **Noter le modèle** à un module **Évaluer le modèle**. Cliquer sur **Visualiser** sur le module **Évaluer le modèle** génère un graphique tel que celui mentionné ci-dessous :

![Évaluation du modèle du module BDT](http://i.imgur.com/0Tl0cdg.png)

Pour des problèmes de classification binaire (à deux classes), l'aire sous la courbe (ASC) est une très bonne mesure de précision des prédictions. Nous présentons ci-dessous nos résultats à l'aide de ce modèle sur notre groupe de données de test. Pour obtenir ceci, cliquez avec le bouton droit sur le port de sortie du module **Évaluer le modèle** et sélectionnez **Visualiser**.

![Visualisation du module Évaluer le modèle](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Étape 6 : Publier le modèle en tant que service Web
La possibilité de publier un modèle Azure Machine Learning en tant que services Web avec un minimum de complications est une fonctionnalité utile qui se doit d'être largement accessible. Une fois cela fait, tout utilisateur peut effectuer des appels vers le service Web avec des données d'entrée pour lesquelles il a besoin de prédictions et le service Web utilise le modèle pour renvoyer ces prédictions.

Pour ce faire, nous enregistrons tout d'abord notre modèle formé en tant qu’ objet de modèle formé. Cette opération s'effectue en cliquant avec le bouton droit sur le module **Former le modèle** et en utilisant l'option **Enregistrer en tant que modèle formé**.

Ensuite, nous devons créer des ports d'entrée et de sortie pour notre service Web :

* un port d'entrée prend les données au même format que les données pour lesquelles nous avons besoin de prédictions, 
* un port de sortie renvoie les Étiquettes notées et les probabilités associées.

#### Sélectionnez quelques lignes de données pour le port d'entrée

Il est pratique d'utiliser un module **Transformation Apply SQL** pour ne sélectionner que 10 lignes en tant que données de port d'entrée. Sélectionnez uniquement ces lignes de données pour notre port d'entrée à l'aide de la requête SQL indiquée ci-dessous.

![Données du port d'entrée](http://i.imgur.com/XqVtSxu.png)

#### Service Web
Nous sommes désormais prêts à exécuter une petite expérience qui peut être utilisée pour publier notre service Web.

#### Générer des données d'entrée pour le service Web

Puisque la table de comptage est volumineuse, nous utilisons, avant toute chose, quelques lignes de données de test et nous créons des données de sortie à l’aide des fonctionnalités de comptage. Ceci servira de format de données d'entrée pour notre service Web. Consultez l’illustration ci-dessous.

![Création des données d'entrée BDT](http://i.imgur.com/OEJMmst.png)

Remarque : pour le format de données d'entrée, nous utiliserons la SORTIE du module **Caractériseur de comptage**. Une fois l’expérience terminée, enregistrez la sortie du module **Caractériseur de comptage** en tant que groupe de données.

**Remarque importante :** ce groupe de données sera utilisé pour les données d'entrée dans le service Web.

#### Expérience d’évaluation de la publication du service Web

Tout d’abord, nous vous indiquons ci-dessous à quoi cela ressemble. La structure essentielle est un module **Noter le modèle** qui accepte notre objet de modèle formé ainsi que quelques lignes de données d'entrée que nous avons créées lors des étapes précédentes à l'aide du module **Caractériseur de comptage**. Nous utilisons « Colonnes de projet » pour projeter les Étiquettes notées et Probabilités notées

![Colonnes de projet](http://i.imgur.com/kRHrIbe.png)

Notez comment le module **Colonnes de projet** peut être utilisé pour le « filtrage » des données d'un groupe de données. Nous vous indiquons ci-dessous le contenu :

![Filtrage du module Colonnes de projet](http://i.imgur.com/oVUJC9K.png)

Pour obtenir les ports d'entrée et de sortie bleus, vous cliquez simplement sur **préparer le service Web**, situé en bas à droite. Exécuter cette expérience nous permet également de publier le service Web en cliquant sur l’icône **PUBLIER LE SERVICE WEB** située en bas à droite et indiquée ci-dessous.

![Publication du service Web](http://i.imgur.com/WO0nens.png)

Une fois le service Web publié, nous sommes redirigés vers une page similaire à celle illustrée ci-dessous :

![](http://i.imgur.com/YKzxAA5.png)

Nous apercevons deux liens pour les services Web sur le côté gauche :

* Le service **REQUÊTE/RÉPONSE** (ou RR) est destiné à des prédictions uniques, que nous utiliserons dans cet atelier. 
* Le service d'**EXÉCUTION DE LOTS** (BES) est utilisé pour les prédictions de lots et requiert que les données en entrée sur lesquelles sont effectuées des prédictions, résident dans un stockage d'objets blob Azure.

Cliquer sur le lien **REQUÊTE/RÉPONSE** nous redirige vers une page qui nous fournit du code prédéfini en C#, python et R. Ce code peut être utilisé pour appeler le service Web. Notez que la clé API doit être utilisée sur cette page pour l'authentification.

Copier ce code python sur une nouvelle cellule dans l'interpréteur IPython notebook s'avère très pratique.

Nous vous présentons ci-dessous un segment de code python comportant la clé API appropriée.

![Code Python](http://i.imgur.com/f8N4L4g.png)

Notez que nous avons remplacé la clé d'API par défaut par la clé d'API de nos services Web. Cliquez sur **Exécuter** sur cette cellule dans un IPython notebook pour générer la réponse suivante :

![Réponse IPython](http://i.imgur.com/KSxmia2.png)

Nous remarquons que pour les deux exemples de test sur lesquels nous nous sommes penchés (dans l’infrastructure JSON du script python), nous obtenons des réponses sous forme d’« Étiquettes notées, de probabilités notées ». Notez que dans ce cas, nous choisissons les valeurs par défaut que le code prédéfini fournit (0 pour toutes les colonnes numériques et la chaîne « valeur » pour toutes les colonnes catégorielles).

Ceci conclut notre procédure pas à pas illustrant comment gérer un jeu de données à grande échelle à l'aide d'Azure Machine Learning. Nous avons démarré avec un téraoctet de données, nous avons construit un modèle de prévision et l'avons déployé en tant que service Web dans le cloud.

<!---HONumber=July15_HO4-->