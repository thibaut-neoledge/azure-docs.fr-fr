<properties
	pageTitle="Science des données évolutive dans Azure Data Lake : une procédure de bout en bout | Microsoft Azure"
	description="Comment utiliser Azure Data Lake pour effectuer des tâches d’exploration de donnés et de classification binaire sur un jeu de données."  
	services="machine-learning"
	documentationCenter=""
	authors="bradsev,wguo123"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="bradsev;weig"/>


# Science des données évolutive dans Azure Data Lake : une procédure de bout en bout

Cette procédure de bout en bout montre comment utiliser Azure Data Lake pour effectuer des tâches d’exploration de données et de classification binaire sur un échantillon de jeu de données NYC taxi trip and fare afin de prédire si le pourboire est compris dans le prix du billet. Elle vous guide tout au long du [processus de science des données](http://aka.ms/datascienceprocess) de bout en bout, depuis l’acquisition de données à l’apprentissage du modèle et au déploiement d’un service web qui publie le modèle.

**Service Analytique Azure Data Lake**

Le [Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) dispose de toutes les fonctionnalités requises pour faciliter le travail des scientifiques de données relatif au stockage des données de toute taille, forme et vitesse et à la réalisation du traitement des données, d’analyses avancées et de modélisation Machine Learning de manière hautement évolutive et à moindre coût. Le paiement s’effectue par travail effectué, seulement lorsque les données sont réellement en cours de traitement. Azure Data Lake Analytics inclut U-SQL, un langage mêlant la nature déclarative du langage SQL à la puissance expressive de C# pour offrir une capacité de requête distribuée évolutive. Elle vous permet de traiter des données non structurées en appliquant des schémas lors de la lecture et d’intégrer une logique personnalisée et des UDF, et a l’extensibilité nécessaire pour permettre un contrôle précis de l’exécution à grande échelle. Pour en savoir plus sur la philosophie de conception sous-jacente à U-SQL, voir ce [Billet de blog Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Il est également un composant essentiel de Cortana Analytics Suite et fonctionne avec Azure SQL Data Warehouse, Power BI et Data Factory. Cela vous offre une plateforme d’analyse avancée et de Big Data cloud complète.

Cette procédure pas à pas commence par la description des conditions préalables et des ressources nécessaires pour effectuer les tâches avec Data Lake Analytics composant le processus de science des données et de leur installation. Ensuite, il décrit les étapes de ce processus accompli à l’aide d’U-SQL et d’Azure Machine Learning et comment utiliser Python pour accomplir des tâches similaires.


**U-SQL et Azure Machine Learning**

Cette procédure pas à pas utilise Visual Studio pour modifier des scripts U-SQL pour traiter le jeu de données. Ces scripts sont décrits ici et fournis dans un fichier distinct. Cette procédure inclut l’ingestion, l’exploration et l’échantillonnage des données. Elle montre ensuite comment exécuter une tâche de script U-SQL à partir du portail Azure. Des tables Hive sont créées pour les données se trouvant dans un cluster HDInsight associé afin de faciliter la création et le déploiement d’un modèle de classification binaire dans Azure Machine Learning Studio.


**Python**

Cette procédure pas à pas contient également une section montrant comment effectuer ces tâches de science des données à l’aide de Python sur un échantillon du jeu de données des courses et tarifs de taxi à New York Nous fournissons un bloc-notes Jupyter avec les scripts Python pour chacune des étapes de ce processus. Le bloc-notes inclut le code de certaines étapes d’ingénierie de fonctionnalités supplémentaires et de modèles construction, comme la modélisation de la régression et de la classification multiclasse qui s’ajoute au modèle de classification binaire présenté ici. La tâche de régression consiste à prédire le montant du pourboire en fonction d’autres fonctionnalités liées au pourboire.


**Scripts**

Seules les principales étapes sont décrites dans cette procédure pas à pas. Vous pouvez télécharger la version complète du **script U-SQL** et du **bloc-notes Jupyter** à partir de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).


## Configuration requise

Avant de commencer ces rubriques, vous devez disposer des éléments suivants :

