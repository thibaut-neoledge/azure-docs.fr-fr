<properties urlDisplayName="Get Started" pageTitle="Configuration et interrogation de tables HBase à l'aide de Hive dans HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. Learn how to create HBase tables and query them using Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Set up HBase clusters and query them using Hive on Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/9/2014" ms.author="bradsev" />



# Configuration et interrogation de clusters HBase à l'aide de Hive sur Hadoop dans HDInsight

Découvrez comment créer et interroger des tables HBase à l'aide de Hive sur Hadoop dans HDInsight 

##Dans cet article

* [Présentation de HBase](#hbaseintro)
* [Configuration requise](#prerequisites)
* [Approvisionnement de clusters HBase à l'aide du portail de gestion Azure](#create-hbase-cluster)
* [Gestion de tables HBase à l'aide du shell HBase](#create-sample-table)
* [Utilisation de Hive pour interroger des tables HBase](#hive-query)
* [Utilisation de la bibliothèque cliente REST Microsoft HBase pour gérer des tables HBase](#hbase-powershell)
* [Voir aussi](#seealso)

##<a name="hbaseintro"></a>Présentation de HBase

HBase est une base de données NoSQL à faible latence qui permet le traitement transactionnel en ligne des données volumineuses (" Big Data "). HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop. Pour plus d'informations sur HBase et les scénarios avec lesquels il peut être utilisé, consultez la rubrique [Vue d'ensemble de HDInsight HBase](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-hbase-overview/).

> [WACOM.NOTE] HBase (version 0.98.0) peut uniquement être utilisé avec des clusters HDInsight 3.1 sur HDInsight (basé sur Apache Hadoop et YARN 2.4.0). Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions]

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure** Pour plus d'informations sur la façon de souscrire un abonnement, consultez la rubrique [Options d'achat][azure-purchase-options], [Offres spéciales membres][azure-member-offers] ou [Version d'évaluation gratuite][azure-free-trial].
- **Un compte de stockage Azure** Pour plus d'informations, consultez la rubrique [Création d'un compte de stockage][azure-create-storageaccount].
- **Un poste de travail sur lequel **Visual Studio 2013 est installé. Pour plus d'informations, consultez la rubrique [Installation de Visual Studio](http://msdn.microsoft.com/fr-fr/library/e2h7fzkw.aspx).
- Téléchargement de la [Bibliothèque cliente REST Microsoft HBase pour .NET](https://github.com/hdinsight/hbase-sdk-for-net). 


##<a name="create-hbase-cluster"></a>Approvisionnement d'un cluster HBase dans le portail Azure

Cette section décrit comment approvisionner un cluster HBase en utilisant le portail Azure.


[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Approvisionnement d'un cluster HDInsight dans le portail Azure** 


1. Connectez-vous au [portail de gestion Azure][azure-management-portal]. 

2. Cliquez sur **HDInsight** à gauche pour afficher le statut des clusters de votre compte, puis sur l'icône **+Nouveau** en bas à gauche. 

	![Creating an HBase cluster in HDInsight in the Azure portal.](http://i.imgur.com/PmGynKZ.jpg)

3. Cliquez sur l'icône HDInsight dans la deuxième colonne en partant de la gauche, puis sur l'option HBase dans la colonne suivante. Spécifiez les valeurs de CLUSTERNAME et CLUSTER SIZE, le nom du compte de stockage et un mot de passe pour le nouveau cluster HBase.
 
	![Choosing and HBase cluster type and entering cluster login credentials.](http://i.imgur.com/ecxbB9K.jpg)

4. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster HBase.


##<a name="create-sample-table"></a>Création d'un exemple de table HBase à partir du shell HBase
Cette section décrit comment activer et utiliser RDP pour accéder au shell HBase et l'utiliser pour créer un exemple de table HBase, ajouter des lignes et lister les lignes dans la table.

Vous devez avoir terminé la procédure indiquée dans la première section et créé un cluster HBase.

**Activation de la connexion RDP au cluster HBase**

1. Pour activer une connexion Bureau à distance avec le cluster HDInsight, sélectionnez le cluster HBase que vous avez créé et cliquez sur l'onglet **CONFIGURATION**. Cliquez sur le bouton **ACTIVER DISTANT** au bas de la page pour activer la connexion RDP avec le cluster.
2. Indiquez les informations d'identification et la date d'expiration dans l'Assistant **CONFIGURER LE BUREAU À DISTANCE** et cliquez sur la coche entourée en bas à droite. (L'opération peut prendre quelques minutes.)
3. Pour vous connecter au cluster HDInsight, cliquez sur le bouton **CONNECTER** au bas de l'onglet  **CONFIGURATION**.

 
**Ouverture du shell HBase**

1. Dans votre session RDP, cliquez sur le raccourci **Invite de commandes Hadoop** situé sur le Bureau.

2. Définissez le dossier sur le répertoire d'accueil HBase :
		
		cd %HBASE_HOME%\bin

3. Ouvrez le shell HBase :

		hbase shell


**Création d'un exemple de table, ajout de données et extraction des données**

1. Création d'un exemple de table :

		create 'sampletable', 'cf1'

2. Ajout d'une ligne à l'exemple de table :

		put 'sampletable', 'row1', 'cf1:col1', 'value1'

3. Liste des lignes dans l'exemple de table :
	
		scan 'sampletable'

##<a name="hive-query"></a>Utilisation de Hive pour interroger une table HBase

Maintenant qu'un cluster HBase est approvisionné et qu'une table est créée, vous pouvez l'interroger avec Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

**Ouverture d'un tableau de bord de cluster**

1. Connectez-vous au [portail de gestion Azure][azure-management-portal]. 
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés s'affiche, avec celui que vous avez créé dans la dernière section.
3. Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive
4. Cliquez sur **GÉRER LE CLUSTER** en bas de page pour ouvrir le tableau de bord du cluster. Une page web s'ouvre dans un autre onglet du navigateur.   
5. Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop.  Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez saisi pendant l'approvisionnement.  Le tableau de bord ressemble à ceci :

	![HDInsight cluster dashboard.](http://i.imgur.com/tMwXlj9.jpg)


**Exécution d'une requête Hive**

1. Pour créer une table Hive avec un mappage vers la table HBase, entrez le script HiveQL ci-dessous dans la fenêtre de la console Hive et cliquez ensuite sur le bouton **SOUMETTRE**. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé ici dans HBase en utilisant le shell HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

 
2. Pour exécuter une requête Hive sur les données de HBase, entrez le script HiveQL ci-dessous dans la fenêtre de la console Hive et cliquez ensuite sur le bouton **SOUMETTRE**.

     	SELECT count(*) FROM hbasesampletable;
 
4. Pour extraire les résultats de la requête Hive, lorsque la tâche est terminée, cliquez sur le lien **Afficher les détails** dans la fenêtre **Session de la tâche**.


Remarque : le lien du shell HBase fait basculer l'onglet vers le **shell HBase**.



**Accès au fichier de sortie**

1. Dans le tableau de bord du cluster, en haut, cliquez sur **Fichiers**.
2. Cliquez sur **Templeton-Job-Status**.
3. Cliquez sur la valeur GUID qui a la dernière heure de modification juste après l'heure de début de la tâche notée précédemment. Notez cette valeur GUID.  Vous en aurez besoin dans la prochaine section.
4. Le fichier **stdout** contient les données dont vous aurez besoin dans la prochaine section. Pour télécharger une copie du fichier de données, au besoin, cliquez sur **stdout**.

	
##<a name="hbase-powershell"></a>Utilisation de la Bibliothèque cliente REST HBase pour les API C# .NET pour créer une table HBase et en extraire des données

La bibliothèque cliente REST HBase pour .NET doit être téléchargée depuis GitHub et le projet doit être conçu pour utiliser le Kit de développement logiciel (SDK) .NET HBase. Vous trouverez des instructions relatives à cette tâche dans la procédure suivante.

1. Téléchargez la [Bibliothèque cliente REST Microsoft HBase pour .NET](https://github.com/hdinsight/hbase-sdk-for-net) le cas échéant.

2. Ouvrez Marlin.sln dans Visual Studio (**Fichier** -> **Ouvrir** -> **Projet/Solution...**) à partir de l'emplacement où vous l'avez téléchargé. Sélectionnez **Afficher** -> **Explorateur de solutions** pour afficher la solution " Marlin " et son projet  Microsoft.HBase.Client. Créez le projet **Marlin** en cliquant sur celui-ci avec le bouton droit dans l'**Explorateur de solutions**, puis en sélectionnant **Générer la solution**. 

4. Créez une application console Visual C#. Extrayez les fichiers Microsoft.HBase.Client.dll et protobuf.dll générés (à partir du répertoire ...\bin\debug\Microsoft.HBase.Client), puis ajoutez-les à votre projet C# : Cliquez avec le bouton droit sur **Références**, sélectionnez **Ajouter des références...**, recherchez les deux assemblys, puis téléchargez-les. [protobuf-net](http://code.google.com/p/protobuf-net/) est une implémentation .NET du sérialiseur binaire Protocol Buffers de Google utilisé pour la communication de données.

5. Ajoutez les instructions using suivantes au début du fichier :
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Créez une instance d'un client HBase en utilisant les informations d'identification du cluster et extrayez la version du cluster :

		// Create a new instance of an HBase client.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		// Retrieve the cluster version
		var version = client.GetVersion();
		Console.WriteLine(version);

7. Pour créer une table HBase, utilisez ce code :

		// Create a new HBase table.
		var testTableSchema = new TableSchema();
		testTableSchema.name = "mytablename";
		testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
		testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
		client.CreateTable(testTableSchema);

8. Utilisez ce code pour insérer des données dans une table :

		// Insert data into a table.
		var tableName = "mytablename";
		var testKey = "content";
		var testValue = "the force is strong in this column";
		var set = new CellSet();
		var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
		set.rows.Add(row);

		var value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
		row.values.Add(value);
		client.StoreCells(tableName, set);

9. Pour extraire une cellule avec sa clé, utilisez ce code : 

		// Retrieve a cell with its key.
		var testKey = "content";
		var tableName = "mytablename";

		var cells = client.GetCells(tableName, testKey);
		// get the first value from the row.
		.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
		// with the previous insert, it should yield: "the force is strong in this column"

10. Analysez les lignes d'une table avec le code suivant :

		//Scan over rows in a table.
		var creds = new ClusterCredentials(new Uri("https://myclustername.azurehdinsight.net"), "myusername", "mypassword");
		var client = new HBaseClient(creds);

		var tableName = "mytablename";

		// assume the table has integer keys and we want data between keys 25 and 35
		var scanSettings = new Scanner()
		{
    		batch = 10,
    		startRow = BitConverter.GetBytes(25),
    		endRow = BitConverter.GetBytes(35)
		};

		var scannerInfo = client.CreateScanner(tableName, scanSettings);
		CellSet next = null;
		while ((next = client.ScannerGetNext(scannerInfo)) != null)
		{
    		foreach (var row in next.rows)
    		{
        		// ... read the rows
    		}
		}



##<a name="summary"></a>Résumé
Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase, à créer des tables et à afficher les données de ces tables à partir du shell HBase. Vous avez également appris à utiliser Hive pour interroger les données des tables HBase et à utiliser les API C# HBase pour créer une table HBase et en extraire les données.

##<a name="next"></a> Étapes suivantes

[Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] :
HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées.

[Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet] :
Avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.

[Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment] :
Apprenez à effectuer une [analyse des sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) des données volumineuses (" Big Data ") en temps réel à l'aide de HBase dans un cluster Hadoop dans HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/

[hdinsight-get-started-30]: ../hdinsight-get-started-30/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-storage]: ../hdinsight-use-blob-storage/



[azure-purchase-options]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/fr-fr/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/fr-fr/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install


[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png







<!--HONumber=35.1-->
