<properties 
	pageTitle="Créer et charger des données dans des tables Hive à partir d'un stockage Azure Blob | Azure" 
	description="Créer des tables Hive et charger les données de blobs dans des tables Hive" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="hangzh;bradsev" />

 
#Créer et charger des données dans des tables Hive à partir d'un stockage Azure Blob
 
Ce document présente des requêtes Hive génériques qui créent des tables Hive et chargent les données d'un stockage Azure Blob. Ces requêtes Hive sont disponibles dans le référentiel GitHub. [Référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql). 

Si vous créez une machine virtuelle Azure en suivant la procédure de l'article " Créer une machine virtuelle Azure avec IPython Notebook Server ", ce fichier de script a été téléchargé dans le répertoire `C:\Users\<user name>\Documents\Data Science Scripts` de la machine virtuelle. Vous pouvez déclarer votre schéma de données et la configuration de votre stockage Azure Blob dans les champs correspondants de ces requêtes. De plus, ces requêtes Hive doivent être prêtes à envoyer. 

Nous partons du principe que les données des tables Hive sont au format tabulaire **non compressé** et qu'elles sont chargées dans le conteneur par défaut ou un conteneur supplémentaire du compte de stockage utilisé par le cluster Hadoop. Si les utilisateurs souhaitent s'exercer sur les données du jeu _NYC Taxi Trips_, ils doivent d'abord [télécharger les 24 fichiers](http://www.andresmh.com/nyctaxitrips/) (12 fichiers Trip et 12 fichiers Fare), les **décompresser** pour obtenir des fichiers CSV, puis les télécharger dans le conteneur par défaut ou un conteneur supplémentaire du compte de stockage Azure utilisé lors de la [personnalisation du cluster Hadoop Azure HDInsight](machine-learning-data-science-customize-hadoop-cluster.html). 

Les requêtes Hive peuvent être envoyées dans la ligne de commande, sur le nœud principal du cluster Hadoop. Vous devez :

1. [Activer l'accès à distance au nœud principal du cluster Hadoop et vous connecter à ce nœud](machine-learning-data-science-customize-hadoop-cluster.md).
2. [Envoyer les requêtes Hive dans la ligne de commande Hadoop sur le nœud principal](machine-learning-data-science-hive-queries.md).

Les utilisateurs peuvent également utiliser [Query Console (Hive Editor)] en saisissant l'adresse " https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor " dans un navigateur Web (saisie obligatoire des identifiants du cluster Hadoop pour se connecter) ou [envoyer des travaux Hive à l'aide de PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md). 

