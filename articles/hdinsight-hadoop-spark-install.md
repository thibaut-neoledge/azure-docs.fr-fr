<properties 
	pageTitle="Utiliser une action de script dans HDInsight pour installer Spark sur un cluster Hadoop| Azure" 
	description="Découvrez comment personnaliser le cluster HDInsight pour installer Spark. Vous allez employer une option de configuration d'action de script pour utiliser un script afin d'installer Spark" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Installation et utilisation de Spark 1.0 sur des clusters HDInsight Hadoop

Vous pouvez installer Spark sur n'importe quel type de cluster dans Hadoop sur HDInsight à l'aide de la personnalisation de cluster **Action de script**. Une action de script vous permet d'exécuter des scripts pour personnaliser un cluster, uniquement au moment de sa création. Pour plus d'informations, consultez la page [Personnalisation d'un cluster HDInsight à l'aide d'une action de script][hdinsight-cluster-customize].

Dans cette rubrique, vous allez apprendre à installer Spark 1.0 à l'aide d'une action de script. Vous verrez ensuite comment exécuter une requête Spark sur des clusters HDInsight.


## Dans cet article

- [Qu'est-ce que Spark ?](#whatis)
- [Comment installer Spark](#install)
- [Comment utiliser Spark dans HDInsight](#usespark)
- [Installation de Spark sur des clusters HDInsight Hadoop à l'aide de PowerShell](#usingPS)
- [Installer Spark sur des clusters Hadoop HDInsight à l'aide du SDK .NET](#usingSDK) 


## <a name="whatis"></a>Qu'est-ce que Spark ?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de " big data ". De par ses capacités de calcul en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans les calculs d'apprentissage machine et de graphiques.

Spark permet également d'effectuer des opérations standard de traitement des données sur disque. En évitant les écritures sur disque lors des étapes intermédiaires, Spark améliore l'infrastructure MapReduce traditionnelle. Spark est aussi compatible avec HDFS et WASB, ce qui facilite le traitement des données existantes. 

Cette rubrique contient des instructions de personnalisation d'un cluster HDInsight pour installer Spark.   

## <a name="install"></a>Comment installer Spark

Pour obtenir un exemple de script pour installer Spark sur un cluster HDInsight, téléchargez l'objet blob de stockage Azure en lecture seule à l'adresse [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1). Cette section fournit des instructions sur l'utilisation de l'exemple de script lors de l'approvisionnement du cluster à l'aide du portail de gestion Azure. 

> [AZURE.NOTE] L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1.  Pour plus d'informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Lancez l'approvisionnement d'un cluster à l'aide de l'option **CRÉATION PERSONNALISÉE**, comme il est décrit dans la rubrique [Approvisionnement d'un cluster à l'aide d'options personnalisées](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal). 
2. Sur la page **Actions de script** de l'Assistant, cliquez sur **ajouter l'action de script** pour fournir des informations sur l'action de script, comme illustré ci-dessous :

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Propriété</th><th>Valeur</th></tr>
		<tr><td>Nom</td>
			<td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Spark</b>.</td></tr>
		<tr><td>URI de script</td>
			<td>Indiquez l'URI vers le script qui est appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>Type de nœud</td>
			<td>Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> ou <b>Nœuds de travail</b> uniquement.
		<tr><td>Paramètres</td>
			<td>Indiquez les paramètres, s'ils sont requis par le script. Le script d'installation de Spark ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
	</table>	

	Vous pouvez ajouter plusieurs actions de script afin d'installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

Vous pouvez également utiliser le script pour installer Spark sur HDInsight à l'aide de PowerShell ou du Kit de développement (SDK) HDInsight .NET. Vous trouverez des instructions relatives à ces procédures dans la suite de cette rubrique.

## <a name="usespark"></a>Comment utiliser Spark dans HDInsight
Spark fournit des API en Scala, Python et Java. Vous pouvez aussi utiliser l'interpréteur de commandes Spark interactif pour exécuter des requêtes Spark. Cette section décrit comment utiliser ces deux approches avec Spark :

- [À l'aide de  l'interpréteur de commandes Spark](#sparkshell)
- [À l'aide d'un programme Scala autonome](#standalone)

###<a name="sparkshell"></a>À l'aide de l'interpréteur de commandes Spark
Procédez comme suit pour exécuter des requêtes Spark à partir d'un interpréteur de commandes Spark interactif. Dans cette section, nous allons exécuter une requête Spark sur un exemple de fichier de données (/example/data/gutenberg/davinci.txt) qui est disponible, par défaut, sur des clusters HDInsight.

1. Dans le Portail de gestion Azure, activez le Bureau à distance pour le cluster que vous avez créé et sur lequel vous avez installé R, puis accédez à distance au cluster. Pour obtenir des instructions, consultez <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l'aide de RDP</a>.

2. Dans la session RDP, ouvrez la ligne de commande Hadoop sur le Bureau, puis naviguez jusqu'à l'emplacement d'installation de Spark (par exemple, **C:\apps\dist\spark-1.0.2**).


3. Exécutez la commande suivante pour démarrer l'interpréteur de commandes Spark.

		 .\bin\spark-shell --master yarn

	Une fois la commande terminée, vous devez obtenir une invite Scala.

		 scala>

5. À l'invite Scala, entrez la requête Spark indiquée ci-dessous. Cette requête compte les occurrences de chaque mot dans le fichier davinci.txt disponible à l'emplacement /example/data/gutenberg/ sur le stockage WASB associé au cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. La sortie doit ressembler à ce qui suit :

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. Entrez :q pour quitter l'invite Scala.

		:q

### <a name="standalone"></a>À l'aide d'un programme Scala autonome

Dans cette section, nous allons écrire une application Scala qui compte le nombre de lignes contenant les lettres 'a' and 'b' dans un exemple de fichier de données (/example/data/gutenberg/davinci.txt) disponible, par défaut, sur des clusters HDInsight. Pour écrire et utiliser un programme Scala autonome avec un cluster personnalisé sur lequel Spark est installé, vous devez suivre les étapes suivantes :

- Écrire un programme Scala
- Générer le programme pour obtenir le fichier .jar
- Exécuter la tâche sur le cluster

#### Écrire un programme Scala
Dans cette section, vous allez écrire un programme Scala qui compte le nombre de lignes contenant les lettres 'a' and 'b' dans l'exemple de fichier de données. 

1. Ouvrez un éditeur de texte et collez le code suivant :


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. Enregistrez le fichier sous le nom **SimpleApp.scala**.

#### Générer le programme Scala
Dans cette section, vous allez utiliser l'outil <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (ou sbt) pour générer le programme scala. Cet outil nécessitant Java version 1.6 ou ultérieure, assurez-vous que vous disposez d'une version de Java compatible avant de poursuivre cette section.

1. Installez l'outil sbt disponible à l'adresse suivante : http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Créez un dossier appelé **SimpleScalaApp** puis, dans celui-ci, créez un fichier appelé **simple.sbt**. Il s'agit d'un fichier de configuration qui contient des informations sur la version de Scala, les dépendances de bibliothèque, etc. Collez ce qui suit dans le fichier simple.sbt et enregistrez-le.


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] Veillez à conserver les lignes vides dans le fichier.

	
3. Dans le dossier **SimpleScalaApp**, créez une structure de répertoires **\src\main\scala**, puis collez le programme Scala (**SimpleApp.scala**) créé précédemment dans le dossier \src\main\scala.
4. Ouvrez une invite de commandes, accédez au répertoire SimpleScalaApp, puis entrez la commande suivante :


		sbt package


	Une fois l'application compilée, le fichier **simpleapp_2.10 1.0.jar** est créé dans le répertoire **\target\scala-2.10** dans le dossier racine SimpleScalaApp.


#### Exécuter la tâche sur le cluster
Dans cette section, vous allez vous connecter à distance au cluster sur lequel Spark est installé, puis copier le dossier cible du projet SimpleScalaApp. Vous allez ensuite utiliser la commande **spark-submit** pour envoyer la tâche au cluster.

1. Accédez à distance au cluster sur lequel Spark est installé. Sur l'ordinateur où vous avez écrit et créé le programme SimpleApp.scala, copiez le dossier **SimpleScalaApp\target** et collez-le à l'emplacement de votre choix sur le cluster.
2. Dans la session RDP, sur le Bureau, ouvrez la ligne de commande Hadoop, puis naviguez jusqu'à l'emplacement dans lequel vous avez collé le dossier **target**.
3. Entrez la commande suivante pour exécuter le programme SimpleApp.scala :


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Une fois l'exécution du programme terminée, la sortie s'affiche dans la console.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Installation de Spark sur des clusters HDInsight Hadoop à l'aide de PowerShell

La cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** est utilisée dans cette section pour appeler des scripts avec une action de script afin de personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré PowerShell. Pour plus d'informations sur la configuration d'une station de travail pour exécuter des applets de commande HDInsight Powershell, consultez la page [Installation et configuration d'Azure PowerShell][powershell-install-configure].

Procédez comme suit :

1. Ouvrez une fenêtre Azure PowerShell et déclarez les variables suivantes :

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilisez l'applet de commande **Add-AzureHDInsightScriptAction** pour ajouter l'action de script à la configuration du cluster. L'action de script sera exécutée plus tard lors de la création du cluster. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

	L'applet de commande **Add-AzureHDInsightScriptAction** accepte les paramètres suivants :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Paramètre</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Définition</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Objet de configuration auquel des informations d'action de script sont ajoutées</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom de l'action de script.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Les valeurs valides sont HeadNode (pour une installation sur le nœud principal) ou DataNode (pour une installation sur tous les nœuds de données). Vous pouvez utiliser l'une de ces valeurs ou les deux.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie l'URI du script qui est exécuté.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres requis par le script. L'exemple de script utilisé dans cette rubrique ne requiert aucun paramètre. Par conséquent, ce paramètre n'apparaît pas dans l'extrait de code ci-dessus.
	</td></tr>
	</table>
	
4. Enfin, commencez à approvisionner un cluster personnalisé sur lequel Spark est installé.  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.


## <a name="usingSDK"></a>Installer Spark sur des clusters Hadoop HDInsight à l'aide du SDK .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Cette section fournit des instructions sur l'utilisation d'une action de script à partir du SDK pour approvisionner un cluster sur lequel Spark est installé. Effectuez les étapes suivantes :

- Installer le SDK .NET HDInsight
- Créer un certificat auto-signé
- Créer une application console
- Exécuter l'application


**Pour installer le SDK .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre station de travail et téléchargez-le dans votre abonnement Azure. Pour plus d'informations, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138). 


**Pour créer une application Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu Fichier, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans Nouveau projet, entrez ou sélectionnez les valeurs suivantes :
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Console Application</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
	</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez les commandes suivantes dans la console pour installer les packages.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références à partir du projet Visual Studio en cours.

7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Dans la fonction Main(), copiez et collez le code suivant, puis entrez des valeurs pour les variables :
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Ajoutez le code suivant à la fonction Main() pour utiliser la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) et appeler un script personnalisé pour installer Spark.

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. Enfin, créez le cluster.

		client.CreateCluster(clusterInfo);

11. Enregistrez les modifications apportées à l'application et générez la solution. 

**Pour exécuter l'application**

Ouvrez une console PowerShell, naviguez jusqu'à l'emplacement dans lequel vous avez enregistré le projet Visual Studio, accédez au répertoire \bin\debug du projet, puis exécutez la commande suivante :

	.\CreateSparkCluster <cluster-name>

Entrez un nom de cluster, puis appuyez sur Entrée pour approvisionner un cluster sur lequel Spark est installé.


## Voir aussi##
- [Installation de R sur des clusters HDInsight][ pour obtenir des instructions sur la personnalisation des clusters pour installer et utiliser R sur des clusters HDInsight Hadoop. R est un environnement de calcul statistique open source. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.
- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.
- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d'effectuer de puissantes opérations de recherche sur des données stockées.





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell.


<!--HONumber=42-->
