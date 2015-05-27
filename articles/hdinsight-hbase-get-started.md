<properties
	pageTitle="Configuration et interrogation de tables HBase à l'aide de Hive dans HDInsight | Azure"
	description="Prise en main de HBase avec Hadoop dans HDInsight Apprenez à créer des tables HBase et à les interroger à l'aide de Hive."
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
	ms.date="03/09/2015"
	ms.author="jgao"/>



# Prise en main d’Apache HBase dans HDInsight

Découvrez comment créer et interroger des tables HBase à l’aide de Hive dans HDInsight

HBase est une base de données NoSQL à faible latence qui permet le traitement transactionnel en ligne des données volumineuses (« Big Data »). HBase est proposé en tant que cluster géré intégré à l’environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d’objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix de performances et de coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop. Pour plus d’informations sur HBase et les scénarios avec lesquels il peut être utilisé, consultez la rubrique [Vue d’ensemble de HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE]HBase (version 0.98.0) peut uniquement être utilisé avec des clusters HDInsight 3.1 sur HDInsight (basé sur Apache Hadoop et YARN 2.4.0). Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions]

## Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure** : pour plus d’informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d’abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d’évaluation gratuite][azure-free-trial].
- **Un compte de stockage Azure** : pour plus d’informations, consultez la rubrique [Création d’un compte de stockage][azure-create-storageaccount].
- **Une station de travail** avec Visual Studio 2013 installé : pour obtenir des instructions, consultez [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Approvisionnement d’un cluster HBase

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Approvisionnement d’un cluster HBase en utilisant le portail Azure**


1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **SERVICES DE DONNÉES** > **HDINSIGHT** > **HBASE**.

	Vous pouvez également utiliser l’option CRÉATION PERSONNALISÉE.
3. Renseignez les champs **NOM DU CLUSTER**, **TAILLE DU CLUSTER**, MOT DE PASSE UTILISATEUR DU CLUSTER et **COMPTE DE STOCKAGE**.

	![Choix d'un type de cluster HBase et saisie des informations d'identification de connexion au cluster.][img-hdinsight-hbase-cluster-quick-create]

	Le NOM D’UTILISATEUR HTTP par défaut est admin. Vous pouvez personnaliser le nom à l'aide de l'option CRÉATION PERSONNALISÉE.

	> [AZURE.WARNING]Pour la haute disponibilité des services HBase, vous devez configurer un cluster qui contient au moins **trois** nœuds. Cela garantit que si un nœud tombe en panne, les régions de données HBase sont disponibles sur d'autres nœuds.

4. Cliquez sur l'icône en forme de coche en bas à droite pour créer le cluster HBase.

>[AZURE.NOTE]Après la suppression d'un cluster HBase, vous pouvez créer un autre cluster HBase à l'aide du même objet blob par défaut. Le nouveau cluster utilisera les tables HBase créées dans le cluster d'origine.

## Création d'un exemple de table HBase à partir du shell HBase
Cette section décrit comment utiliser le shell HBase pour créer des tables HBase, ajouter des lignes et répertorier les lignes. Pour accéder au shell HBase, vous devez tout d'abord activer le protocole RDP (Remote Desktop Protocol), puis effectuer une connexion RDP au cluster HBase. Pour obtenir des instructions, consultez [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure][hdinsight-manage-portal].


**Pour utiliser le shell HBase**

1. Dans votre session RDP, cliquez sur le raccourci **Ligne de commande Hadoop** sur le Bureau.
2. Définissez le dossier sur le répertoire d'accueil HBase :

		cd %HBASE_HOME%\bin
3. Ouvrez le shell HBase :

		hbase shell

4. Créez un HBase avec une famille de colonnes et insérez une ligne :

		create 'sampletable', 'cf1'
		put 'sampletable', 'row1', 'cf1:col1', 'value1'
		scan 'sampletable'

	Pour plus d'informations sur le schéma de la table Hbase, consultez [Introduction à la conception de schéma HBase][hbase-schema]. Pour plus de commandes HBase, voir [Guide de référence Apache HBase][hbase-quick-start].
5. Répertorier les tables HBase :

		list

**Vérification du statut du cluster dans l'interface utilisateur web (WebUI) HBase**

HBase est également livré avec un WebUI que vous pouvez utiliser pour faciliter la surveillance de votre cluster. Par exemple, vous pouvez demander des statistiques ou des informations sur les régions. L'interface utilisateur web est disponible sur le cluster HBase, sous l'adresse du nœud zookeeper :


	http://zookeepernode:60010/master-status

Un lien vers le nœud principal HBase actif qui héberge le WebUI est disponible dans un cluster haute disponibilité.

**Chargement en masse d'un exemple de table**

1. À partir du shell HBase, créez une table HBase avec deux familles de colonnes :

		create 'Contacts', 'Personal', 'Office'


3. Créez une liste de contacts qui contient les données suivantes et téléchargez-la vers un dossier appelé /tmp/contacts.txt dans le stockage d’objets blob Azure. Pour obtenir des instructions, consultez la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight][hdinsight-upload-data].

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

2. Dans une ligne de commande Hadoop, définissez le dossier sur le répertoire d'accueil HBase :

		cd %HBASE_HOME%\bin

