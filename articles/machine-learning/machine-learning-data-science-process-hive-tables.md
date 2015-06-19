<properties 
	pageTitle="Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus de science des données Cloud | Azure" 
	description="Traiter les données des tables Hive avec des requêtes Hive" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/25/2015" 
	ms.author="hangzh;bradsev" /> 

#<a name="heading"></a> Envoyer des requêtes Hive à des clusters Hadoop HDInsight dans le processus de science des données Cloud

Ce document décrit différentes manières d'envoyer des requêtes Hive à des clusters Hadoop gérés par un service HDInsight dans Azure. Plusieurs tâches de manipulation des données sont abordées : exploration des données et génération de fonctionnalités. Des requêtes Hive génériques qui montrent comment explorer des données ou générer des fonctionnalités à l'aide de Hive dans un cluster Hadoop Azure HDInsight. Ces requêtes Hive utilisent les FDU (fonctions définies par l'utilisateur) Hive intégrées. 

Des exemples de requêtes propres aux scénarios mettant en œuvre le jeu de données [NYC Taxi Trip](http://chriswhong.com/open-data/foil_nyc_taxi/) sont également disponibles dans le [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Le schéma de données de ces requêtes est déjà spécifié et elles sont exécutables en l'état. 

La section finale présente les paramètres que les utilisateurs peuvent ajuster pour accélérer le traitement des requêtes Hive.

## Conditions préalables
Cet article suppose que vous avez :
 
* Créé un compte Azure Storage. Si vous avez besoin d'aide, consultez la section [Créer un compte Azure Storage](../hdinsight-get-started.md#storage). 
* Approvisionné un cluster Hadoop personnalisé avec le service HDInsight.  Si vous avez besoin d'aide, consultez l'article [Personnaliser des clusters Hadoop Azure HDInsight pour la science des données](machine-learning-data-science-customize-hadoop-cluster.md).
* Chargé les données dans les tables Hive de clusters Hadoop Azure HDInsight. Si tel n'est pas le cas, commencez par suivre la procédure [Créer et charger des données dans les tables Hive](machine-learning-data-science-move-hive-tables.md).
* Activé l'accès à distance au cluster. Si vous avez besoin d'aide, consultez la section [Accéder au nœud principal du cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


## <a name="submit"></a>Comment envoyer des requêtes Hive
Pour envoyer des requêtes Hive, utilisez au choix : 

* la ligne de commande Hadoop sur le nœud principal du cluster ;
* l'interpréteur IPython Notebook ; 
* l'éditeur Hive ;
* des scripts Azure PowerShell. 

Lors de l'envoi d'une requête Hive, vous pouvez également contrôler la destination de sa sortie : écran, fichier local sur le nœud principal ou blob Azure.

### Avec la console de ligne de commande Hadoop dans le nœud principal du cluster Hadoop

Une requête Hive complexe envoyée directement au nœud principal du cluster Hadoop est traitée plus rapidement qu'avec un éditeur Hive ou des scripts Azure PowerShell. 

Connectez-vous au nœud principal du cluster Hadoop, ouvrez la ligne de commande Hadoop sur le bureau du nœud principal et saisissez la commande :

    cd %hive_home%\bin

Les utilisateurs disposent de trois possibilités pour envoyer des requêtes Hive dans la console de ligne de commande Hadoop :

* directement depuis la ligne de commande Hadoop ;
* à l'aide de fichiers HQL ;
* à l'aide de la console de commande Hive.

####Envoyer des requêtes Hive directement depuis la ligne de commande Hadoop 

Les utilisateurs peuvent exécuter une commande du type 

	hive -e "<your hive query>; 

pour envoyer des requêtes Hive simples directement dans la ligne de commande Hadoop. Voici un exemple, où l'encadré rouge indique la commande qui envoie la requête Hive et l'encadré vert, la sortie de la requête Hive.

![Create workspace][10]

####Envoyer des requêtes Hive dans des fichiers HQL

Lorsque la requête Hive est plus complexe et comporte plusieurs lignes, sa modification dans la ligne de commande Hadoop ou la console de commande Hive n'est pas pratique. L'alternative consiste à utiliser un éditeur de texte dans le nœud principal du cluster Hadoop pour enregistrer la requête Hive dans un fichier HQL situé dans un répertoire local du nœud principal. Ensuite, celle-ci peut être envoyée à l'aide de l'argument `-f` dans la commande `hive`, comme indiqué ci-dessous :
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


####Supprimer l'affichage de l'état d'avancement des requêtes Hive

Par défaut, après l'envoi d'une requête Hive dans la console de ligne de commande Hadoop, l'état d'avancement de la tâche Map/Reduce s'affiche à l'écran. Pour supprimer l'affichage de l'état d'avancement de la tâche Map/Reduce, déclarez l'argument `-S` (" S " en majuscule) dans la ligne de commande, comme indiqué ci-dessous :

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

####Envoyer des requêtes Hive dans la console de commande Hive

Les utilisateurs peuvent également ouvrir la console de commande Hive en exécutant la commande`hive` dans la ligne de commande Hadoop, puis envoyer les requêtes Hive depuis la console de commande Hive. Voici un exemple.  

![Create workspace][11]

Ici, les deux encadrés rouges indiquent les commandes utilisées pour ouvrir la console de commande Hive et envoyer la requête Hive dans cette console. L'encadré vert montre la sortie de la requête Hive.

Les exemples précédents affichent directement les résultats de la requête à l'écran. Les utilisateurs peuvent également consigner la sortie dans un fichier local sur le nœud principal ou dans un blob Azure. Puis, ils peuvent utiliser d'autres outils pour analyser plus finement la sortie de la requête Hive.

####Enregistrer les résultats d'une requête Hive dans un fichier local 

Pour enregistrer les résultats d'une requête Hive dans un répertoire local du nœud principal, les utilisateurs doivent envoyer celle-ci dans la ligne de commande Hadoop, comme indiqué ci-dessous :

	`hive -e "<hive query>" > <local path in the head node>`

Dans l'exemple suivant, la sortie de la requête Hive est consignée dans un fichier `hivequeryoutput.txt` situé dans le répertoire `C:\apps\temp`.

![Create workspace][12]

####Enregistrer les résultats d'une requête Hive dans un blob Azure

Les utilisateurs peuvent également enregistrer les résultats d'une requête Hive dans un blob Azure situé dans le conteneur par défaut du cluster Hadoop. La requête Hive doit être similaire à celle-ci :

	insert overwrite directory wasb:///<directory within the default container> <select clause from ...>

Dans l'exemple suivant, la sortie de la requête Hive est consignée dans le répertoire de blob `queryoutputdir` situé dans le conteneur par défaut du cluster Hadoop. Ici, il suffit d'indiquer le nom du répertoire, sans celui du blob. Le fait de déclarer le nom du répertoire et celui du blob, comme dans `wasb:///queryoutputdir/queryoutput.txt`, déclenche une erreur. 

![Create workspace][13]

Pour visualiser la sortie de la requête Hive dans le stockage de blob, ouvrez le conteneur par défaut du cluster Hadoop avec Azure Storage Explorer (ou un outil équivalent). Vous pouvez utiliser le filtre (encadré rouge) pour retrouver un blob dont le nom comporte les lettres spécifiées.

![Create workspace][14]

### Avec l'éditeur Hive ou des commandes Azure PowerShell

Les utilisateurs peuvent également utiliser Query Console (un éditeur Hive) en saisissant l'URL au format
 
https://<nœud du cluster Hadoop>.azurehdinsight.net/Home/HiveEditor  

dans un navigateur Web. Notez que vous devez saisir les identifiants du cluster Hadoop pour vous y connecter. L'autre solution consiste à [envoyer des requêtes Hive à l'aide de PowerShell](../hdinsight/hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell). 


##<a name="hive-dataexploration"></a>Exploration de données
Voici quelques exemples de scripts Hive qui vous permettent d'explorer les données dans des tables Hive.

1. Obtenir le nombre d'observations par partition
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtenir le nombre d'observations par jour
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. Obtenir les niveaux dans une colonne catégorielle  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtenir le nombre de niveaux combinés de deux colonnes catégorielles 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtenir la distribution des colonnes numériques  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraire les enregistrements à partir de la jointure des deux tables 

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

##<a name="hive-featureengineering"></a>Génération de fonctionnalités

Cette section décrit les méthodes permettant de générer des fonctionnalités à l'aide de requêtes Hive. 

> [AZURE.NOTE] Les exemples de requêtes Hive présentées supposent que les données ont été chargées dans les tables Hive des clusters Hadoop Azure HDInsight. Si tel n'est pas le cas, commencez par suivre la procédure [Créer et charger des données dans les tables Hive](machine-learning-data-science-hive-tables.md).

Lorsque vous avez généré des fonctionnalités supplémentaires, vous pouvez soit les ajouter sous la forme de colonnes à la table existante, soit créer une table avec les fonctionnalités supplémentaires et la clé principale, sur lesquelles vous pouvez créer une jointure à la table d'origine.  

1. [Génération de fonctionnalités basées sur la fréquence](#hive-frequencyfeature)
2. [Risques de variables catégorielles en classification binaire](#hive-riskfeature)
3. [Extraction de fonctionnalités à partir de champs d'horodatage](#hive-datefeatures)
4. [Extraction de fonctionnalités à partir de champs de texte](#hive-textfeatures)
5. [Calcul de la distance entre des coordonnées GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Génération de fonctionnalités basées sur la fréquence

Parfois, il est intéressant de calculer les fréquences des niveaux d'une variable catégorielle ou des niveaux combinés de plusieurs variables catégorielles. Les utilisateurs peuvent utiliser les scripts suivants pour calculer ces fréquences :

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

###<a name="hive-riskfeature"></a>Risques de variables catégorielles en classification binaire

En classification binaire, il faut convertir des variables catégorielles non numériques en fonctionnalités numériques, lorsque les modèles utilisés n'acceptent que les fonctionnalités numériques. Pour ce faire, chaque niveau non numérique est remplacé par un risque numérique. Cette section présente certaines requêtes Hive génériques qui calculent les valeurs de risque (" log odds ") d'une variable catégorielle. 


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

Dans cet exemple, les variables `smooth_param1` et `smooth_param2` sont configurées pour lisser les valeurs de risque obtenues à partir des données. Les risques sont compris entre -Inf et Inf. Un risque supérieur à 0 signifie que la probabilité que la cible soit égale à 1 est supérieure à 0,5. 

Une fois la table de risque calculée, les utilisateurs peuvent attribuer les valeurs de risque à une table en créant une jointure à la table de risque. La requête de jointure Hive est fournie dans la section précédente.

###<a name="hive-datefeature"></a>Extraction de fonctionnalités à partir de champs d'horodatage

Hive est livré avec un ensemble de FDU pour traiter des champs d'horodatage. Dans Hive, le format d'horodatage par défaut est " aaaa-MM-jj 00:00:00 " (comme " 1970-01-01 12:21:32 "). Cette section montre comment extraire le jour du mois et le mois d'un champ d'horodatage, ainsi que des exemples qui convertissent une chaîne d'horodatage d'un format autre que par défaut en une chaîne d'horodatage au format par défaut. 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

Cette requête Hive suppose que `<datetime fied>` est au format par défaut.

Si un champ d'horodatage n'est pas au format par défaut, il faut d'abord le convertir en horodatage Unix puis en chaîne d'horodatage au format par défaut. Une fois l'horodatage au format par défaut, les utilisateurs peuvent appliquer les FDU d'horodatage intégrées pour extraire des fonctionnalités.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

Dans cette requête, si `<datetime field>` a le format `03/26/2015 12:04:39`, le paramètre `'<pattern of the datetime field>'` doit être `'MM/dd/yyyy HH:mm:ss'`. Pour le tester, les utilisateurs peuvent exécuter

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

Dans cette requête, la table `hivesampletable` est installée par défaut sur tous les clusters Hadoop Azure HDInsight lors de leur approvisionnement. 


###<a name="hive-textfeature"></a>Extraction de fonctionnalités à partir de champs de texte

Lorsque la table Hive a un champ de texte qui contient plusieurs mots séparés par un espace, la requête suivante extrait la longueur de la chaîne et le nombre de mots de celle-ci.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calculer la distance entre des coordonnées GPS

La requête fournie dans cette section peut être directement appliquée aux données du jeu " NYC Taxi Trip ". Cette requête montre comment appliquer une fonction mathématique intégrée dans Hive pour générer des fonctionnalités. 

Les champs utilisés dans cette requête sont des coordonnées GPS des emplacements de départ et d'arrivée, intitulés pickup_longitude, pickup_latitude, dropoff_longitude et dropoff_latitude. Les requêtes permettant de calculer la distance directe entre les coordonnées de départ et d'arrivée sont :

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

Les équations mathématiques calculant la distance entre deux coordonnées GPS sont disponibles sur le site [Movable Type Scripts](http://www.movable-type.co.uk/scripts/latlong.html) de Peter Lapisu. Dans ce code Javascript, la fonction toRad() est simplement `lat_or_lon*pi/180`, qui convertit les degrés en radians. Ici, `lat_or_lon` indique la latitude ou la longitude. Comme Hive ne fournit pas la fonction `atan2`, mais fournit la fonction `atan`, la fonction `atan2` est implémentée par la fonction `atan` dans la requête Hive ci-dessus, selon sa définition dans [Wikipedia](http://en.wikipedia.org/wiki/Atan2). 

![Create workspace][1]

La liste complète des FDU Hive intégrées est disponible dans la section **Fonctions intégrées** du [wiki Apache Hive wiki](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions). 

## <a name="tuning"></a> Rubrique avancée : ajuster des paramètres Hive pour accélérer le traitement des requêtes

Les paramètres par défaut du cluster Hive peuvent ne pas convenir aux requêtes Hive et aux données qu'elles traitent. Cette section présente certains paramètres que les utilisateurs peuvent ajuster pour accélérer le traitement des requêtes Hive. Les requêtes d'ajustement des paramètres doivent précéder les requêtes de traitement des données. 

1. **Espace de tas Java** : les requêtes impliquant la jointure de jeux de données volumineux ou le traitement d'enregistrements longs peuvent générer une erreur de type **espace du tas insuffisant**. Ceci peut être corrigé en ajustant la valeur des paramètres `mapreduce.map.java.opts` et `mapreduce.task.io.sort.mb`. Voici un exemple :

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	Ce paramètre alloue 4 Go de mémoire à l'espace de tas Java et accélère le tri en lui attribuant davantage de mémoire. Ajuster ce paramètre est une bonne idée si des erreurs se produisent à cause d'un espace de tas insuffisant.

2. **Taille de bloc DFS** : ce paramètre définit l'unité élémentaire de donnée stockée dans le système de fichiers. Par exemple, si le bloc DFS a une taille de 128 Mo, les données dont la taille est inférieure à ce seuil occupent un bloc, et celles dont la taille est supérieure à ce seuil reçoivent des blocs supplémentaires. Une taille de bloc très petite génère des temps de traitement importants dans Hadoop, car le nœud de noms doit traiter beaucoup plus de requêtes pour trouver le bloc approprié au fichier. Lorsque vos données ont une taille supérieure ou égale à plusieurs gigaoctets, la valeur suivante est recommandée :

		set dfs.block.size=128m;

3. **Optimisation de l'opération de jointure dans Hive** : dans l'architecture Map/Reduce, si les opérations de jointure s'exécutent en général pendant la phase de réduction, il est possible de gagner en efficacité en planifiant les jointures pendant la phase de mappage (également appelée " jointures Map "). Pour obtenir ce comportement dans Hive le cas échéant, saisissez la commande suivante :

		set hive.auto.convert.join=true;

4. **Détermination du nombre de mappeurs dans Hive** : Hadoop permet à l'utilisateur de définir le nombre de réducteurs, mais pas le nombre de mappeurs. Pour contrôler ce nombre dans une certaine mesure, l'astuce consiste à choisir les variables Hadoop *mapred.min.split.size* et *mapred.max.split.size*, car la taille de chaque tâche de mappage est déterminée par :

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	En général, la valeur par défaut de *mapred.min.split.size* est 0, celle de *mapred.max.split.size* est **Long.MAX**, et celle de *dfs.block.size* est 64 Mo. Comme nous pouvons le constater, vu la taille des données, l'ajustement de ces paramètres permet d'optimiser le nombre de mappeurs utilisés. 

5. D'autres **options avancées** optimisant les performances de Hive sont indiquées ci-dessous. Elles permettent de configurer la mémoire allouée aux tâches de mappage et de réduction, et d'ajuster au mieux les performances. Mais gardez à l'esprit que `mapreduce.reduce.memory.mb` ne peut pas être supérieur à la mémoire physique de chaque nœud de travail du cluster Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-process-hive-tables/atan2new.png
[10]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-process-hive-tables/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-process-hive-tables/run-hive-queries-3.png



<!--HONumber=49--> 