<properties 
	pageTitle="Créer et charger des données dans des tables Hive à partir d'un stockage Blob | Microsoft Azure" 
	description="Créer des tables Hive et charger des données d’un blob dans des tables Hive" 
	services="machine-learning,storage" 
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
	ms.date="05/29/2015" 
	ms.author="hangzh;bradsev" />

 
#Créer et charger des données dans des tables Hive à partir d’un stockage Azure Blob
 
Ce document présente des requêtes Hive génériques qui créent des tables Hive et chargent des données à partir d’un stockage Azure Blob. Il donne également quelques conseils sur le partitionnement des tables Hive et sur l’utilisation du format ORC (Optimized Row Columnar) pour améliorer les performances des requêtes.


Les requêtes Hive sont disponibles en téléchargement dans le [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql).

Si vous créez une machine virtuelle Azure en suivant les instructions de l'article [Créer une machine virtuelle Azure pour l'analyse avancée](machine-learning-data-science-setup-virtual-machine.md), ce fichier de script doit avoir été téléchargé dans le répertoire *C:\Utilisateurs<nom utilisateur>\Documents\Data Science Scripts* de la machine virtuelle. Pour pouvoir être envoyées, ces requêtes Hive nécessitent simplement que votre schéma de données et la configuration de votre stockage Azure Blob soient déclarés dans les champs appropriés.

