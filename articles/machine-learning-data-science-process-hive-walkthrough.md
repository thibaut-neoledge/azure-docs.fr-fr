<properties 
	pageTitle="Processus de science des données Azure en action : utilisation de clusters Hadoop HDInsight | Azure" 
	description="Processus complet de science des données Azure mettant en œuvre un cluster Hadoop HDInsight pour créer et déployer un modèle utilisant un jeu de données disponible publiquement." 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

                
# Processus de science des données Azure en action : utilisation de clusters Hadoop HDInsight

Dans ce didacticiel, vous allez suivre le processus de science des données Azure mettant en œuvre un cluster Hadoop Azure HDInsight pour créer et déployer un modèle utilisant le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) disponible publiquement. 


## <a name="dataset"></a>Description du jeu de données NYC Taxi Trips

Pesant environ 20 Go au format compressé (ou 48 Go au format non compressé), le jeu de données NYC Taxi Trip contient des fichiers CSV (valeurs séparées par des virgules) concernant plus de 173 millions de trajets et le prix réglé pour chacun d'entre eux. Chaque enregistrement comporte l'emplacement et l'heure de départ et d'arrivée du trajet, le numéro de licence anonymisé (du conducteur) et le numéro du médaillon (identifiant unique du taxi). Les données couvrent tous les trajets de 2013 et sont fournies dans les deux jeux de données suivants pour chaque mois :

1. Les fichiers CSV de 'trip_data' contiennent les détails des trajets, comme le nombre de passagers, les lieux de départ et d'arrivée, la durée et la longueur du trajet. Voici quelques exemples d'enregistrements :

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Les fichiers CSV de 'trip_fare' contiennent des informations sur le prix payé pour chaque trajet, comme le type de paiement, le montant, la surcharge et les taxes, les pourboires et péages, ainsi que le montant total réglé. Voici quelques exemples d'enregistrements :

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La clé de jointure de trip_data et trip_fare se compose des champs medallion, hack_licence et pickup_datetime.

Il y a 12 fichiers 'trip_data' et 12 fichiers 'trip_fare'. Dans les noms de fichier, les numéros 1 à 12 représentent les mois où les trajets ont été effectués. 

## <a name="mltasks"></a>Exemples de tâches de prévision
Le fait de connaître le type de prévisions que vous souhaitez obtenir de l'analyse des données permet de clarifier les tâches à inclure dans votre processus.
Voici trois exemples de problèmes de prévisions que nous allons traiter dans ce guide et dont la formulation s'appuie sur le champ *tip_amount* :

1. **Classification binaire** : pour prévoir si un pourboire a été payé pour un trajet, c'est-à-dire qu'une valeur de *tip_amount* supérieure à 0 $ est un exemple positif alors qu'une valeur de *tip_amount* de 0 $ est un exemple négatif.

2. **Classification multiclasse** : pour prévoir la fourchette des montants de pourboire réglés pour le trajet. Nous divisons *tip_amount* en cinq compartiments ou classes :
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. **Tâche de régression** : pour prévoir le montant du pourboire payé pour un trajet.  


## <a name="setup"></a>Créer l'environnement de science des données Azure

Comme vous pouvez le voir d'après le guide [Planifier votre environnement](machine-learning-data-science-plan-your-environment.md), plusieurs approches sont possibles pour exploiter le jeu de données " NYC Taxi Trips " dans Azure :

- Exploiter les données dans des blobs Azure puis modéliser dans Azure Machine Learning
- Charger les données dans une base de données SQL Server puis modéliser dans Azure Machine Learning
- Charger les données dans des tables Hive HDInsight puis modéliser dans Azure Machine Learning

