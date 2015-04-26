<properties 
	pageTitle="Configuration et interrogation de tables HBase à l'aide de Hive dans HDInsight | Azure" 
	description="Prise en main de HBase avec Hadoop dans HDInsight Apprenez à créer des tables HBase et à les interroger à l'aide de Hive." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="1/7/2015" 
	ms.author="bradsev"/>



# Configuration et interrogation de clusters HBase à l'aide de Hive sur Hadoop dans HDInsight

Découvrez comment créer et interroger des tables HBase à l'aide de Hive sur Hadoop dans HDInsight 

##Dans cet article

* [Présentation de HBase](#hbaseintro)
* [Conditions préalables](#prerequisites)
* [Approvisionnement de clusters HBase à l'aide du portail de gestion Azure](#create-hbase-cluster)
* [Gestion de tables HBase à l'aide du shell HBase](#create-sample-table)
* [Utilisation de Hive pour interroger des tables HBase](#hive-query)
* [Utilisation de la bibliothèque cliente REST Microsoft HBase pour gérer des tables HBase](#hbase-powershell)
* [Voir aussi](#seealso)

##<a name="hbaseintro"></a>Présentation de HBase

HBase est une base de données NoSQL à faible latence qui permet le traitement transactionnel en ligne des données volumineuses (" Big Data "). HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop. Pour plus d'informations sur HBase et les scénarios avec lesquels il peut être utilisé, consultez la rubrique [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview].

> [AZURE.NOTE] HBase (version 0.98.0) peut uniquement être utilisé avec des clusters HDInsight 3.1 sur HDInsight (basé sur Apache Hadoop et YARN 2.4.0). Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][hdinsight-versions].

##<a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure** Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].
- **Un compte de stockage Azure** Pour plus d'informations, consultez la rubrique [Création d'un compte de stockage][azure-create-storageaccount].
- **Un poste de travail sur lequel **Visual Studio 2013 est installé. Pour plus d'informations, consultez la rubrique [Installation de Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="create-hbase-cluster"></a>Approvisionnement d'un cluster HBase dans le portail Azure

Cette section décrit comment approvisionner un cluster HBase à l'aide du portail de gestion Azure.


[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Approvisionnement d'un cluster HDInsight dans le portail de gestion Azure** 


1. Connectez-vous au [Portail de gestion Azure][azure-management-portal]. 
2. Cliquez sur **NOUVEAU** dans le coin inférieur gauche, puis sur **SERVICES DE DONNÉES**, **HDINSIGHT**, **HBASE**. 
3. Renseignez les champs **NOM DU CLUSTER**, **TAILLE DU CLUSTER**, MOT DE PASSE UTILISATEUR DU CLUSTER et **COMPTE DE STOCKAGE**.
 
	![Choosing and HBase cluster type and entering cluster login credentials.][img-hdinsight-hbase-cluster-quick-create]

4. Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster HBase.


##<a name="create-sample-table"></a>Création d'un exemple de table HBase à partir du shell HBase
Cette section décrit comment activer et utiliser RDP pour accéder au shell HBase et l'utiliser pour créer un exemple de table HBase, ajouter des lignes et lister les lignes dans la table.

Vous devez avoir terminé la procédure indiquée dans la première section et créé un cluster HBase.

**Activation de la connexion RDP au cluster HBase**

1. Dans le portail de gestion, cliquez sur **HDINSIGHT** dans la partie gauche pour afficher la liste des clusters existants.
2. Cliquez sur le cluster HBase sur lequel vous souhaitez ouvrir le shell HBase.
3. Cliquez sur **CONFIGURATION** dans la partie supérieure.
4. Cliquez sur **ACTIVER DISTANT** dans la partie inférieure.
5. Entrez le nom d'utilisateur et le mot de passe de la connexion RDP.  Le nom d'utilisateur doit être différent de celui du cluster que vous avez utilisé lors de l'approvisionnement de ce dernier. Les données du champ **EXPIRE LE** peuvent comporter jusqu'à 7 jours.
6. Cliquez sur la coche dans le coin inférieur droit pour activer le Bureau à distance.
7. Une fois la connexion RDP activée, cliquez sur **CONNEXION** dans la partie inférieure de l'onglet **CONFIGURATION**, puis suivez les instructions.

 
**Ouverture du shell HBase**

1. Dans votre session RDP, cliquez sur le raccourci **Ligne de commande Hadoop** sur le Bureau.

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

**Vérification de l'état du cluster dans l'interface utilisateur web (WebUi) HBase**
	
HBase s'accompagne également d'une interface utilisateur web qui vous aide à surveiller votre cluster en fournissant, par exemple, des statistiques de demande ou des informations sur les régions. L'interface utilisateur web est disponible sur le cluster HBase, sous l'adresse du nœud zookeeper.


	http://zookeepernode:60010/master-status
	
Dans un cluster HA, vous trouverez un lien vers le nœud principal HBase actif qui héberge l'interface utilisateur web.

**Chargement en masse d'un exemple de table**

1. Créez le fichier samplefile1.txt contenant les données suivantes et téléchargez-le vers le stockage d'objets blob Azure dans /tmp/samplefile1.txt :

		row1	c1	c2
		row2	c1	c2
		row3	c1	c2
		row4	c1	c2
		row5	c1	c2
		row6	c1	c2
		row7	c1	c2
		row8	c1	c2
		row9	c1	c2
		row10    c1	c2

2. Définissez le dossier sur le répertoire d'accueil HBase :
		
		cd %HBASE_HOME%\bin

3. Exécutez ImportTsv :

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,a:b,a:c" -Dimporttsv.bulk.output=/tmpOutput sampletable2 /tmp/samplefile1.txt

4. Chargez la sortie de la commande précédente dans HBase :

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput sampletable2



##<a name="hive-query"></a>Utilisation de Hive pour interroger une table HBase

Maintenant qu'un cluster HBase est approvisionné et qu'une table HBase est créée, vous pouvez l'interroger avec Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

**Ouverture d'un tableau de bord de cluster**

1. Connectez-vous au [Portail de gestion Azure][azure-management-portal]. 
2. Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés s'affiche, avec celui que vous avez créé dans la dernière section.
3. Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive
4. Cliquez sur **CONSOLE DE REQUÊTE** en bas de page pour ouvrir le tableau de bord du cluster. Une page web s'ouvre dans un autre onglet du navigateur.   
5. Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop.  Le nom d'utilisateur par défaut est **admin** et le mot de passe est celui que vous avez entré pendant l'approvisionnement. Un nouvel onglet de navigateur s'ouvre. 
6. Cliquez sur **Éditeur Hive** dans la partie supérieure. L'éditeur Hive se présente comme suit :

	![HDInsight cluster dashboard.][img-hdinsight-hbase-hive-editor]





























**Exécution de requêtes Hive**

1. Entrez le script HiveQL ci-dessous dans l'éditeur Hive et cliquez sur **SOUMETTRE** pour créer un mappage de table Hive vers la table HBase. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé ici dans HBase en utilisant le shell HBase.
 
    	CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
    	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    	WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
    	TBLPROPERTIES ('hbase.table.name' = 'sampletable');

	Attendez que l'**état** soit mis à jour sur **Terminé**.
 
2. Entrez le script HiveQL ci-dessous dans l'éditeur Hive, puis cliquez sur le bouton **SOUMETTRE**. La requête Hive interroge les données dans la table HBase :

     	SELECT count(*) FROM hbasesampletable;
 
4. Pour extraire les résultats de la requête Hive, lorsque la tâche est terminée, cliquez sur le lien **Afficher les détails** dans la fenêtre **Session de la tâche**. La sortie de la tâche doit être 1, car vous placez uniquement un enregistrement dans la table HBase.




**Accès au fichier de sortie**

1. Cliquez sur **Explorateur de fichiers** dans la partie supérieure de la console de requête.
2. Cliquez sur le compte Azure Storage utilisé comme système de fichiers par défaut pour le cluster HBase.
3. Cliquez sur le nom du cluster HBase. Le conteneur de comptes de stockage Azure par défaut utilise le nom du cluster.
4. Cliquez sur **utilisateur**.
5. Cliquez sur **admin**. Il s'agit du nom d'utilisateur Hadoop.
6. Cliquez sur le nom de la tâche dont l'heure **de dernière modification** correspond à l'heure d'exécution de la requête Hive SELECT.
4. Cliquez sur **stdout**. Enregistrez le fichier et ouvrez-le avec le Bloc-notes. La sortie doit être 1.

	![HDInsight HBase Hive Editor File Browser][img-hdinsight-hbase-file-browser]
	
##<a name="hbase-powershell"></a>Utilisation de la Bibliothèque cliente REST HBase pour les API C# .NET pour créer une table HBase et en extraire des données

La bibliothèque cliente REST HBase pour .NET doit être téléchargée depuis GitHub et le projet doit être conçu pour utiliser le Kit de développement logiciel (SDK) .NET HBase. Vous trouverez des instructions relatives à cette tâche dans la procédure suivante.

1. Créez une application console Windows Desktop Visual Studio en C#.
2. Ouvrez la console du gestionnaire de package NuGet en cliquant sur le menu **OUTILS**, sur **Gestionnaire de Package NuGet**, puis sur **Console de gestionnaire de package**.
3. Exécutez la commande NuGet suivante dans la console :

	Install-Package Microsoft.HBase.Client

5. Ajoutez les instructions using suivantes au début du fichier :
		
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

6. Remplacez la fonction Main par le code suivant :

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

7. Définissez les trois premières variables de la fonction Main.
8. Appuyez sur **F5** pour exécuter l'application.



##<a name="next"></a> Étapes suivantes
Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase, à créer des tables et à afficher les données de ces tables à partir du shell HBase. Vous avez également appris à utiliser Hive pour interroger les données des tables HBase et à utiliser les API C# HBase pour créer une table HBase et en extraire les données. 

Pour plus d'informations, consultez les rubriques suivantes :

- [Vue d'ensemble de HDInsight HBase][hdinsight-hbase-overview] :
HBase est une base de données NoSQL open source Apache basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées.
- [Approvisionnement de clusters HBase sur Azure Virtual Network][hdinsight-hbase-provision-vnet] :
Avec l'intégration du réseau virtuel, les clusters HBase peuvent être déployés sur le même réseau virtuel que vos applications pour permettre à celles-ci de communiquer directement avec HBase.
- [Analyse de sentiments Twitter avec HBase dans HDInsight][hbase-twitter-sentiment] :
Apprenez à effectuer une [analyse des sentiments](http://en.wikipedia.org/wiki/Sentiment_analysis) des données volumineuses en temps réel à l'aide de HBase dans un cluster Hadoop dans HDInsight.

[hdinsight-hbase-overview]: ../hdinsight-hbase-overview/
[hdinsight-hbase-provision-vnet]: ../hdinsight-hbase-provision-vnet
[hdinsight-versions]: ../hdinsight-component-versioning/
[hbase-twitter-sentiment]: ../hdinsight-hbase-analyze-twitter-sentiment/
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/ 

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-get-started/hdinsight-hbase-file-browser.png









<!--HONumber=42-->
