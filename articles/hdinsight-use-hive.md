<properties 
	pageTitle="Utilisation de Hadoop Hive dans HDInsight | Azure" 
	description="Apprenez à utiliser Hive avec HDInsight. Vous allez utiliser un fichier journal comme entrée dans une table HDInsight et vous servir de HiveQL pour interroger les données et générer des rapports sur les statistiques de base." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>

# Utilisation de Hive avec Hadoop dans HDInsight

[Apache Hive][apache-hive] permet d'exécuter une tâche MapReduce via un langage de création de scripts semblable à SQL, nommé *HiveQL*. Hive est un système d'entrepôt de données pour Hadoop qui facilite la synthèse de données, les requêtes et l'analyse d'ensembles de données volumineux. Dans cet article, vous allez utiliser HiveQL pour interroger un exemple de fichier de données fourni dans le cadre de l'approvisionnement du cluster HDInsight.


**Conditions préalables :**

- Vous devez avoir approvisionné un **cluster HDInsight**. Pour une description de la procédure à suivre avec le portail Azure, consultez la page [Prise en main de HDInsight][hdinsight-get-started]. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la page [Approvisionnement de clusters HDInsight][hdinsight-provision].

- Vous devez avoir installé **Azure PowerShell** sur votre station de travail. Pour des instructions sur la marche à suivre, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install-configure].

## Dans cet article