- [Étape 1 : Créer la base de données et les tables Hive](#create-tables)
- [Étape 2 : Charger les données dans les tables Hive](#load-data)
- [Rubriques avancées : Table partitionnée et Stocker des données Hive au format ORC](#partition-orc)

## <a name="create-tables"></a>Créer la base de données et les tables Hive

    create database if not exists <database name>;
	CREATE EXTERNAL TABLE if not exists <database name>.<table name>
	(
		field1 string, 
		field2 int, 
		field3 float, 
		field4 double, 
		...,
		fieldN string
	) 
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>' 
	STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");

Voici les descriptions des champs que les utilisateurs doivent renseigner et d'autres opérations de configuration :

- `<database name>` : nom de la base de données que les utilisateurs souhaitent créer. S'ils souhaitent simplement utiliser la base de données par défaut, la requête `create database...` peut être omise. 
- `<table name>` : nom de la table que les utilisateurs souhaitent créer dans la base de données spécifiée. S'ils souhaitent utiliser la base de données par défaut, la table peut être désignée directement par `<table name>` sans `<database name>`.
- `<field separator>` : séparateur délimitant les champs dans le fichier de données à charger dans la table Hive. 
- `<line separator>` : séparateur délimitant les lignes dans le fichier de données. 
- `<storage location>` : emplacement Azure où enregistrer les données des tables Hive. Si les utilisateurs ne spécifient pas `LOCATION '<storage location>'`, la base de données et les tables sont stockées par défaut dans le répertoire `hive/warehouse/` du conteneur par défaut du cluster Hive. Si un utilisateur souhaite spécifier l'emplacement de stockage,  ce dernier doit se situer dans le conteneur par défaut de la base de données et des tables. Cet emplacement doit être spécifié par rapport au conteneur par défaut du cluster, au format `'wasb:///<répertoire 1>/'` ou `'wasb:///<répertoire 1>/<répertoire 2>/'`, etc. Une fois la requête exécutée, les répertoires relatifs sont créés dans le conteneur par défaut. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: si le fichier de données contient une ligne d'en-tête, les utilisateurs doivent ajouter cette propriété à la **fin** de la requête `create table`. Sinon, cette ligne est chargée en tant qu'enregistrement dans la table. Si le fichier de données ne contient aucune ligne d'en-tête, cette configuration peut être omise dans la requête. 

## <a name="load-data"></a>Charger les données dans les tables Hive

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>` : si le fichier de blob à charger dans la table Hive se trouve dans le conteneur par défaut du cluster Hadoop HDInsight, `<path to blob data>` doit être au format `'wasb:///<répertoire de ce conteneur>/<nom du fichier blob>'`. Le fichier de blob peut également se trouver dans le conteneur supplémentaire du cluster Hadoop HDInsight. Dans ce cas, `<path to blob data>` doit être au format `'wasb://<nom du conteneur>@<nom du compte de stockage>.blob.windows.core.net/<nom du fichier blob>'`.
>[AZURE.NOTE] Les données de blob à charger dans la table Hive doivent se trouver dans le conteneur par défaut ou un conteneur supplémentaire du compte de stockage du cluster Hadoop. Sinon, la requête `LOAD DATa` ne peut pas s'exécuter car elle n'a pas accès aux données. 


## <a name="partition-orc"></a>Rubriques avancées : Table partitionnée et Stocker des données Hive au format ORC

Si les données sont volumineuses, le partitionnement de la table est avantageux pour les requêtes qui doivent n'en balayer que quelques partitions. Par exemple,  il est raisonnable de partitionner les données journalisées d'un site Web par dates. 

Outre le partitionnement de la table, il est judicieux de stocker les données Hive au format ORC. ORC signifie " Optimized Row Columnar ".  Pour plus d'informations, consultez l'article _[L'utilisation de fichiers ORC améliore les performances lorsque Hive lit, écrit et traite des données](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles)_.

### Table partitionnée

Les requêtes qui créent une table partitionnée et y chargent les données sont les suivantes :

    CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
	(field1 string,
	...
	fieldN string
	)
    PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
		 lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
	LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name> 
		PARTITION (<partitionfieldname>=<partitionfieldvalue>);

Lors de l'interrogation de tables partitionnées, il est recommandé d'ajouter la condition de partition au **début** de la clause `where`, car cela améliore considérablement l'efficacité de la recherche. 

    select 
		field1, field2, ..., fieldN
	from <database name>.<partitioned table name> 
	where <partitionfieldname>=<partitionfieldvalue> and ...;

### <a name="orc"></a>Stocker les données Hive au format ORC

Les utilisateurs ne peuvent pas charger directement les données d'un blob dans des tables Hive au format ORC. Voici les étapes que les utilisateurs doivent suivre pour charger les données de blobs Azure dans des tables Hive stockées au format ORC. 

1. Créez une table externe **STORED AS TEXTFILE** et chargez-y les données du stockage blob.

		CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
		(
			field1 string,
			field2 int,
			...
			fieldN date
		)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' 
			lines terminated by '<line separator>' STORED AS TEXTFILE 
			LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

		LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;

2. Créez une table interne avec le même schéma que la table externe à l'étape 1 et le même délimiteur de champ. Puis, stockez les données Hive au format ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Sélectionnez les données de la table externe et insérez-les dans la table ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] Si la table TEXTFILE `<database name>.<external textfile table name>` a des partitions, à l'étape 3, `SELECT * FROM <database name>.<external textfile table name>` sélectionne la variable de partition comme un champ dans le jeu de données renvoyé. Son insertion dans `<database name>.<ORC table name>` échoue car le schéma de la table `<database name>.<ORC table name>` ne définit pas la variable de partition comme un champ. Dans ce cas, les utilisateurs doivent sélectionner explicitement les champs à insérer dans `<database name>.<ORC table name>` comme suit :

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Vous pouvez supprimer `<external textfile table name>` dans la requête suivante, lorsque toutes les données sont insérées dans `<database name>.<ORC table name>` :

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

Vous disposez maintenant d'une table au format ORC, immédiatement exploitable. 

<!--HONumber=49-->