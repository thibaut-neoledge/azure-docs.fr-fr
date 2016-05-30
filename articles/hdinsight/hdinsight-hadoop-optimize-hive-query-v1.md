<properties
   pageTitle="Optimisation de vos requêtes Hive pour une accélération de l’exécution de HDInsight | Microsoft Azure"
   description="Apprenez à optimiser vos requêtes Hive dans HDInsight"
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/28/2015"
   ms.author="rashimg"/>


# Optimisation des requêtes Hive pour Hadoop dans HDInsight

Par défaut, les clusters Hadoop ne sont pas optimisés pour les performances. Cet article présente quelques-unes des méthodes d’optimisation des performances Hive courantes que vous pouvez appliquer à nos requêtes.

> [AZURE.IMPORTANT] Les étapes de ce document utilisent le portail Azure Classic. Microsoft ne recommande pas l’utilisation du portail Classic lors de la création de services. Pour obtenir une explication des avantages du portail Azure, consultez la rubrique [Portail Microsoft Azure](https://azure.microsoft.com/features/azure-portal/).
>
> Ce document inclut également des informations sur l’utilisation d’Azure PowerShell. Les extraits de code fournis sont basés sur des commandes qui utilisent Azure Service Management (ASM) pour travailler avec HDInsight et qui sont __déconseillées__. Ces commandes seront supprimées d’ici au 1er janvier 2017.
>
>Pour obtenir une version de ce document qui utilise le portail Azure, ainsi que des extraits de code PowerShell qui recourent à Azure Resource Manager (ARM), consultez [Optimisation des requêtes Hive pour Hadoop dans HDInsight](hdinsight-hadoop-optimize-hive-query.md).

##Montée en charge des nœuds de travail

L’augmentation du nombre de nœuds de travail d’un cluster permet d’exploiter l’exécution de mappeurs et de raccords de réduction en parallèle. Il existe deux manières d’accroître la montée en charge dans HDInsight :

- Au moment de l’approvisionnement, vous pouvez spécifier le nombre de nœuds de travail à l’aide du portail Azure, d’Azure PowerShell ou d’une interface de ligne de commande multiplateforme. Pour plus d’informations, consultez la rubrique [Configuration de clusters HDInsight](hdinsight-provision-clusters.md). La capture d’écran suivante montre la configuration du nœud de travail sur le portail Azure :

	![scaleout\_1][image-hdi-optimize-hive-scaleout_1]
- Au moment de l’exécution, vous pouvez également monter en charge un cluster sans en recréer un autre. Cette méthode est illustrée ci-dessous. ![scaleout\_1][image-hdi-optimize-hive-scaleout_2]

Pour plus d’informations sur les différentes machines virtuelles prises en charge par HDInsight, consultez la [tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

##Activation de Tez

[Apache Tez](http://hortonworks.com/hadoop/tez/) est un moteur d’exécution représentant une alternative au moteur MapReduce :

![tez\_1][image-hdi-optimize-hive-tez_1]


Tez est plus rapide pour les raisons suivantes :

- Il exécute un graphique orienté acyclique en tant que tâche unique. Dans le moteur MapReduce, le graphique orienté acyclique exprimé requiert que chaque ensemble de mappeurs soit suivi par un ensemble de raccords de réduction. Cela provoque la préparation de plusieurs tâches MapReduce pour chaque requête Hive. Tez n’a pas de telles contraintes et peut traiter un graphique orienté acyclique comme une tâche unique, ce qui réduit la surcharge de démarrage de tâche.
- **Il évite les écritures inutiles**. Lorsque plusieurs tâches sont préparées pour la même requête Hive dans le moteur MapReduce, la sortie de chacune d’elles est écrite vers HDFS pour les données intermédiaires. Comme Tez réduit le nombre de tâches de chaque requête Hive, il est possible d’éviter de telles écritures inutiles.
- **Il réduit les délais de démarrage**. Tez peut réduire davantage le délai de démarrage en réduisant le nombre de mappeurs nécessaires au démarrage tout en améliorant l’optimisation tout au long du processus.
- **Il réutilise les conteneurs**. Dès que possible, Tez peut réutiliser les conteneurs pour réduire la latence provoquée par le nombre de conteneurs au démarrage.
- **Il utilise des techniques d’optimisation continue**. Généralement, l’optimisation est effectuée lors de la compilation. Cependant, des informations supplémentaires sur les entrées sont disponibles pour améliorer l’optimisation durant le démarrage. Tez utilise des techniques d’optimisation continue permettant d’améliorer le plan lors du démarrage.

Pour plus d’informations sur ces concepts, cliquez [ici](http://hortonworks.com/hadoop/tez/)

Vous pouvez activer n’importe quelle requête Hive pour Tez en faisant précéder la requête du paramètre ci-dessous :

	set hive.execution.engine=tez;

Tez doit être activé lors de l’approvisionnement. Voici un exemple de script Azure PowerShell pour l’approvisionnement d’un cluster Hadoop où Tez est activé :


	$clusterName = "[HDInsightClusterName]"
	$location = "[AzureDataCenter]" #i.e. West US
	$dataNodes = 32 # number of worker nodes in the cluster

	$defaultStorageAccountName = "[DefaultStorageAccountName]"
	$defaultStorageContainerName = "[DefaultBlobContainerName]"
	$defaultStorageAccountKey = $defaultStorageAccountKey = Get-AzureStorageKey $defaultStorageAccountName.ToLower() | %{ $_.Primary }

	$hdiUserName = "[HTTPUserName]"
	$hdiPassword = "[HTTPUserPassword]"

	$hdiSecurePassword = ConvertTo-SecureString $hdiPassword -AsPlainText -Force
	$hdiCredential = New-Object System.Management.Automation.PSCredential($hdiUserName, $hdiSecurePassword)

	$hiveConfig = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfig.Configuration = @{ "hive.execution.engine"="tez" }

	New-AzureHDInsightClusterConfig -ClusterSizeInNodes $dataNodes -HeadNodeVMSize Standard_D14 -DataNodeVMSize Standard_D14 |
	Set-AzureHDInsightDefaultStorage -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" -StorageAccountKey $defaultStorageAccountKey -StorageContainerName $defaultStorageContainerName |
	Add-AzureHDInsightConfigValues -Hive $hiveConfig |
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $hdiCredential

## Partitionnement Hive

Les opérations d’E/S constituent le principal goulot d’étranglement des performances pour l’exécution de requêtes Hive. Il est possible d’améliorer les performances en réduisant la quantité de données à lire. Par défaut, les requêtes Hive analysent l’ensemble des tables Hive. Cela est très utile pour les requêtes telles que les analyses de tables, mais cela crée une surcharge inutile pour les requêtes analysant seulement une petite quantité de données (par exemple, les requêtes avec filtrage). Le partitionnement Hive permet aux requêtes Hive d’accéder uniquement à la quantité de données nécessaire dans les tables Hive.

Le partitionnement Hive est implémenté en réorganisant les données brutes en nouveaux répertoires où chaque partition a son propre répertoire, comme défini par l’utilisateur. Le schéma suivant illustre le partitionnement d’une table Hive selon la colonne *Année*. Un nouveau répertoire est créé pour chaque année.

![partitionnement][image-hdi-optimize-hive-partitioning_1]

Considérations relatives au partitionnement :

- **Évitez les sous-partitionnements** : les partitionnements appliqués à des colonnes contenant uniquement quelques valeurs peuvent entraîner des partitions très réduites. Par exemple, un partitionnement de genre crée uniquement deux partitions (masculin et féminin), ce qui réduit la latence de moitié seulement.

- **Évitez les sur-partitionnements** : l’autre extrême, le partitionnement appliqué à une colonne avec une valeur unique (par exemple, userid) va entraîner de nombreuses partitions et communiquer un stress important au cluster namenode, car ce dernier devra gérer de grandes quantités de répertoires.

- **Évitez le décalage de données** : choisissez votre clé de partitionnement avec soin, pour que toutes les partitions soient de taille égale. Par exemple, le partitionnement sur *Région* peut entraîner un nombre d’enregistrements sous Île-de-France 30 fois supérieur à celui sous Franche-Comté, en raison de la différence de population.

Pour créer une table de partition, utilisez la clause *Partitioned By* :

    CREATE TABLE lineitem_part
    	(L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE 	  	 STRING, L_SHIPINSTRUCT STRING, L_SHIPMODE STRING,
    	 L_COMMENT STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    STORED AS TEXTFILE;

Lorsque la table partitionnée est créée, vous pouvez créer un partitionnement statique ou dynamique.

- **Partitionnement statique** signifie que vous avez déjà partagé des données dans des répertoires appropriés et que vous pouvez demander des partitions Hive manuellement en fonction de l’emplacement du répertoire. Cela est illustré dans l’extrait de code ci-dessous.

	    INSERT OVERWRITE TABLE lineitem_part
	    PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
	    SELECT * FROM lineitem 
	    WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’

	    ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
	    LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'

- **Partitionnement dynamique** signifie que vous voulez que Hive crée automatiquement des partitions pour vous. Étant donné que nous avons déjà créé la table de partitionnement à partir de la table intermédiaire, il nous suffit d’insérer des données dans la table partitionnée comme indiqué ci-dessous :

	    SET hive.exec.dynamic.partition = true;
	    SET hive.exec.dynamic.partition.mode = nonstrict;
	    INSERT INTO TABLE lineitem_part
	    PARTITION (L_SHIPDATE)
	    SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
	    	 L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
	     	 L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
	    	 L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as 	 	 L_RETURNFLAG, L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as 	 	 L_SHIPDATE_PS, L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as 	 L_RECEIPTDATE, L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as 	 L_SHIPMODE, L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;

Pour plus d’informations, consultez [Tables partitionnées](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

##Utilisation du format ORCFile

Hive prend en charge différents formats de fichier. Par exemple :

- **Texte** : il s’agit du format de fichier par défaut, qui fonctionne avec la plupart des scénarios
- **Avro** : fonctionne correctement avec les scénarios d’interopérabilité
- **ORC/Parquet** : adapté pour les performances

Le format ORC (Optimized Row Columnar) est un moyen très efficace pour stocker des données Hive. Par rapport aux autres formats, ORC présente les avantages suivants :

- prise en charge des types complexes, notamment les types DateTime, ainsi que les types semi-structurés ;
- jusqu’à 70 % de compression ;
- création d’index toutes les 10 000 lignes, ce qui permet d’ignorer des lignes ;
- baisse significative de l’exécution du démarrage.

Pour activer le format ORC, vous devez commencer par créer une table avec la clause *Stored as ORC* :

    CREATE TABLE lineitem_orc_part
    	(L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
    	 L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
    	 L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
    	 L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
		 L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT 	 STRING)
    PARTITIONED BY(L_SHIPDATE STRING)
    STORED AS ORC;

Ensuite, vous devez insérer des données dans la table ORC à partir de la table de mise en lots. Par exemple :

    INSERT INTO TABLE lineitem_orc
    SELECT L_ORDERKEY as L_ORDERKEY, 
           L_PARTKEY as L_PARTKEY , 
    	   L_SUPPKEY as L_SUPPKEY,
		   L_LINENUMBER as L_LINENUMBER,
     	   L_QUANTITY as L_QUANTITY, 
		   L_EXTENDEDPRICE as L_EXTENDEDPRICE,
    	   L_DISCOUNT as L_DISCOUNT,
		   L_TAX as L_TAX,
           L_RETURNFLAG as L_RETURNFLAG,
		   L_LINESTATUS as L_LINESTATUS,
		   L_SHIPDATE as L_SHIPDATE,
		   L_COMMITDATE as L_COMMITDATE,
		   L_RECEIPTDATE as L_RECEIPTDATE, 
		   L_SHIPINSTRUCT as L_SHIPINSTRUCT,
		   L_SHIPMODE as L_SHIPMODE,
		   L_COMMENT as L_COMMENT
    FROM lineitem;

Vous pouvez en savoir plus sur le format ORC [ici](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

##Vectorisation

La vectorisation permet à Hive de traiter un lot de 1024 lignes simultanément, au lieu d’une ligne à la fois. Cela signifie que les opérations simples sont effectuées plus rapidement, car elles requièrent moins d’exécution de code interne .

Pour activer la vectorisation, faites précéder vos requêtes Hive par le paramètre suivant :

    set hive.vectorized.execution.enabled = true;

Pour plus d’informations, consultez la page [Exécution de requêtes vectorisées](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).


##Autres méthodes d’optimisation

Vous pouvez envisager plusieurs autres méthodes d’optimisation, par exemple :

- **Création de compartiments Hive** : cette technique permet de mettre en cluster ou de segmenter des jeux de données volumineux pour optimiser les performances des requêtes.
- **Optimisation des jointures** : une optimisation de la planification de l’exécution des requêtes Hive pour améliorer l’efficacité des jointures et réduire le besoin d’indicateurs utilisateur. Pour plus d’informations, consultez la page [Optimisation des jointures](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization).
- **Augmentation des raccords de réduction**

##<a id="nextsteps"></a>Étapes suivantes
Dans cet article, vous avez appris plusieurs méthodes d’optimisation courantes des requêtes. Pour en savoir plus, consultez les articles suivants :

- [Utilisation d’Apache Hive dans HDInsight](hdinsight-use-hive.md)
- [Analyse des données sur les retards de vol avec Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analyse des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md)
- [Analyse des données de capteur à l’aide de la console de requête Hive sur Hadoop dans HDInsight](hdinsight-hive-analyze-sensor-data.md)
- [Utilisation de Hive avec HDInsight pour analyser les journaux de site web](hdinsight-hive-analyze-website-log.md)


[image-hdi-optimize-hive-scaleout_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/scaleout_1.png
[image-hdi-optimize-hive-scaleout_2]: ./media/hdinsight-hadoop-optimize-hive-query-v1/scaleout_2.png
[image-hdi-optimize-hive-tez_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/tez_1.png
[image-hdi-optimize-hive-partitioning_1]: ./media/hdinsight-hadoop-optimize-hive-query-v1/partitioning_1.png

<!---HONumber=AcomDC_0518_2016-->