* [Hive : cas d'emploi](#usage)
* [Téléchargement de données pour des tables Hive](#uploaddata)
* [Exécution de requêtes Hive avec PowerShell](#runhivequeries)
* [Exécution de requêtes Hive à l'aide de HDInsight Tools pour Visual Studio](#runhivefromvs)
* [Utilisation de Tez pour des performances améliorées](#usetez)
* [Étapes suivantes](#nextsteps)

## <a id="usage"></a>Hive : cas d'emploi

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive projette une structure sur des données non structurées, puis vous laisse interroger ces données. Hive fournit une couche d'abstraction au-dessus de l'infrastructure MapReduce Java, ce qui permet aux utilisateurs d'interroger les données sans connaissance de Java ou MapReduce. HiveQL, le langage de requête de Hive, vous permet d'écrire des requêtes avec des instructions semblables à T-SQL. Les requêtes HiveQL sont compilées pour MapReduce par HDInsight et exécutées sur le cluster. Hive présente d'autres avantages :

- Hive permet aux programmeurs familiers de l'infrastructure MapReduce de raccorder des mappeurs et des réducteurs personnalisés pour effectuer une analyse plus complexe qui n'est peut-être pas prise en charge par les fonctionnalités intégrées du langage HiveQL.
- Hive convient le mieux pour le traitement par lots de grosses quantités de données immuables (comme les journaux Web). Il ne convient pas pour les applications de transactions qui ont besoin de temps de réponse rapides, notamment les systèmes de gestion de base de données.
- Hive est optimisé pour l'évolutivité (il est possible d'ajouter davantage de machines dynamiquement au cluster Hadoop), l'extensibilité (dans l'infrastructure MapReduce et avec d'autres interfaces de programmation) et la tolérance aux pannes. La latence ne fait pas partie des éléments clés considérés dans la conception.

## <a id="uploaddata"></a>Téléchargement de données pour des tables Hive

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut pour les clusters Hadoop. Quelques exemples de fichiers de données sont ajoutés au stockage d'objets blob dans le cadre de l'approvisionnement du cluster. Cet article utilise un exemple de fichier *log4j* distribué avec le cluster HDInsight et stocké dans **\example\data\sample.log**, dans votre conteneur de stockage d'objets blob. Chaque journal à l'intérieur du fichier est constitué d'une ligne de champs qui contient un champ `[LOG LEVEL]` pour indiquer le type et la gravité. Par exemple :

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Dans l'exemple ci-dessus, le niveau de journal est ERROR.

> [AZURE.NOTE] Vous pouvez également générer vos propres fichiers log4j à l'aide de l'utilitaire de journalisation [Apache Log4j][apache-log4j], puis les télécharger vers le conteneur d'objets blob. Consultez la page [Téléchargement de données vers HDInsight][hdinsight-upload-data] pour obtenir des instructions. Pour plus d'informations sur l'utilisation du stockage d'objets blob Azure avec HDInsight, consultez [Utiliser le stockage d'objets blob Azure avec HDInsight][hdinsight-storage].

HDInsight peut accéder aux fichiers stockés dans un stockage d'objets blob à l'aide du préfixe **wasb**. Par exemple, pour accéder au fichier sample.log, vous devez utiliser la syntaxe suivante :

	wasb:///example/data/sample.log

Étant donné que WASB est le stockage par défaut pour HDInsight, vous pouvez également accéder au fichier en utilisant **/example/data/sample.log**.

> [AZURE.NOTE] La syntaxe ci-dessus, **wasb:///**, permet d'accéder à des fichiers stockés dans le conteneur de stockage par défaut de votre cluster HDInsight. Si vous avez indiqué d'autres comptes de stockage lors de l'approvisionnement du cluster et souhaitez accéder aux fichiers qui y sont stockés, vous pouvez accéder aux données en indiquant le nom du conteneur et l'adresse du compte de stockage. Par exemple, **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

## <a id="runhivequeries"></a> Exécution de requêtes Hive avec PowerShell

Les requêtes Hive peuvent être exécutées dans PowerShell avec le cmdlet **Start-AzureHDInsightJob** ou **Invoke-Hive**.

* **Start-AzureHDInsightJob** est un exécuteur de tâches générique, utilisé pour lancer des tâches Hive, Pig et MapReduce sur un cluster HDInsight. **Start-AzureHDInsightJob** est asynchrone et renvoie des résultats avant la fin de la tâche. Les informations relatives à la tâche sont renvoyées et peuvent être utilisées avec des cmdlets, telles que **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** et **Get-AzureHDInsightJobOutput**.  **Get-AzureHDInsightJobOutput** doit être utilisée pour récupérer des informations écrites sur **STDOUT** ou **STDERR** par la tâche.

* **Invoke-Hive** exécute une requête Hive, attend qu'elle soit terminée et récupère la sortie en une seule opération.

1. Ouvrez une fenêtre de console Azure PowerShell. Pour les instructions, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install-configure].
2. Exécutez la commande suivante pour vous connecter à votre abonnement Azure :

		Add-AzureAccount

	Vous êtes invité à entrer les informations d'identification de votre compte Azure.

2. Définissez les variables dans le script suivant et exécutez-le :

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Exécutez le script suivant pour créer une table nommée **log4jLogs** à l'aide de l'exemple de données.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Les instructions HiveQL effectuent les opérations suivantes :

	* **DROP TABLE** : supprime la table et le fichier de données, si la table existe déjà.
	* **CREATE EXTERNAL TABLE** : crée une table 'external' dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent à leur emplacement d'origine
	* **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.
	* **STORED AS TEXTFILE LOCATION** : indique à Hive l'emplacement des données (répertoire example/data) et précise qu'elles sont stockées sous la forme de texte.
	* **SELECT** : sélectionne toutes les lignes dont la colonne **t4** contient la valeur **[ERROR]**. Cette commande doit renvoyer une valeur de **3**, car trois lignes contiennent cette valeur.

	> [AZURE.NOTE] Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, ou par une autre opération MapReduce, mais souhaitez toujours que les requêtes Hive utilisent les données les plus récentes.
	>
	> La suppression d'une table externe ne supprime **pas** les données, mais seulement la définition de la table.


4. Exécutez le script suivant pour créer une définition de tâche Hive à partir de la requête précédente.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Vous pouvez également utiliser le commutateur -File pour spécifier un fichier de script HiveQL sur HDFS.

5. Exécutez le script suivant pour envoyer la tâche Hive :

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Exécutez le script suivant pour attendre que la tâche Hive soit terminée :

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Exécutez le script suivant pour imprimer la sortie standard :

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Voici le résultat :

		[ERROR] 3

	ce qui signifie que le fichier  *sample.log* contenait trois instances de journaux ERROR.

4. Avant d'utiliser **Invoke-Hive**, vous devez définir le cluster à utiliser.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Utilisez le script suivant pour créer une table 'internal' nommée **errorLogs** à l'aide du cmdlet **Invoke-Hive**.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	Ces instructions effectuent les opérations suivantes :

	* **CREATE TABLE IF NOT EXISTS** : crée une table, le cas échéant. Le mot-clé **EXTERNAL** n'étant pas utilisé, il s'agit d'une table 'internal', stockée dans l'entrepôt de données Hive et gérée intégralement par Hive.
	* **STORED AS ORC** : stocke les données au format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
	* **INSERT OVERWRITE ... SELECT** : sélectionne des lignes de la table **log4jLogs** qui contiennent **[ERROR]**, puis insère les données dans la table **errorLogs**.

	> [AZURE.NOTE] Contrairement aux tables **EXTERNAL**, la suppression d'une table interne entraîne également la suppression des données sous-jacentes.

	La sortie se présente comme suit :

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Pour les requêtes HiveQL plus longues, vous pouvez utiliser les fichiers de script Here-Strings ou HiveQL de PowerShell. L'extrait de code suivant montre comment utiliser le cmdlet *Invoke-Hive* pour exécuter un fichier de script HiveQL. Ce dernier doit être téléchargé vers WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Pour plus d'informations sur Here-Strings, consultez la page [Utilisation du fichier de script Here-Strings de PowerShell][powershell-here-strings].

5. Pour vérifier que seules les lignes contenant **[ERROR]** dans la colonne t4 ont été stockées dans la table **errorLogs**, utilisez l'instruction suivante afin de renvoyer toutes les lignes à partir de **errorLogs**.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	Trois lignes de données doivent normalement être renvoyées. Elles contiennent toutes **[ERROR]** dans la colonne t4.


> [AZURE.NOTE] Si besoin est, vous pouvez également importer le résultat de vos requêtes dans Microsoft Excel pour une analyse plus approfondie. Pour des instructions, consultez la rubrique [Connexion d'Excel à Hadoop à l'aide de Power Query][import-to-excel].

## <a id="runhivefromvs"></a>Exécution de requêtes Hive avec Visual Studio
HDInsight Tools for Visual Studio est fourni avec le Kit de développement logiciel (SDK) Azure pour .NET version 2.5 ou ultérieure.  L'utilisation des outils de Visual Studio vous permet de vous connecter à un cluster HDInsight, de créer des tables Hive et d'exécuter des requêtes Hive.  Pour plus d'informations, consultez la rubrique [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1].



## <a id="usetez"></a>Utilisation de Tez pour des performances améliorées

[Apache Tez][apache-tez] est une infrastructure qui permet une exécution à l'échelle beaucoup plus efficace pour les applications qui manipulent de grandes quantités de données telles que Hive. Dans la dernière version de HDInsight, Hive prend en charge l'exécution sur Tez.  Cette fonctionnalité est actuellement désactivée par défaut et doit être activée.  Dans les prochaines versions du cluster, elle sera activée par défaut. Pour pouvoir tirer parti de Tez, vous devez définir la valeur suivante pour une requête Hive :

		set hive.execution.engine=tez;

Vous pouvez le faire individuellement pour chaque requête en plaçant cette instruction au début de la requête.  Il est également possible d'activer cette fonctionnalité par défaut sur un cluster en définissant la valeur de configuration à la création du cluster.  Pour plus de détails, consultez la page  [Approvisionnement de clusters HDInsight][hdinsight-provision]

Les [documents de conception Hive sur Tez][hive-on-tez-wiki] contiennent des informations détaillées sur les options d'implémentation et les configurations de réglage.


## <a id="nextsteps"></a>Étapes suivantes

Bien que Hive facilite l'interrogation des données avec un langage de requête similaire à SQL, d'autres composants disponibles avec HDInsight fournissent des fonctionnalités supplémentaires telles que le transfert et la transformation des données. Pour en savoir plus, consultez les articles suivants :

* [Prise en main de HDInsight Hadoop Tools pour Visual Studio][1]
* [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Utilisation de Pig avec HDInsight](../hdinsight-use-pig/)
* [Analyse des données sur les retards de vol avec HDInsight][hdinsight-analyze-flight-data]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Prise en main d'Azure HDInsight](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/fr-fr/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/fr-fr/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/fr-fr/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=42-->