Compte tenu de la taille du jeu de données, de l'emplacement source des données et de l'environnement cible Azure sélectionné, cette situation est similaire au [Scénario n°7 : jeu de données volumineux dans des fichiers locaux, base de données cible Hive dans des clusters Hadoop Azure HDInsight](machine-learning-data-science-plan-sample-scenarios.md#largedbtohive).

Pour créer l'environnement de science des données Azure correspondant à cette approche qui utilise des clusters Hadoop Azure HDInsight et, plus précisément, des tables et requêtes Hive pour stocker et traiter les données, procédez comme suit :

1. [Créez un compte de stockage](storage-whatis-account.md).

2. [Créez un espace de travail Azure ML](machine-learning-create-workspace.md).

3. [Approvisionnez une machine virtuelle **Windows** de science des données](machine-learning-data-science-setup-virtual-machine.md).

	> [AZURE.NOTE] Les exemples de script seront téléchargés sur votre machine virtuelle de science des données pendant la configuration de celle-ci. Une fois le script de post-installation de la machine virtuelle exécutée, les exemples se trouveront dans la bibliothèque Documents de votre machine virtuelle, située dans *C:\Users<user_name>\Documents\Data Science Scripts*  Ces dossiers exemples sont appelés **Sample Scripts** ci-après. Les exemples de requêtes Hive sont stockés dans des fichiers HQL situés dans le dossier **Sample Scripts**. Notez que l'élément *<user_name>* mentionné dans le chemin d'accès à ce dossier est le nom de connexion Windows de votre machine virtuelle, pas votre nom d'utilisateur Azure.

4. [Personnalisez les clusters Hadoop Azure HDInsight pour la science des données](machine-learning-data-science-customize-hadoop-cluster.md). Cette étape crée un cluster Hadoop Azure HDInsight avec Anaconda Python 2.7 64 bits installé sur tous les nœuds. Une fois le cluster Hadoop personnalisé créé, activez l'accès à distance au nœud principal du cluster Hadoop dans le portail Azure en suivant la procédure décrite dans cette rubrique de personnalisation.


## <a name="getdata"></a>Obtenir les données auprès d'une source publique

Pour récupérer le jeu de données [NYC Taxi Trips](http://www.andresmh.com/nyctaxitrips/) depuis son emplacement de stockage public et le copier sur votre nouvelle machine virtuelle, vous pouvez utiliser l'une des méthodes décrites dans [Déplacer des données vers et depuis un stockage Azure Blob](machine-learning-data-science-move-azure-blob.md).

Pour copier les données à l'aide d'AzCopy :

1. Connectez-vous à votre machine virtuelle.

2. Créez un répertoire sur le disque de données de la machine virtuelle (Remarque : n'utilisez pas le disque temporaire fourni avec la machine virtuelle en tant que disque de données).

3. Dans une fenêtre d'invite de commandes, exécutez les commandes AzCopy suivantes en remplaçant <path_to_data_folder> par le chemin d'accès à votre dossier de données créé à l'étape (2) :

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxitrip /Dest:<path_to_data_folder> /S

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://getgoing.blob.core.windows.net/nyctaxifare /Dest:<path_to_data_folder> /S

	Une fois AzCopy exécuté, le dossier de données doit contenir 24 fichiers CSV compressés (12 pour trip_data et 12 pour trip_fare).

	>[AZURE.NOTE] *Source : https://getgoing.blob.core.windows.net/nyctaxitrip* et *Source:https://getgoing.blob.core.windows.net/nyctaxifare*. Spécifiez deux conteneurs Azure publics utilisés pour partager les données du jeu " NYC Taxi Trips " avec les utilisateurs. L'accès en lecture à ces deux conteneurs Azure publics ne nécessite aucune clé d'accès. 

## <a name="upload"></a>Charger les données dans le conteneur par défaut du cluster Hadoop Azure HDInsight

Dans une fenêtre Powershell Windows ou d'invite de commandes sur votre machine virtuelle, exécutez la commande AzCopy suivante en remplaçant les paramètres suivants par les valeurs que vous avez spécifiées lors de la création du cluster Hadoop. Par conséquent, remplacez :

* ***&#60;path_to_data_folder>*** par le dossier de données créé dans la section précédente ; 
* ***&#60;storage account name of Hadoop cluster>*** par le compte de stockage utilisé par votre cluster ;
* ***&#60;default container of Hadoop cluster>*** par le conteneur par défaut utilisé par votre cluster ;
* ***&#60;storage account key>*** par la clé du compte de stockage utilisé par votre cluster.

Notez que si le répertoire ***nyctaxitripraw*** ou ***nyctaxifareraw*** référencé dans cette commande est absent, il est créé dans le conteneur. 

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
		
		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_data_folder> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

## <a name="#hive-db-tables"></a>Créer la base de données Hive et les tables partitionnées par mois

Pour accéder aux requêtes Hive utilisées dans ce didacticiel et les exécuter, connectez-vous au nœud principal du cluster Hadoop, ouvrez la ligne de commande Hadoop sur le bureau du nœud principal et indiquez le répertoire Hive en saisissant la commande :

    cd %hive_home%\bin

Si vous avez besoin d'aide sur ces procédures ou les alternatives, consultez l'article [Envoyer des requêtes Hive directement depuis la ligne de commande Hadoop](machine-learning-data-science-process-hive-tables.md#submit). 

Si vous avez créé une machine virtuelle Azure selon les instructions de l'article [Créer l'environnement de science des données Azure](#setup), les exemples de requêtes Hive doivent avoir été téléchargés sur votre machine virtuelle dans le dossier **Sample Scripts**. Sinon, les utilisateurs peuvent récupérer les fichiers HQL depuis le site [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). 

Pour envoyer les requêtes Hive dans des fichiers HQL, vous devez transférer ces fichiers HQL dans un répertoire local du nœud principal du cluster Hadoop. Dans les exemples suivants, nous supposons que les fichiers HQL ont été transférés dans le répertoire ***C:\temp&#92;*** du nœud principal.

Voici le contenu du fichier ***C:\temp\sample_hive_create_db_and_tables.hql*** qui crée la base de données Hive ***nyctaxidb***, ainsi que les tables ***trip*** et ***fare***.

	create database if not exists nyctaxidb;

	create external table if not exists nyctaxidb.trip
	( 
	    medallion string, 
	    hack_license string,
	    vendor_id string, 
	    rate_code string, 
	    store_and_fwd_flag string, 
	    pickup_datetime string, 
	    dropoff_datetime string, 
	    passenger_count int, 
	    trip_time_in_secs double, 
	    trip_distance double, 
	    pickup_longitude double, 
	    pickup_latitude double, 
	    dropoff_longitude double, 
	    dropoff_latitude double)  
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

	create external table if not exists nyctaxidb.fare 
	( 
	    medallion string, 
	    hack_license string, 
	    vendor_id string, 
	    pickup_datetime string, 
	    payment_type string, 
	    fare_amount double, 
	    surcharge double,
	    mta_tax double,
	    tip_amount double,
	    tolls_amount double,
	    total_amount double)
	PARTITIONED BY (month int) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
	STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');

Saisissez la commande suivante dans la ligne de commande Hadoop du nœud principal pour envoyer ces requêtes Hive :
	
	hive -f "C:\temp\sample_hive_create_db_and_tables.hql"


## <a name="#load-data"></a>Charger les données dans les tables par partitions
Exécutez les commandes PowerShell suivantes dans la ligne de commande Hadoop pour charger les données dans les tables Hive partitionnées par mois :

	for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

Voici le contenu du fichier *sample_hive_load_data_by_partitions.hql*.

	LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
	LOAD DATA INPATH 'wasb:///nyctaxifareraw/fare_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

### <a name="#show-db"></a>Afficher les bases de données dans le cluster Hadoop HDInsight

Pour afficher les bases de données créées dans le cluster Hadoop HDInsight à l'intérieur de fenêtre de commande Hadoop, exécutez la commande suivante dans la ligne de commande Hadoop :

	hive -e "show databases;"

### <a name="#show-tables"></a>Afficher les tables de la base de données nyctaxidb 
	
Pour afficher les tables dans la base de données nyctaxidb, exécutez la commande suivante dans la ligne de commande Hadoop :
	
	hive -e "show tables in nyctaxidb;"
   
## <a name="#explore-hive"></a>Exploration des données et ingénierie des fonctionnalités dans Hive
Les tâches d'exploration des données et d'ingénierie des fonctionnalités pour les données chargées dans les tables Hive peuvent être exécutées à l'aide de requêtes Hive. Voici des exemples de ces tâches que nous vous décrivons dans cette section :

- Affichez les 10 premiers enregistrements des deux tables.
- Explorez les distributions des données de quelques champs dans différentes périodes.
- Analysez la qualité des données des champs de longitude et de latitude.
- Générez des libellés de classification binaire et multiclasse basés sur le champ **tip_amount**.
- Générez des fonctionnalités en calculant les distances des trajets directs.
- Créez une jointure entre les deux tables et extrayez un échantillon aléatoire qui sera utilisé pour créer des modèles.

Pour anticiper, après avoir exécuté l'exploration des données et l'ingénierie des fonctionnalités dans Hive, vous êtes prêt à passer à l'étape Azure Machine Learning. Vous pouvez enregistrer la requête Hive finale pour extraire et échantillonner les données, puis pour copier-coller la requête directement dans un module Reader dans Azure Machine Learning. Vous êtes alors prêt à créer un modèle pour ces données.

### Exploration : Afficher les 10 premiers enregistrements de la table trip

Pour saisir la nature du schéma de données et l'aspect des données, les utilisateurs peuvent extraire les 10 premiers enregistrements de chaque table. Exécutez les deux requêtes suivantes séparément dans la ligne de commande Hadoop pour analyser les enregistrements.

Pour obtenir les 10 premiers enregistrements de *table _trip_* :

	hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
    
Pour obtenir les 10 premiers enregistrements de *table _fare_* :
	
	hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

### Exploration : afficher le nombre d'enregistrements dans chacune des 12 partitions

Exécutez la commande suivante dans la ligne de commande Hadoop pour afficher le nombre d'enregistrements dans chacune des 12 partitions mensuelles.
	
	hive -e "select month, count(*) from nyctaxidb.trip group by month;"

### Exploration : distribution des trajets selon le champ medallion

Cet exemple identifie le médaillon (numéro du taxi) avec plus de 100 trajets pendant une période donnée. La requête a accès aux tables partitionnées, car elle est conditionnée par la variable de partition **month**. 

	hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql"

Voici le contenu du fichier *sample_hive_trip_count_by_medallion.hql*.

	SELECT medallion, COUNT(*) as med_count
	FROM nyctaxidb.fare
	WHERE month<=3
	GROUP BY medallion
	HAVING med_count > 100 
	ORDER BY med_count desc;

### Exploration : distribution des trajets selon les champs medallion et hack_license

Voici un exemple de regroupement par plusieurs colonnes de la table, en l'occurrence par les colonnes medallion et hack_license. Exécutez la commande suivante dans la ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql"

Voici le contenu du fichier *sample_hive_trip_count_by_medallion_license.hql*.
	
    SELECT medallion, hack_license, COUNT(*) as trip_count
	FROM nyctaxidb.fare
	WHERE month=1
	GROUP BY medallion, hack_license
	HAVING trip_count > 100
	ORDER BY trip_count desc;

### Évaluation de la qualité des données : rechercher les longitude et latitude incorrectes dans les enregistrements

Cet exemple montre comment vérifier si les champs longitude et latitude contiennent une valeur incorrecte (degrés compris entre -90 et 90) ou des coordonnées (0, 0).

Exécutez la commande suivante dans la console de ligne de commande Hadoop :

	hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

L'argument *-S* inclus dans la commande supprime l'affichage de l'état des travaux Map/Reduce Hive. Son utilité réside dans le fait qu'il rend l'affichage de la sortie de la requête Hive plus lisible. 

Voici le contenu du fichier *sample_hive_quality_assessment.hql*.

    	SELECT COUNT(*) FROM nyctaxidb.trip
    	WHERE month=1
    	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'));

### Exploration : fréquences des trajets ayant donné lieu ou pas à des pourboires

Cet exemple détermine le nombre de trajets qui ont fait l'objet d'un pourboire et ceux qui n'ont pas donné lieu à un pourboire pendant la période donnée (ou dans le jeu de données complet si la période couvre l'année complète). Cette distribution reflète la distribution de libellés binaires à utiliser ultérieurement pour la modélisation de classification binaire.

Exécutez la commande suivante dans la console de ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_tipped_frequencies.hql"

Voici le contenu du fichier *sample_hive_tipped_frequencies.hql*.

    SELECT tipped, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

### Exploration : fréquences des montants des pourboires

Cet exemple calcule la distribution des montants des pourboires pendant une période donnée (ou dans l'ensemble du jeu de données si la période couvre l'année complète). Il s'agit de la distribution des classes de libellés qui seront utilisées ultérieurement pour la modélisation de la classification multiclasse.

Exécutez la commande suivante dans la console de ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

Voici le contenu du fichier *sample_hive_tip_range_frequencies.hql*.

	SELECT tip_class, COUNT(*) AS tip_freq 
    FROM 
    (
        SELECT if(tip_amount=0, 0, 
            if(tip_amount>0 and tip_amount<=5, 1, 
            if(tip_amount>5 and tip_amount<=10, 2, 
            if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tip_class;

### Exploration : Calculer la distance directe et la comparer avec la distance du trajet

Cet exemple calcule la distance des trajets directs (en miles). L'exemple limite les résultats aux seules coordonnées valides, grâce à la requête d'évaluation de la qualité des données présentée précédemment.

Exécutez la commande suivante dans la console de ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_trip_direct_distance.hql"

Voici le contenu du fichier *sample_hive_trip_direct_distance.hql*.

    set R=3959;
    set pi=radians(180);
	
	insert overwrite directory 'wasb:///queryoutputdir'
    select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
        ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
        *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
        *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
        /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
        +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
        pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
    from nyctaxidb.trip 
    where month=1 
        and pickup_longitude between -90 and -30
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and -30
        and dropoff_latitude between 30 and 90;

Une fois la requête exécutée, les résultats sont consignés dans le blob Azure ***queryoutputdir/000000_0*** situé dans le conteneur par défaut du cluster Hadoop. Si vous utilisez Azure Storage Explorer, vous devez pouvoir consulter ce blob dans le conteneur par défaut du cluster Hadoop, comme indiqué dans la figure suivante.

![Create workspace][2]

Notez que vous pouvez appliquer le filtre (encadré en rouge) pour ne récupérer que le blob dont le nom contient les lettres spécifiées. 

Une fois la requête exécutée, vous pouvez utiliser le SDK Azure pour lire la sortie du blob Azure dans une table de données Pandas pour effectuer d'autres explorations et exécuter d'autres processus. Pour savoir comment lire des blobs Azure dans des tables de données Pandas, consultez l'article [Traiter les données d'un blob Azure dans votre environnement de science des données](machine-learning-data-science-process-data-blob.md). 
	
### Préparer les données pour la création de modèles

La requête fournie dans cette section crée une jointure entre les tables **nyctaxidb.trip** et **nyctaxidb.fare**, génère le libellé de classification binaire **tipped** et le libellé de classification multiclasse **tip_class**. Cette requête peut être copiée puis collée directement dans le module Reader d'[Azure Machine Learning Studio](https://studio.azureml.net) pour intégrer les données à partir de **Hive Query** dans Azure. Cette requête exclut également les enregistrements présentant des coordonnées de latitude ou de longitude incorrectes.

Elle applique directement les FDU Hive intégrées pour générer plusieurs fonctionnalités à partir des enregistrements Hive, dont l'heure, la semaine de l'année et le jour de la semaine (1 représentant lundi et 7, dimanche) du champ _pickup_datetime_. La liste complète des FDU Hive intégrées est disponible dans le document [FDU LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

De plus, cette requête sous-échantillonne les données pour que les résultats tiennent dans Azure Machine Learning Studio. Seul 1 % est importé dans Studio.

Envoyez la requête en exécutant la commande suivante depuis la console de ligne de commande Hadoop :

	hive -f "C:\temp\sample_hive_prepare_for_aml.hql"

Voici le contenu du fichier *sample_hive_prepare_for_aml.hql*.
	
    select 
        t.medallion, 
        t.hack_license,
        t.vendor_id,
        t.rate_code,
        t.store_and_fwd_flag,
        t.pickup_datetime,
        t.dropoff_datetime,
        hour(t.pickup_datetime) as pickup_hour,
        weekofyear(t.pickup_datetime) as pickup_week,
        from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
        t.passenger_count,
        t.trip_time_in_secs,
        t.trip_distance,
        t.pickup_longitude,
        t.pickup_latitude,
        t.dropoff_longitude,
        t.dropoff_latitude,
        f.payment_type, 
        f.fare_amount, 
        f.surcharge, 
        f.mta_tax, 
        f.tip_amount, 
        f.tolls_amount, 
        f.total_amount,
        if(tip_amount>0,1,0) as tipped,
        if(tip_amount=0,0,
            if(tip_amount>0 and tip_amount<=5,1,
            if(tip_amount>5 and tip_amount<=10,2,
            if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
    from
    (
        select medallion, 
            hack_license,
            vendor_id,
            rate_code,
            store_and_fwd_flag,
            pickup_datetime,
            dropoff_datetime,
            passenger_count,
            trip_time_in_secs,
            trip_distance,
            pickup_longitude,
            pickup_latitude,
            dropoff_longitude,
            dropoff_latitude,
            rand() as sample_key 
        from nyctaxidb.trip
        where pickup_latitude between 30 and 60
            and pickup_longitude between -90 and -60
            and dropoff_latitude between 30 and 60
            and dropoff_longitude between -90 and -60
    )t
    join
    (
        select 
            medallion, 
            hack_license, 
            vendor_id, 
            pickup_datetime, 
            payment_type, 
            fare_amount, 
            surcharge, 
            mta_tax, 
            tip_amount, 
            tolls_amount, 
            total_amount
        from nyctaxidb.fare 
    )f
    on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
    where t.sample_key<=0.01

## <a name="mlmodel"></a>Créer des modèles dans Azure Machine Learning

Nous sommes maintenant prêts à créer et déployer le modèle dans [Azure Machine Learning](https://studio.azureml.net). Les données sont maintenant exploitables pour répondre aux problèmes de prévision identifiés précédemment :

1. **Classification binaire** : pour prédire si un trajet a fait ou non l'objet d'un pourboire.

2. **Classification multiclasse** : pour prédire la fourchette des pourboires réglés pour le trajet, en utilisant les classes précédemment définies. 

3. **Tâche de régression** : pour prévoir le montant du pourboire payé pour un trajet.  

Pour commencer l'exercice de modélisation, connectez-vous à votre espace de travail Azure Machine Learning. Si aucun espace de travail Azure Machine Learning n'a été créé, consultez l'article [Créer un espace de travail Azure ML](machine-learning-create-workspace.md).

1. Pour une prise en main d'Azure Machine Learning, consultez l'article [Présentation d'Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md).

2. Connectez-vous à [Azure Machine Learning Studio](https://studio.azureml.net).

3. La page d'accueil de Studio fournit quantité d'informations, de vidéos, de didacticiels, de liens de référence aux modules et d'autres ressources. Pour plus d'informations sur Azure Machine Learning, consultez le [Centre de documentation d'Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/).

L'exercice suivant a une visée pédagogique évidente :

1. Créez une expérience **+NEW**.
2. Intégrez les données Azure ML.
3. Au besoin, prétraitez, transformez et manipulez les données.
4. Au besoin, générez des fonctionnalités.
5. Divisez les données en des jeux de données de formation, de validation ou de test (ou créez des jeux de données séparés).
6. Sélectionnez un ou plusieurs algorithmes d'apprentissage automatique, selon le problème d'apprentissage à résoudre. Par exemple, classification binaire, classification multiclasse, régression.
7. Formez un ou plusieurs modèles à l'aide du jeu de données de formation.
8. Notez le jeu de données de validation à l'aide du ou des modèles formés.
9. Évaluez le ou les modèles pour calculer les métriques appropriées au problème d'apprentissage.
10. Affinez le ou les modèles et sélectionnez le meilleur à déployer.

Dans cet exercice, nous avons déjà exploré et exploité les données dans Hive (étapes 1 à 4), puis déterminé la taille de l'échantillon à intégrer dans Azure ML. Pour créer un ou plusieurs modèles de prévision :

1. Intégrez les données dans Azure ML à l'aide du module **Reader** disponible dans la section **Entrée et sortie de données**. Pour plus d'informations, consultez la page de référence du module [Reader](http://msdn.microsoft.com/library/dn784775).

	![Create workspace][15]

2. Sélectionnez **Hive Query** comme **Source de données** dans le panneau **Propriétés**.

3. Saisissez les informations du cluster Hadoop HDInsight, comme suit. Le **nom du compte de stockage Azure** doit être le compte de stockage utilisé pour créer le cluster Hadoop HDInsight et le **nom du conteneur Azure** doit être le conteneur par défaut du cluster Hadoop. 

	![Create workspace][11]

4. Dans le champ d'édition de la **requête de base de données Hive**, collez la requête qui extrait les champs nécessaires de la base de données (notamment les champs calculés tels que les libellés) et sous-échantillonnez les données pour obtenir la taille d'échantillon souhaitée.

La figure ci-dessous montre un exemple d'expérience de classification binaire qui lit les données directement dans Hive Query. Il est possible de créer des expériences similaires pour les problèmes de classification multiclasse et de régression.

![Create workspace][12]

> [AZURE.IMPORTANT] Dans les exemples de modélisation de l'extraction des données et d'échantillonnage de requête fournis dans les précédentes sections, **tous les libellés des trois exercices de modélisation sont inclus dans la requête**. Une étape importante (et obligatoire) de chacun des exercices de modélisation consiste à **exclure** les libellés superflus des deux autres problèmes et les autres **fuites de données cibles**. Par exemple, si vous utilisez la classification binaire, utilisez le libellé **tipped** et excluez les champs **tip_class**, **tip_amount** et **total_amount**. Les derniers sont des fuites de données cibles car ils impliquent le pourboire payé.

> Dans cette expérience, le premier module **Metadata Editor** ajoute des noms de colonne dans les données affichées par le module Reader. Ce module est nécessaire, car le module Reader qui lit les données dans Hive Query ne crée aucun nom de colonne pour les données de sortie. 

> Pour exclure les colonnes superflues et/ou les fuites de données cibles, vous pouvez utiliser le module **Project Columns** ou l'éditeur **Metadata Editor**. Pour plus d'informations, consultez les pages de références [Project Columns](http://msdn.microsoft.com/library/dn784740) et [Metadata Editor](http://msdn.microsoft.com/library/dn784761).

## <a name="mldeploy"></a>Déployer des modèles dans Azure Machine Learning

Lorsque votre modèle est prêt, vous pouvez le déployer en tant que service Web directement depuis l'expérience. Pour plus d'informations sur la publication de services Web Azure ML, consultez l'article [Opérations sur le service d'API Azure Machine Learning](machine-learning-overview-of-azure-ml-process.md).

Pour déployer un nouveau service Web, vous devez effectuer les opérations suivantes :

1. Créez une expérience de notation.
2. Publiez le service Web.

Pour créer une expérience de notation, à partir d'une expérience de formation **terminée**, cliquez sur **CRÉER UNE EXPÉRIENCE DE NOTATION** dans la barre d'actions inférieure.

![Azure Scoring][13]

Azure Machine Learning va tenter de créer une expérience de notation basée sur les composants de l'expérience de notation. En particulier il va :

1. Enregistrer le modèle formé et supprimer les modules de formation du modèle.
2. Identifier un **port d'entrée** logique pour représenter le schéma de données attendu.
3. Identifier un **port de sortie** logique pour représenter le schéma de sortie du service Web attendu.

Une fois l'expérience de notation créée, vérifiez-la et effectuez les ajustements nécessaires. Un ajustement typique consiste à remplacer le jeu de données d'entrée et/ou la requête par un autre qui exclut les champs de libellé, car ceux-ci ne sont pas disponibles lorsque le service est appelé. Il est également recommandé de réduire la taille du jeu de données d'entrée et/ou de la requête à quelques enregistrements, ce qui suffit pour indiquer le schéma d'entrée. Pour le port de sortie, il est fréquent d'exclure tous les champs d'entrée et de n'inclure que les **libellés notés** et les **probabilités notées** dans la sortie à l'aide du module **Project Columns**.

La figure ci-dessous montre un exemple d'expérience de notation. Lorsque la publication est prête, cliquez sur le bouton **PUBLIER LE SERVICE WEB** dans la barre d'actions inférieure.

![Create workspace][14]

En résumé, dans ce didacticiel, vous avez créé un environnement de science des données Azure qui fonctionne avec un jeu de données public volumineux. Depuis l'acquisition des données jusqu'aux tâches d'exploration et d'ingénierie des fonctionnalités dans le processus de science des données, pour modéliser la formation et la publication d'un service Web Azure Machine Learning.

## Informations sur la licence

Ce didacticiel et ses scripts associés sont partagés par Microsoft sous la licence MIT. Pour en savoir plus, consultez le fichier LICENSE.txt dans le répertoire du code exemple sur GitHub.

## Références

*	[Page de téléchargement du jeu de données NYC Taxi Trips d'Andrés Monroy](http://www.andresmh.com/nyctaxitrips/)  
*	[FOILing NYC's Taxi Trip Data de Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)

[2]: ./media/machine-learning-data-science-process-hive-walkthrough/output-hive-results-3.png
[11]: ./media/machine-learning-data-science-process-hive-walkthrough/hive-reader-properties.png
[12]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-training.png
[13]: ./media/machine-learning-data-science-process-hive-walkthrough/create-scoring-experiment.png
[14]: ./media/machine-learning-data-science-process-hive-walkthrough/binary-classification-scoring.png
[15]: ./media/machine-learning-data-science-process-hive-walkthrough/amlreader.png

<!--HONumber=49-->