Nous partons du principe que les données des tables Hive ont un format tabulaire **non compressé** et qu'elles ont été chargées dans le conteneur par défaut (ou un conteneur supplémentaire) du compte de stockage utilisé par le cluster Hadoop. Si vous souhaitez vous exercer sur les _données NYC Taxi Trip_, vous devez d'abord télécharger les 24 fichiers de [données NYC Taxi Trip](http://www.andresmh.com/nyctaxitrips/) (12 fichiers Trip et 12 fichiers Fare), **décompresser** ces fichiers pour obtenir des fichiers CSV, puis les charger dans le conteneur par défaut (ou le conteneur approprié) du compte Azure Storage utilisé par la procédure dans l'article [Personnaliser les clusters Hadoop Azure HDInsight pour le processus et la technologie d'analyse avancée](machine-learning-data-science-customize-hadoop-cluster.md).

Les requêtes Hive peuvent être envoyées à partir de console de ligne de commande Hadoop, sur le nœud principal du cluster Hadoop. Pour effectuer cette opération, connectez-vous au nœud principal du cluster Hadoop, ouvrez la console de ligne de commande Hadoop, puis soumettez les requêtes Hive à cet emplacement. Pour plus d'informations sur la procédure, consultez l'article [Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus d'analyse avancée](machine-learning-data-science-process-hive-tables.md).

Les utilisateurs peuvent également utiliser Query Console (Hive Editor) en saisissant l’adresse

https://&#60;Hadoop nom de cluster>.azurehdinsight.net/Home/HiveEditor

dans un navigateur Web. Notez que vous devrez entrer les informations d'identification du cluster Hadoop pour vous connecter. Vous pouvez également [envoyer des tâches Hive avec PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell).

## Conditions préalables
Cet article suppose que vous avez :
 
* Créé un compte Azure Storage. Si vous avez besoin d'aide, consultez [Créer un compte Azure Storage](../hdinsight-get-started.md#storage). 
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight. Si vous avez besoin d'aide, consultez [Personnaliser des clusters Hadoop Azure HDInsight pour l'analyse avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* Activé l’accès à distance au cluster, saisi les identifiants appropriés et ouvert la console de ligne de commande Hadoop. Si vous avez besoin d'aide, consultez [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode). 


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

Voici les descriptions des champs que les utilisateurs doivent renseigner et d’autres opérations de configuration :

- **&#60;database name>** : nom de la base de données que les utilisateurs souhaitent créer. S'ils veulent utiliser la base de données par défaut, la requête *create database...* peut être omise. 
- **&#60;table name>** : nom de la table que les utilisateurs veulent créer dans la base de données spécifiée. S'ils veulent utiliser la base de données par défaut, la table peut être désignée directement par *&#60;table name>* sans &#60;database name>.
- **&#60;field separator>** : séparateur qui délimite les champs dans le fichier de données à charger dans la table Hive. 
- &#60;line separator> : séparateur qui délimite les lignes dans le fichier de données. 
- **&#60;storage location>** : emplacement Azure où enregistrer les données des tables Hive. Si les utilisateurs ne spécifient pas *LOCATION &#60;storage location>*, la base de données et les tables sont stockées dans le répertoire *hive/warehouse/* du conteneur par défaut du cluster Hive par défaut. Si un utilisateur souhaite spécifier l’emplacement de stockage, ce dernier doit se trouver dans le conteneur par défaut de la base de données et des tables. Cet emplacement doit être désigné comme emplacement relatif du conteneur par défaut du cluster dans le format de *'wasb:///&#60;directory 1>/'* ou *'wasb:///&#60;directory 1>/&#60;directory 2>/'*, etc. Une fois la requête exécutée, les répertoires relatifs seront créés dans le conteneur par défaut. 
- **TBLPROPERTIES("skip.header.line.count"="1")** : si le fichier de données contient une ligne d’en-tête, les utilisateurs doivent ajouter cette propriété **à la fin** de la requête *create table*. Sinon, cette ligne d’en-tête est chargée comme un enregistrement dans la table. Si le fichier de données ne contient aucune ligne d’en-tête, cette configuration peut être omise dans la requête. 

## <a name="load-data"></a>Charger les données dans des tables Hive
Voici la requête Hive qui charge les données dans une table Hive.

    LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;

- **&#60;path to blob data>** : si le fichier blob à charger dans la table Hive se trouve dans le conteneur par défaut du cluster Hadoop HDInsight, le chemin *&#60;path to blob data>* doit être au format *'wasb:///&#60;directory in this container>/&#60;blob file name>'*. Le fichier blob peut également se trouver dans un autre conteneur du cluster Hadoop HDInsight. Dans ce cas, *&#60;path to blob data>* doit être au format *'wasb://&#60;container name>@&#60;storage account name>.blob.windows.core.net/&#60;blob file name>'*.

	>[AZURE.NOTE]Les données blob à charger dans la table Hive doivent se trouver dans le conteneur par défaut ou un autre conteneur du compte de stockage du cluster Hadoop. Sinon, la requête *LOAD DATA* ne pourra pas s'exécuter car elle n'aura pas accès aux données.


## <a name="partition-orc"></a>Rubriques avancées : Table partitionnée et Stocker des données Hive au format ORC

Si les données sont volumineuses, le partitionnement de la table est avantageux pour les requêtes qui doivent n’en balayer que quelques partitions. Par exemple, il est raisonnable de partitionner les données journalisées d’un site Web par dates.

Outre le partitionnement des tables Hive, il est également judicieux de stocker les données Hive au format ORC (Optimized Row Columnar). Pour plus d'informations sur le format ORC, consultez [L'utilisation de fichiers ORC améliore les performances lorsque Hive lit, écrit et traite des données](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles).

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

2. Créez une table interne avec le même schéma que la table externe à l’étape 1 et le même délimiteur de champ. Puis, stockez-y les données Hive au format ORC.

		CREATE TABLE IF NOT EXISTS <database name>.<ORC table name> 
		(
			field1 string,
			field2 int,
			...
			fieldN date
		) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;

3. Sélectionnez les données de la table externe à l’étape 1 et insérez-les dans la table ORC.

		INSERT OVERWRITE TABLE <database name>.<ORC table name>
            SELECT * FROM <database name>.<external textfile table name>;

	[AZURE.NOTE]Si la table TEXTFILE *&#60;database name>.&#60;external textfile table name>* a des partitions, à l'ÉTAPE 3, la commande `SELECT * FROM <database name>.<external textfile table name>` sélectionne la variable de partition comme champ dans le jeu de données renvoyé. Le fait de l'insérer dans *&#60;database name>.&#60;ORC table name>* échouera car *&#60;database name>.&#60;ORC table name>* ne dispose pas de la variable de partition comme champ dans le schéma de la table. Dans ce cas, les utilisateurs doivent sélectionner explicitement les champs à insérer dans *&#60;database name>.&#60;ORC table name>* comme suit :

		INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
		   SELECT field1, field2, ..., fieldN
		   FROM <database name>.<external textfile table name> 
		   WHERE <partition variable>=<partition value>;

4. Il est possible de supprimer *&#60;external textfile table name>* lors de l'utilisation de la requête suivante après que toutes les données aient été insérées dans *&#60;database name>.&#60;ORC table name>* :

		DROP TABLE IF EXISTS <database name>.<external textfile table name>;

À l’issue de cette procédure, vous devez obtenir une table immédiatement exploitable et contenant des données au format ORC.

<!---HONumber=July15_HO4-->