- Un abonnement Azure. Si vous n’en avez pas, consultez [Obtenir une version d’évaluation gratuite Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- [Recommandé] Visual Studio 2013 ou 2015. Si vous ne disposez pas d’une de ces versions, vous pouvez télécharger une édition Community gratuite [ici](https://www.visualstudio.com/visual-studio-homepage-vs.aspx). Cliquez sur le bouton **Download Community 2015** (Télécharger Community 2015) situé sous la section Visual Studio.

>[AZURE.NOTE] Vous pouvez également utiliser le portail Azure au lieu de Visual Studio pour envoyer des requêtes Azure Data Lake. Nous fournissons des instructions sur la façon de procéder avec Visual Studio et sur le portail dans la section intitulée **Traiter des données avec U-SQL**.

- Inscription à la version préliminaire d’Azure Data Lake

>[AZURE.NOTE] Vous devez obtenir l’autorisation d’utiliser Azure Data Lake Store (ADLS) et Azure Data Lake Analytics (ADLA) dans la mesure où ces services sont en version préliminaire. Vous serez invité à vous inscrire lors de la création de votre premier ADLS ou ADLA. Pour ce faire, cliquez sur **S’inscrire à la version préliminaire**, lisez le contrat, puis cliquez sur **OK**. Voici, par exemple, la page d’inscription à ADLS :

 ![2](./media/machine-learning-data-science-process-data-lake-walkthough/ADLA-preview-signup.PNG)
 


## Préparer l’environnement de science des données pour Azure Data Lake
Pour préparer l’environnement de science des données pour cette procédure pas à pas, créez les ressources suivantes :

- Azure Data Lake Store (ADLS) 
- Azure Data Lake Analytics (ADLA)
- Compte de stockage d’objets blob Azure
- Compte Azure Machine Learning Studio
- Outils Azure Data Lake pour Visual Studio (Recommandé)

Cette section fournit des instructions sur la création de chacune de ces ressources. Notez qu’un Azure Data Lake Store peut être créé séparément ou lorsque vous créez Azure Data Lake Analytics comme stockage par défaut, et, de même, le compte de stockage d’objets blob Azure peut être créé séparément ou en tant que stockage par défaut lors de la création d’un cluster HDInsight Linux. Il est fait référence à des instructions pour la création distincte de chacune des ressources ci-dessous. Toutefois, il n’est pas nécessaire de créer séparément les deux comptes de stockage.

### Créer un Azure Data Lake Store

Créez un ADLS à partir du [portail Azure](http://ms.portal.azure.com). Pour en savoir plus, voir [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Veillez à définir l’identité AAD de cluster dans le panneau **DataSource** de **Configuration facultative** décrit ici.

 ![3](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLS.PNG)


### Créer un compte Azure Data Lake Analytics
Créez un compte ADLA à partir du [portail Azure](http://ms.portal.azure.com). Pour en savoir plus, voir [Didacticiel : Prise en main du service Azure Data Lake Analytics à l’aide du portail Azure](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/machine-learning-data-science-process-data-lake-walkthough/create_ADLA_new.PNG)


### Créer un compte de stockage d’objets blob Azure
Créez votre compte de stockage d’objets blob Azure à partir du [portail Azure](http://ms.portal.azure.com). Pour en savoir plus, voir la section Créer un compte de stockage de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).
	
 ![5](./media/machine-learning-data-science-process-data-lake-walkthough/Create_Azure_Blob.PNG)


### Configurer un compte Azure Machine Learning Studio
Inscrivez-vous/Connectez-vous à Azure Machine Learning Studio depuis la page [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Cliquez sur le bouton **Commencer dès maintenant**, puis choisissez un « espace de travail libre» ou un « espace de travail standard ». Après cela, vous serez en mesure de créer des expériences dans Azure ML Studio.

### Installer les outils Azure Data Lake 
Installez les outils Azure Data Lake pour votre version de Visual Studio à partir de la page [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) (Outils Azure Data Lake pour Visual Studio).

 ![10](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS.PNG)

Une fois l’installation terminée, ouvrez Visual Studio. L’onglet Data Lake doit apparaître dans le menu supérieur. Vos ressources Azure doivent apparaître dans le volet gauche, lorsque vous vous connectez à votre compte Azure.

 ![11](./media/machine-learning-data-science-process-data-lake-walkthough/install_ADL_tools_VS_done.PNG)


## Jeu de données NYC Taxi Trips
Le jeu de données utilisé ici ([Jeu de données NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/)) est disponible au public. Les données NYC Taxi Trip sont constituées de fichiers CSV compressés d’une taille totale approximative de 20 Go (soit environ 48 Go après la décompression des fichiers), correspondant à plus de 173 millions de courses et au prix de chacune. Chaque enregistrement de course inclut le lieu et l’heure d’embarquement et de débarquement, le numéro de licence (du chauffeur) rendu anonyme et le numéro de médaillon (numéro d’identification unique) du taxi. Les données portent sur toutes les courses effectuées en 2013 et sont fournies dans les deux jeux de données ci-après pour chaque mois :

 - Le fichier CSV trip\_data contient les détails de chaque course, comme le nombre de passagers, les points d’embarquement et de débarquement, la durée du trajet et la distance parcourue. Voici quelques exemples d’enregistrements :

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count, trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

 - Le fichier CSV trip\_fare contient les détails du prix de chaque course, comme le type de paiement, le prix de la course, les suppléments et les taxes, les pourboires et les péages, ainsi que le montant total réglé. Voici quelques exemples d’enregistrements :

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clé unique permettant de joindre trip\_data et trip\_fare se compose des trois champs suivants : medallion (médaillon), hack\_licence (licence de taxi) et pickup\_datetime (date et heure d’embarquement). Les fichiers CSV bruts sont accessibles à partir d’un objet blob de stockage Azure public.

## Traiter des données avec U-SQL

Les tâches de traitement des données illustrées dans cette section incluent l’ingestion, la vérification de la qualité, l’exploration et l’échantillonnage des données. Nous montrons également comment joindre des tables relatives aux courses et aux tarifs. La section finale montre comment exécuter un travail de script U-SQL à partir du portail Azure. Voici les liens vers chaque sous-section :

- [Ingestion de données : données lues à partir d’un objet blob public](#ingest)
- [Contrôles de qualité des données](#quality)
- [Exploration des données](#explore)
- [Joindre des tables relatives aux courses et aux tarifs](#join)
- [Échantillonnage des données](#sample)
- [Exécuter des travaux U-SQL](#run)

Ces scripts sont décrits ici et fournis dans un fichier distinct. Vous pouvez télécharger la version complète des **scripts U-SQL** à partir de [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Pour exécuter U-SQL, ouvrez Visual Studio, cliquez sur **Fichier--> Nouveau--> Projet**, choisissez **Projet U-SQL**, nommez-le et enregistrez-le dans un dossier.

![12](./media/machine-learning-data-science-process-data-lake-walkthough/create_USQL_project.PNG)

>[AZURE.NOTE] Si vous utilisez le portail Azure pour exécuter U-SQL au lieu de Visual Studio, vous pouvez accéder à la ressource Azure Data Lake Analytics et suivre ces étapes illustrées pour envoyer des requêtes.


![29](./media/machine-learning-data-science-process-data-lake-walkthough/portal_submit_job.PNG)

### <a name="ingest"></a>Ingestion de données : données lues à partir d’un objet blob public

L’emplacement des données dans l’objet blob Azure, auquel il est fait référence sous la forme **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**, peut être extrait à l’aide de **Extractors.Csv()**. Remplacez vos propres nom de conteneur et nom de stockage dans les scripts suivants pour container_name@blob_storage_account_name dans l’adresse wasb. Étant donné que les noms de fichiers sont au même format, nous pouvons utiliser **trip\_data\_ {*} .csv** pour lire les 12 fichiers de course.

	///Read in Trip data
	@trip0 =
	    EXTRACT 
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string
    // This is reading 12 trip data from blob
    FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
    USING Extractors.Csv();

Dans la mesure où la première ligne comporte des en-têtes, nous devons les supprimer et modifier les types de colonne afin de les adapter. Vous pouvez enregistrer les données traitées dans Azure Data Lake Storage à l’aide de **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name**_ ou dans un compte de stockage d’objets blob Azure à l’aide de **wasb://container_name@blob_storage_account_name.blob.core.windows.net/blob_name**.

	// change data types
	@trip =
	    SELECT 
	    medallion,
	    hack_license,
	    vendor_id,
	    rate_code,
	    store_and_fwd_flag,
	    DateTime.Parse(pickup_datetime) AS pickup_datetime,
	    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
	    Int32.Parse(passenger_count) AS passenger_count,
	    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
	    Double.Parse(trip_distance) AS trip_distance,
	    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
	    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
	    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
	    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
    FROM @trip0
    WHERE medallion != "medallion";

	////output data to ADL
	OUTPUT @trip   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_trip.csv"
	USING Outputters.Csv(); 

	////Output data to blob
	OUTPUT @trip   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
	USING Outputters.Csv();  

De même, nous pouvons lire les jeux de données relatifs aux tarifs. Cliquez avec le bouton droit sur Azure Data Lake Store. Vous pouvez choisir de consulter vos données dans **Portail Azure--> Explorateur de données** ou **Explorateur de fichiers** dans Visual Studio.

 ![13\.](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL_VS.PNG)

 ![14](./media/machine-learning-data-science-process-data-lake-walkthough/data_in_ADL.PNG)


### <a name="quality"></a>Contrôles de qualité des données

Une fois les tables relatives aux courses et aux tarifs lues, les contrôles de qualité des données peuvent être effectués de la façon suivante. Les fichiers CSV qui en résultent peuvent être sortis dans le stockage d’objets blob Azure ou Azure Data Lake Store.

Recherchez le nombre de médaillons et le nombre unique de médaillons :

	///check the number of medallions and unique number of medallions
	@trip2 =
	    SELECT
	    medallion,
	    vendor_id,
	    pickup_datetime.Month AS pickup_month
	    FROM @trip;
	
	@ex_1 =
	    SELECT
	    pickup_month, 
	    COUNT(medallion) AS cnt_medallion,
	    COUNT(DISTINCT(medallion)) AS unique_medallion
	    FROM @trip2
	    GROUP BY pickup_month;
	    OUTPUT @ex_1   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
	USING Outputters.Csv(); 

Recherchez les medaillons ayant plus de 100 courses :

	///find those medallions that had more than 100 trips
	@ex_2 =
	    SELECT medallion,
	           COUNT(medallion) AS cnt_medallion
	    FROM @trip2
	    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
	    GROUP BY medallion
	    HAVING COUNT(medallion) > 100;
	    OUTPUT @ex_2   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
	USING Outputters.Csv(); 

Recherchez les enregistrements non valides en termes de pickup\_longitude :

	///find those invalid records in terms of pickup_longitude
	@ex_3 =
	    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
	    FROM @trip
	    WHERE
	    pickup_longitude <- 90 OR pickup_longitude > 90;
	    OUTPUT @ex_3   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
	USING Outputters.Csv(); 

Recherchez les valeurs manquantes pour certaines variables :

	//check missing values
	@res =
	    SELECT *,
	           (medallion == null? 1 : 0) AS missing_medallion
	    FROM @trip;
	
	@trip_summary6 =
	    SELECT 
	        vendor_id,
	    SUM(missing_medallion) AS medallion_empty, 
	    COUNT(medallion) AS medallion_total,
	    COUNT(DISTINCT(medallion)) AS medallion_total_unique  
	    FROM @res
	    GROUP BY vendor_id;
	OUTPUT @trip_summary6
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
	USING Outputters.Csv();



### <a name="explore"></a>Exploration des données

Nous pouvons effectuer des explorations de données pour obtenir une meilleure compréhension des données.

Recherchez la répartition des courses avec et sans pourboire :

	///tipped vs. not tipped distribution
	@tip_or_not =
	    SELECT *,
	           (tip_amount > 0 ? 1: 0) AS tipped
	    FROM @fare;
	
	@ex_4 =
	    SELECT tipped,
	           COUNT(*) AS tip_freq
	    FROM @tip_or_not
	    GROUP BY tipped;
	    OUTPUT @ex_4   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
	USING Outputters.Csv(); 

Recherchez la répartition du montant du pourboire avec les valeurs limites : 0, 5, 10 et 20 dollars.

	//tip class/range distribution
	@tip_class =
	    SELECT *,
	           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
	    FROM @fare;
	@ex_5 =
	    SELECT tip_class,
	           COUNT(*) AS tip_freq
	    FROM @tip_class
	    GROUP BY tip_class;
	    OUTPUT @ex_5   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
	USING Outputters.Csv(); 

Recherchez les statistiques de base de la distance de la course :

	// find basic statistics for trip_distance
	@trip_summary4 =
	    SELECT 
	        vendor_id,
	        COUNT(*) AS cnt_row,
	        MIN(trip_distance) AS min_trip_distance,
	        MAX(trip_distance) AS max_trip_distance,
	        AVG(trip_distance) AS avg_trip_distance 
	    FROM @trip
	    GROUP BY vendor_id;
	OUTPUT @trip_summary4
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
	USING Outputters.Csv();

Recherchez les percentiles de distance de la course :

	// find percentiles of trip_distance
	@trip_summary3 =
	    SELECT DISTINCT vendor_id AS vendor,
	                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
	                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
	    FROM @trip;
	   // group by vendor_id;
	OUTPUT @trip_summary3
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
	USING Outputters.Csv(); 


### <a name="join"></a>Joindre des tables relatives aux courses et aux tarifs

Les tables relatives aux courses et aux tarifs peuvent être jointes par médaillon, hack\_license et pickup\_time.

	//join trip and fare table

    @model_data_full =
    SELECT t.*, 
    f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
    (f.tip_amount > 0 ? 1: 0) AS tipped,
    (f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @trip AS t JOIN  @fare AS f
    ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
    WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

	//// output to blob
	OUTPUT @model_data_full   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 

	////output data to ADL
	OUTPUT @model_data_full   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_7_full_data.csv"
	USING Outputters.Csv(); 


Pour chaque niveau de nombre de passagers, calculez le nombre d’enregistrements, le montant moyen du pourboire, l’écart du montant du pourboire et le pourcentage de courses avec pourboire.

	// contigency table
	@trip_summary8 =
	    SELECT passenger_count,
	           COUNT(*) AS cnt,
	           AVG(tip_amount) AS avg_tip_amount,
	           VAR(tip_amount) AS var_tip_amount,
	           SUM(tipped) AS cnt_tipped,
	           (float)SUM(tipped)/COUNT(*) AS pct_tipped
	    FROM @model_data_full
	    GROUP BY passenger_count;
	    OUTPUT @trip_summary8
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
	USING Outputters.Csv();


### <a name="sample"></a>Échantillonnage des données

Tout d’abord, nous sélectionnons aléatoirement 0,1 % des données à partir de la table jointe :

	//random select 1/1000 data for modeling purpose
	@addrownumberres_randomsample =
	SELECT *,
	        ROW_NUMBER() OVER() AS rownum
	FROM @model_data_full;
	
	@model_data_random_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_randomsample
	WHERE rownum % 1000 == 0;
	
	OUTPUT @model_data_random_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
	USING Outputters.Csv(); 

Ensuite, nous procédons à un échantillonnage stratifié par variable binaire tip\_class :

	//stratified random select 1/1000 data for modeling purpose
	@addrownumberres_stratifiedsample =
	SELECT *,
	        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
	FROM @model_data_full;
	
	@model_data_stratified_sample_1_1000 =
	SELECT *
	FROM @addrownumberres_stratifiedsample
	WHERE rownum % 1000 == 0;
	//// output to blob
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 
	////output data to ADL
	OUTPUT @model_data_stratified_sample_1_1000   
	TO "swebhdfs://cdsp.azuredatalakestore.net/nyctaxi_weig/demo_ex_9_stratified_1_1000.csv"
	USING Outputters.Csv(); 


### <a name="run"></a>Exécuter des travaux U-SQL

Lorsque vous avez terminé la modification de scripts U-SQL, vous pouvez les envoyer au serveur à l’aide de votre compte Azure Data Lake Analytics. Cliquez sur **Data Lake**, **Envoyer le travail**, sélectionnez votre **compte Analytics**, choisissez **Parallélisme**, puis cliquez sur le bouton **Envoyer**.

 ![15](./media/machine-learning-data-science-process-data-lake-walkthough/submit_USQL.PNG)

Lorsque le travail est compilé avec succès, son état s’affiche dans Visual Studio pour la surveillance. Une fois le travail terminé, vous pouvez même relire le processus d’exécution du travail et rechercher les étapes de goulot d’étranglement afin d’améliorer l’efficacité. Vous pouvez également accéder au portail Azure pour vérifier l’état de vos travaux U-SQL.

 ![16](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_running_v2.PNG)


 ![17](./media/machine-learning-data-science-process-data-lake-walkthough/USQL_jobs_portal.PNG)


Vous pouvez maintenant vérifier les fichiers de sortie dans le stockage d’objets blob Azure ou sur le portail Azure. Nous utiliserons les exemples de données stratifiées pour notre modélisation lors de l’étape suivante.

 ![18](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv.PNG)

 ![19](./media/machine-learning-data-science-process-data-lake-walkthough/U-SQL-output-csv-portal.PNG)

## Créer et déployer des modèles dans Azure Machine Learning
Nous allons vous montrer deux options permettant d’extraire des données vers Azure Machine Learning. La première option consiste à utiliser les données échantillonnées écrites dans un objet blob Azure (à partir de l’étape d’**échantillonnage des données** ci-dessus) et Python pour créer un modèle et le déployer dans Azure Machine Learning. La deuxième option consiste à interroger les données d’Azure Data Lake directement à l’aide d’une requête Hive. Cette option nécessite la création d’un cluster HDInsight ou l’utilisation d’un cluster HDInsight existant, où les tables Hive pointent vers les données d’Azure Data Lake Storage. Nous abordons ces deux options ci-dessous.

### Option 1: Utiliser Python pour créer et déployer des modèles Machine Learning

Nous allons à présent créer et déployer des modèles Machine Learning à l’aide de Python. Nous pouvons utiliser Python pour lire les données traitées depuis le stockage d’objets blob Azure (enregistré avec U-SQL lors de l’étape d’**échantillonnage des données** ci-dessus) et générer des modèles. Créez un bloc-notes Jupyter dans votre machine locale ou dans Azure Machine Learning Studio. Nous allons seulement montrer les étapes de déploiement et de modélisation dans cet article. Le bloc-notes Jupyter contient l’intégralité du code nécessaire à l’exploration, à la visualisation des données, au développement de fonctionnalités, à la modélisation et au déploiement.

#### Importer des bibliothèques Python

Pour exécuter l’exemple de bloc-notes Jupyter ou le fichier de script Python, vous avez besoin des packages Python suivants. Si vous utilisez le service AzureML Notebook, ces packages sont préinstallés.

	import pandas as pd
	from pandas import Series, DataFrame
	import numpy as np
	import matplotlib.pyplot as plt
	from time import time
	import pyodbc
	import os
	from azure.storage.blob import BlobService
	import tables
	import time
	import zipfile
	import random
	import sklearn
	from sklearn.linear_model import LogisticRegression
	from sklearn.cross_validation import train_test_split
	from sklearn import metrics
	from __future__ import division
	from sklearn import linear_model
	from azureml import services


#### Lire les données à partir d’un objet blob

- Chaîne de connexion   

		CONTAINERNAME = 'test1'
		STORAGEACCOUNTNAME = 'XXXXXXXXX'
		STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
		BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
		blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
	
- Lire sous forme de texte

		t1 = time.time()
		data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
		t2 = time.time()
		print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))

 ![29](./media/machine-learning-data-science-process-data-lake-walkthough/python_readin_csv.PNG)
 
- Ajouter des noms de colonnes et des colonnes distinctes

		colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
		'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
		df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
	


- Modifier des colonnes pour les faire passer au format numérique

		cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
		'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
		,'tipped','tip_class','rownum']
		for col in cols_2_float:
		    df1[col] = df1[col].astype(float)

#### Créer des modèles Machine Learning

Ici, nous créons un modèle de classification binaire pour prédire s’il s’agit d’une course avec ou sans pourboire. Dans le bloc-notes Jupyter, vous trouverez deux autres modèles : un modèle de classification multiclasse et un modèle de régression.

- Nous devons d’abord créer des variables factices pouvant être utilisées dans des modèles scikit-learn

		df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
		df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')

- Créer la trame de données pour la modélisation

		cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
		data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])
		
		X = data.iloc[:,1:]
		Y = data.tipped

- Apprentissage et test de fractionnement de 60 à 40

		X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)

- Régression logistique dans le jeu d’apprentissage

		model = LogisticRegression()
		logit_fit = model.fit(X_train, Y_train)
		print ('Coefficients: \n', logit_fit.coef_)
		Y_train_pred = logit_fit.predict(X_train)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_coefficient.PNG)

- Noter le jeu de données de test

		Y_test_pred = logit_fit.predict(X_test)

- Calculer les mesures d’évaluation

		fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
		print fpr_train, tpr_train, thresholds_train
		
		fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred) 
		print fpr_test, tpr_test, thresholds_test
		
		#AUC
		print metrics.auc(fpr_train,tpr_train)
		print metrics.auc(fpr_test,tpr_test)
		
		#Confusion Matrix
		print metrics.confusion_matrix(Y_train,Y_train_pred)
		print metrics.confusion_matrix(Y_test,Y_test_pred)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/py_logit_evaluation.PNG)


 
#### Créer une API de service web et l’exploiter dans Python

Nous voulons rentre le modèle Machine Learning opérationnel une fois créé. Ici, nous utilisons le modèle logistique binaire comme exemple. Assurez-vous que la version scikit-learn se trouvant dans votre machine locale est 0.15.1. Vous n’avez pas à vous soucier de cela si vous utilisez le service Azure ML studio.

- Recherchez vos informations d’identification à partir des paramètres du service Azure ML studio. Dans Azure Machine Learning Studio, cliquez sur **Paramètres** --> **Nom** --> **Jetons d’autorisation**. 

	![](./media/machine-learning-data-science-process-data-lake-walkthough/workspace_id.PNG)


		workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
		auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'

- Créer un service web

		@services.publish(workspaceid, auth_token) 
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(int) #0, or 1
		def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
		    return logit_fit.predict(inputArray)

- Obtenir des informations d’identification du service web

		url = predictNYCTAXI.service.url
		api_key =  predictNYCTAXI.service.api_key
		
		print url
		print api_key

		@services.service(url, api_key)
		@services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
		@services.returns(float)
		def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
		    pass

- Appeler une API de service web Vous devez attendre 5 à 10 secondes après l’étape précédente.

		NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)

       ![](./media/machine-learning-data-science-process-data-lake-walkthough/call_API.PNG)

### Option 2 : Créer et déployer des modèles directement dans Azure Machine Learning

Vous pouvez utiliser Azure Machine Learning Studio pour lire des données directement à partir d’Azure Data Lake Store, créer un modèle et le déployer. Vous devez créer une table Hive pour qu’Azure Machine Learning lise les données directement à partir d’ Azure Data Lake Store. Pour ce faire, un cluster HDInsight Azure distinct doit être approvisionné, sur lequel une table Hive pointant vers le Azure Data Lake Store, est créée. Les sous-sections suivantes montrent comment procéder.

#### Créer un cluster HDInsight Linux
Créez un cluster HDInsight (Linux) à partir du [portail Azure](http://ms.portal.azure.com). Pour plus d’informations, consultez la section Créer un cluster HDInsight ayant accès au Azure Data Lake Store dans [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![6](./media/machine-learning-data-science-process-data-lake-walkthough/create_HDI_cluster.PNG)

#### Créer une table Hive dans HDInsight

Nous allons maintenant créer des tables Hive dans un cluster HDInsight en utilisant les données stockées dans Azure Data Lake Store lors de l’étape précédente. Les tables Hive seront utilisées dans Azure Machine Learning Studio à l’étape suivante. Accédez au cluster HDInsight créé au début de la procédure pas à pas. Cliquez sur **Paramètres** --> **Propriétés** --> **Identité AAD de cluster** --> **ADLS Access** (Accès ADLS), assurez-vous que votre compte Azure Data Lake Store est ajouté dans la liste avec des droits de lecture, d’écriture et d’exécution.

 ![20](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_cluster_add_ADLS.PNG)


Puis cliquez sur **Tableau de bord** en regard du bouton Paramètres et une fenêtre contextuelle s’affiche. Cliquez sur **Affichage Hive** dans l’angle supérieur droit de la page pour afficher l’**éditeur de requête**.

 ![21](./media/machine-learning-data-science-process-data-lake-walkthough/HDI_dashboard.PNG)

 ![22](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_Editor_v2.PNG)


Collez les scripts Hive suivants pour créer une table. L’emplacement de la source de données se trouve dans la référence Azure Data Lake Store sous la forme suivante : **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

	CREATE EXTERNAL TABLE nyc_stratified_sample
	(
	    medallion string,
	    hack_license string,
	    vendor_id string,
	    rate_code string,
	    store_and_fwd_flag string,
	    pickup_datetime string,
	    dropoff_datetime string,
	    passenger_count string,
	    trip_time_in_secs string,
	    trip_distance string,
	    pickup_longitude string,
	    pickup_latitude string,
	    dropoff_longitude string,
	    dropoff_latitude string,
	  payment_type string,
	  fare_amount string,
	  surcharge string,
	  mta_tax string,
	  tolls_amount string,
	  total_amount string,
	  tip_amount string,
	  tipped string,
	  tip_class string,
	  rownum string
	  )
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	LOCATION 'adl://cdsp.azuredatalakestore.net:443/nyctaxi_weig/demo_ex_9_stratified_1_1000_copy.csv';

Lorsque l’exécution de la requête est terminée, les résultats s’affichent comme suit :

 ![23](./media/machine-learning-data-science-process-data-lake-walkthough/Hive_Query_results.PNG)



#### Créer et déployer des modèles dans Azure Machine Studio

Nous pouvons à présent passer aux phases de création et de déploiement de modèles dans Azure Machine Learning. Les données sont prêtes à être utilisées dans ces problèmes de prédiction avec nos données échantillonnées : classification binaire (pourboire ou non), classification multiclasse (tip\_class) et régression (tip\_amount). Ici nous montrons comment créer et déployer un modèle de classification binaire dans Azure Machine Learning Studio.

1. Insérez les données dans Azure ML à l’aide du module **Lecteur**, disponible dans la section **Entrée et sortie des données**. Pour plus d’informations, consultez la page de référence du module [Lecteur](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/).
2. Sélectionnez **Hive Query** comme **Source de données** dans le panneau **Propriétés**.
3. Collez le script Hive suivant dans l’éditeur de **requête de base de données Hive**.

    	select * from nyc_stratified_sample;

4. Entrez l’URI du cluster HDInsight (il se trouve dans le portail Azure), les informations d’identification Hadoop, l’emplacement des données de sortie et le nom du compte Azure Storage/la clé/le nom du conteneur.

 ![24](./media/machine-learning-data-science-process-data-lake-walkthough/reader_module_v3.PNG)

La figure ci-dessous montre un exemple d’expérience de classification binaire qui lit les données à partir de la table Hive.

 ![25](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp.PNG)

Une fois l’expérience créée, cliquez sur **Configurer le service web** --> **Service web prédictif**

 ![26](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy.PNG)

Exécutez l’expérience de notation créée automatiquement. Une fois terminée, cliquez sur **Déployer le service web**.

 ![27](./media/machine-learning-data-science-process-data-lake-walkthough/AML_exp_deploy_web.PNG)

Le tableau de bord du service web s’affiche brièvement :

 ![28](./media/machine-learning-data-science-process-data-lake-walkthough/AML_web_api.PNG)


## Résumé

Voici un résumé des actions effectuées dans cette procédure pas à pas :

- création d’un environnement de science des données pour créer des solutions de bout en bout évolutives dans Azure Data Lake ;
- acheminement d’un jeu de données public volumineux à travers les étapes du processus de science des données à l’aide d’U-SQL et de Python, de l’acquisition des données en passant par l’apprentissage du modèle jusqu’au déploiement du modèle en tant que service web.

<!---HONumber=AcomDC_0525_2016-->