3. Exécutez ImportTsv. ImportTsv est un outil qui charge les données au format TSV dans HBase. Il a deux utilisations distinctes : le chargement des données au format TSV dans le système de fichiers DFS Hadoop (HDFS) vers HBase et la préparation des fichiers à charger. Pour plus d’informations, voir le [Guide de référence Apache HBase][hbase-reference].

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts /tmp/contacts.txt

4. Chargez la sortie de la commande précédente dans HBase :

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts

## Utilisation de Hive pour interroger une table HBase

Maintenant que vous avez configuré un cluster HBase et créé une table HBase, vous pouvez interroger les données de la table à l'aide de Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

**Ouverture du tableau de bord de cluster**

1. Connectez-vous au [portail Azure][azure-management-portal].
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters s'affiche, avec celui que vous avez créé dans la dernière section.
3. Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive
4. Cliquez sur **CONSOLE DE REQUÊTE** en bas de la page pour ouvrir le tableau de bord du cluster. Une page Web s'ouvre dans un autre onglet du navigateur.
5. Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement. Un nouvel onglet de navigateur s'ouvre.
6. Cliquez sur **Éditeur Hive** en haut de la page. L'éditeur Hive se présente comme suit :

	![Tableau de bord de cluster HDInsight.][img-hdinsight-hbase-hive-editor]





























**Exécution de requêtes Hive**

1. Entrez le script HiveQL suivant dans l'éditeur Hive et cliquez sur **SOUMETTRE** pour créer une table Hive mappant vers la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé dans ce didacticiel en utilisant le shell HBase.

		CREATE EXTERNAL TABLE hbasecontactstable(rowkey STRING, name STRING, homephone STRING, office STRING)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
		WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:HomePhone,Office:Address')
		TBLPROPERTIES ('hbase.table.name' = 'Contacts');

	Attendez que l'**état** soit mis à jour sur **Terminé**.

2. Entrez le script HiveQL suivant dans l'éditeur Hive, puis cliquez sur **SOUMETTRE**. La requête Hive interroge les données dans la table HBase :

     	SELECT count(*) FROM hbasecontactstable;

4. Pour extraire les résultats de la requête Hive, lorsque la tâche est terminée, cliquez sur le lien **Afficher les détails** dans la fenêtre **Session de la tâche**. Il n’y a qu’un fichier de sortie de projet, parce que vous placez un enregistrement dans la table HBase.




**Accès au fichier de sortie**

1. Cliquez sur **Explorateur de fichiers** dans la console de requête.
2. Cliquez sur le compte de stockage Azure utilisé comme système de fichiers par défaut pour le cluster HBase.
3. Cliquez sur le nom du cluster HBase. Le conteneur de comptes de stockage Azure par défaut utilise le nom du cluster.
4. Cliquez sur **Utilisateur**, puis sur **Admin**. (Il s'agit du nom d'utilisateur Hadoop.)
6. Cliquez sur le nom de la tâche dont l'heure de **dernière modification** correspond à l'heure d'exécution de la requête Hive SELECT.
4. Cliquez sur **stdout**. Enregistrez le fichier et ouvrez-le avec le Bloc-notes. Il y a un fichier de sortie.

	![Explorateur de fichiers de l’éditeur Hive HDInsight HBase][img-hdinsight-hbase-file-browser]

## Utilisation de la bibliothèque cliente API REST HBase pour C# .NET pour créer une table HBase et en extraire des données

Vous devez télécharger la bibliothèque cliente API REST de HBase pour .NET à partir de GitHub et générer le projet afin d’utiliser le Kit de développement (SDK) .NET HBase. Vous trouverez des instructions relatives à cette tâche dans la procédure suivante.

1. Créez une application console Windows Desktop Visual Studio en C#.
2. Ouvrez la console du gestionnaire de package NuGet en cliquant sur le menu **OUTILS** > **Gestionnaire de Package NuGet** > **Console de gestionnaire de package**.
3. Exécutez la commande NuGet suivante dans la console :

		Install-Package Microsoft.HBase.Client

5. Ajoutez les instructions **using** suivantes au début du fichier :

		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Remplacez la fonction **Main** par le code suivant :

        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";

            string hbaseTableName = "sampleHbaseTable";

            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);

            // Retrieve the cluster version
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);

            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);

            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);

            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);

            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"

		    //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
		    Scanner scanSettings = new Scanner()
		    {
    		    batch = 10,
    		    startRow = BitConverter.GetBytes(25),
    		    endRow = BitConverter.GetBytes(35)
		    };

		    ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
		    CellSet next = null;
            Console.WriteLine("Scan results");

            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
		    {
    		    foreach (CellSet.Row row in next.rows)
    		    {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
    		    }
		    }

            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }

7. Définissez les trois premières variables de la fonction **Main**.
8. Appuyez sur **F5** pour exécuter l'application.



## Et ensuite ?
Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase, à créer des tables et à afficher les données de ces tables à partir du shell HBase. Vous avez également appris à utiliser Hive pour interroger les données des tables HBase et à utiliser les API REST C# HBase pour créer une table HBase et en extraire les données.

Pour plus d'informations, consultez les rubriques suivantes :

- [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] : HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de grandes quantités de données non structurées et semi-structurées.
- [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet] :avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
- [Configurer la réplication HBase dans HDInsight](hdinsight-hbase-geo-replication.md) : découvrez comment configurer la réplication HBase entre deux centres de données Azure. 
- [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment] : découvrez comment effectuer une [analyse de sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) en temps réel des données volumineuses à l'aide de HBase dans un cluster Hadoop sous HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png

<!--HONumber=54-->