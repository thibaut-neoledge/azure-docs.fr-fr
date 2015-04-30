<properties 
	pageTitle="Créer et charger des données dans des tables Hive à partir d'un stockage Azure Blob | Azure" 
	description="Créer des tables Hive et charger des données d'un blob dans des tables Hive" 
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
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" />

 
# Créer et charger des données dans des tables Hive à partir d'un stockage Azure Blob
 
Ce document présente des requêtes Hive génériques qui créent des tables Hive et chargent des données à partir d'un stockage Azure Blob. Il donne également quelques conseils sur le partitionnement des tables Hive et sur l'utilisation du format ORC (Optimized Row Columnar) pour améliorer les performances des requêtes.

Les requêtes Hive sont disponibles en téléchargement dans le [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).

Si vous créez une machine virtuelle Azure en suivant les instructions de l'article [Créer une machine virtuelle Azure pour la science des données](machine-learning-data-science-setup-virtual-machine.md), ce fichier de script doit avoir été téléchargé dans le répertoire *C:\Users\<user name>\Documents\Data Science Scripts* de la machine virtuelle. Pour pouvoir être envoyées, ces requêtes Hive nécessitent simplement que votre schéma de données et la configuration de votre stockage Azure Blob soient déclarés dans les champs appropriés.

Nous partons du principe que les données des tables Hive ont un format tabulaire **non compressé** et qu'elles ont été chargées dans le conteneur par défaut (ou un conteneur supplémentaire) du compte de stockage utilisé par le cluster Hadoop. Si les utilisateurs souhaitent s'exercer sur les données du jeu _NYC Taxi Trips_, ils doivent d'abord [télécharger les 24 fichiers](http://www.andresmh.com/nyctaxitrips/) (12 fichiers Trip et 12 fichiers Fare), **décompresser** ces fichiers pour obtenir des fichiers CSV, puis les charger dans le conteneur par défaut (ou le conteneur approprié) du compte Azure Storage utilisé par la procédure dans l'article [Personnaliser les clusters Hadoop Azure HDInsight pour la science des données](machine-learning-data-science-customize-hadoop-cluster.md). 

Les requêtes Hive peuvent être envoyées à partir de console de ligne de commande Hadoop, sur le nœud principal du cluster Hadoop. Pour ce faire, connectez-vous au nœud principal du cluster Hadoop, ouvrez la console de ligne de commande Hadoop et envoyez les requêtes Hive. Pour plus d'informations sur la procédure, consultez l'article [Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus de science des données Cloud](machine-learning-data-science-process-hive-tables.md).

Les utilisateurs peuvent également utiliser Query Console (Hive Editor) en saisissant l'adresse 

https://&#60;nom du cluster Hadoop>.azurehdinsight.net/Home/HiveEditor

dans un navigateur Web. Notez que vous devrez saisir les identifiants du cluster Hadoop pour vous y connecter. L'autre solution consiste à [envoyer des travaux Hive à l'aide de PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell). 

## Conditions préalables
Cet article suppose que vous avez :
 
* Créé un compte Azure Storage. Si vous avez besoin d'aide, consultez l'article [Créer un compte Azure Storage](hdinsight-get-started.md#storage). 
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight.  Si vous avez besoin d'aide, consultez l'article [Personnaliser des clusters Hadoop Azure HDInsight pour la science des données](machine-learning-data-science-customize-hadoop-cluster.md).
* Activé l'accès à distance au cluster, saisi les identifiants appropriés et ouvert la console de ligne de commande Hadoop. Si vous avez besoin d'aide, consultez l'article [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="create-tables"></a>Créer la base de données et les tables Hive
Voici la requête Hive qui crée une table Hive.

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

- `<database name>` : nom de la base de données que les utilisateurs souhaitent créer. S'ils veulent utiliser la base de données par défaut, la requête `create database...` peut être omise. 
- `<table name>` : nom de la table que les utilisateurs veulent créer dans la base de données spécifiée. S'ils veulent utiliser la base de données par défaut, la table peut être désignée directement par `<table name>` sans `<database name>.`.
- `<field separator>` : séparateur qui délimite les champs dans le fichier de données à charger dans la table Hive. 
- `<line separator>` : séparateur qui délimite les lignes dans le fichier de données. 
- `<storage location>` : emplacement Azure où enregistrer les données des tables Hive. Si les utilisateurs ne spécifient pas `LOCATION '<storage location>'`, la base de données et les tables sont stockées dans le répertoire `hive/warehouse/` du conteneur par défaut du cluster Hive par défaut. Si un utilisateur souhaite spécifier l'emplacement de stockage, ce dernier doit se trouver dans le conteneur par défaut de la base de données et des tables. Cet emplacement doit être spécifié par rapport au conteneur par défaut du cluster, au format `'wasb:///<répertoire 1>/'` ou `'wasb:///<répertoire 1>/<répertoire 2>/'`, etc. Une fois la requête exécutée, les répertoires relatifs sont créés dans le conteneur par défaut. 
- `TBLPROPERTIES("skip.header.line.count"="1")`: Si le fichier de données contient une ligne d'en-tête, les utilisateurs doivent ajouter cette propriété à la **fin** de la requête `create table`. Sinon, cette ligne d'en-tête est chargée comme un enregistrement dans la table. Si le fichier de données ne contient aucune ligne d'en-tête, cette configuration peut être omise dans la requête. 

## <a name="load-data"></a>Charger les données dans des tables Hive
Voici la requête Hive qui charge les données dans une table Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- `<path to blob data>` : si le fichier blob à charger dans la table Hive se trouve dans le conteneur par défaut du cluster Hadoop HDInsight, le chemin `<path to blob data>` doit être au format `'wasb:///<répertoire dans ce conteneur>/<nom du fichier blob>'`. Le fichier blob peut également se trouver dans un autre conteneur du cluster Hadoop HDInsight. Dans ce cas, `<path to blob data>` doit être au format `'wasb://<nom du conteneur>@<nom du compte de stockage>.blob.windows.core.net/<nom du fichier blob>'`.

>[AZURE.NOTE] Les données blob à charger dans la table Hive doivent se trouver dans le conteneur par défaut ou un autre conteneur du compte de stockage du cluster Hadoop. Sinon, la requête `LOAD DATa` ne pourra pas s'exécuter car elle n'aura pas accès aux données. 


## <a name="partition-orc"></a>Rubriques avancées : Table partitionnée et Stocker des données Hive au format ORC

Si les données sont volumineuses, le partitionnement de la table est avantageux pour les requêtes qui doivent n'en balayer que quelques partitions. Par exemple, il est raisonnable de partitionner les données journalisées d'un site Web par dates. 

Outre le partitionnement des tables Hive, il est également judicieux de stocker les données Hive au format ORC (Optimized Row Columnar). Pour plus d'informations sur le format ORC, consultez l'article [L'utilisation de fichiers ORC améliore les performances lorsque Hive lit, écrit et traite des données](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles).

### Table partitionnée
Voici la requête Hive qui crée une table partitionnée et charge les données dans celle-ci.

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

### <a name="orc"></a>Stocker des données Hive au format ORC

Les utilisateurs ne peuvent pas charger directement des données au format ORC depuis le stockage blob dans des tables Hive. Voici les étapes que les utilisateurs doivent suivre pour charger des données au format ORC depuis des blobs Azure dans des tables Hive. 

1. Créez une table externe **STORED AS TEXTFILE** et chargez les données du stockage blob dedans.

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

2. Créez une table interne avec le même schéma que la table externe à l'étape 1 et le même délimiteur de champ. Puis, stockez-y les données Hive au format ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Sélectionnez les données de la table externe à l'étape 1 et insérez-les dans la table ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name> SELECT * FROM <database name>.<external textfile table name>;

[AZURE.NOTE] Si la table TEXTFILE `<database name>.<external textfile table name>` a des partitions, à l'étape 3, `SELECT * FROM <database name>.<external textfile table name>` sélectionne la variable de partition comme champ dans le jeu de données renvoyé. Son insertion dans `<database name>.<ORC table name>` échoue car le schéma de la table `<database name>.<ORC table name>` ne définit pas la variable de partition comme un champ. Dans ce cas, les utilisateurs doivent sélectionner explicitement les champs à insérer dans `<database name>.<ORC table name>` comme suit :

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		      SELECT field1, field2, ..., fieldN
		      FROM <database name>.<external textfile table name> 
		      WHERE <partition variable>=<partition value>;

4. Vous pouvez supprimer l'élément `<external textfile table name>` si vous utilisez la requête suivante, une fois toutes les données insérées dans `<database name>.<ORC table name>` :

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

À l'issue de cette procédure, vous devez obtenir une table immédiatement exploitable et contenant des données au format ORC. 

<!--HONumber=49-->