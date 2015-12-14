<properties
	pageTitle="Utiliser une action de script dans HDInsight pour installer Spark sur un cluster Hadoop| Azure"
	description="Découvrez comment personnaliser un cluster HDInsight avec Spark à l'aide d'une action de script."
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
	ms.date="10/15/2015"
	ms.author="nitinme"/>

# Installation et utilisation de Spark sur des clusters HDInsight Hadoop à l’aide d’une action de script

> [AZURE.IMPORTANT]Cet article est désormais obsolète. HDInsight fournit désormais Spark comme type de cluster de première classe pour les clusters Windows, ce qui signifie que vous pouvez à présent créer directement un cluster Spark sans modifier de cluster Hadoop à l’aide d’une action de script. À l’aide du type de cluster Spark, vous obtenez un cluster HDInsight version 3.2 avec Spark version 1.3.1. Pour installer les différentes versions de Spark, vous pouvez utiliser une action de script. HDInsight fournit un exemple de script d'action de script.

Découvrez comment installer Spark sur HDInsight basé sur Windows à l'aide d'une action de script, et comment exécuter des requêtes Spark sur des clusters HDInsight.


**Articles connexes** - [Installer Spark sur des clusters HDInsight basés sur Linux](hdinsight-hadoop-spark-install-linux.md).

- [Créer des clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md) : informations générales sur la création de clusters HDInsight.

- [Prendre en main Apache Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) : créer un cluster de type Spark sur le système d’exploitation Windows.

- [Personnaliser un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : informations générales sur la personnalisation de clusters HDInsight à l’aide d’actions de script.

- [Développer des scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md).

## Qu'est-ce que Spark ?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. De par ses capacités de calcul en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans les calculs d'apprentissage machine et de graphiques.

Spark permet également d'effectuer des opérations standard de traitement des données sur disque. En évitant les écritures sur disque lors des étapes intermédiaires, Spark améliore l’infrastructure MapReduce traditionnelle. En outre, Spark est compatible avec le système HDFS (Hadoop Distributed File System) et le stockage d’objets blob Azure ; les données existantes peuvent dont être traitées facilement via Spark.

Cette rubrique contient des instructions de personnalisation d'un cluster HDInsight pour installer Spark.

## Installation de Spark à l’aide du portail Azure

Pour obtenir un exemple de script pour installer Spark sur un cluster HDInsight, téléchargez l’objet blob de stockage Azure en lecture seule à l’adresse [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1). Ce script peut installer Spark 1.2.0 ou Spark 1.0.2 selon la version du cluster HDInsight que vous créez.

- Si vous utilisez le script pendant la création d’un cluster **HDInsight 3.2**, il installe **Spark 1.2.0**.
- Si vous utilisez le script pendant la création d’un cluster **HDInsight 3.1**, il installe **Spark 1.0.2**.

Vous pouvez modifier ce script ou créer votre propre script pour installer d’autres versions de Spark.

> [AZURE.NOTE]L’exemple de script fonctionne uniquement avec des clusters HDInsight 3.1 et 3.2. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).

1. Démarrez la création d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE**, comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md#portal). Choisissez la version de cluster en fonction de ce qui suit :

	- Si vous souhaitez installer **Spark 1.2.0**, créez un cluster HDInsight 3.2.
	- Si vous souhaitez installer **Spark 1.0.2**, créez un cluster HDInsight 3.1.


2. Sur la page **Actions de script** de l’Assistant, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Utilisation d’une action de script pour personnaliser un cluster")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Spark</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Le script d'installation de&#160;Spark ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer la création du cluster.

Vous pouvez également utiliser le script pour installer Spark sur HDInsight à l’aide d’Azure PowerShell ou du Kit de développement (SDK) .NET HDInsight. Vous trouverez des instructions relatives à ces procédures dans la suite de cette rubrique.

## Utilisation de Spark dans HDInsight
Spark fournit des API en Scala, Python et Java. Vous pouvez aussi utiliser l'interpréteur de commandes Spark interactif pour exécuter des requêtes Spark. Cette section fournit des instructions sur l’utilisation des différentes approches pour travailler avec Spark :

- [Utilisation de l’interpréteur de commandes Spark pour exécuter des requêtes interactives](#sparkshell)
- [Utilisation de l’interpréteur de commandes Spark pour exécuter des requêtes Spark SQL](#sparksql)
- [Utilisation d’un programme Scala autonome](#standalone)

###<a name="sparkshell"></a>Utilisation de l’interpréteur de commandes Spark pour exécuter des requêtes interactives
Procédez comme suit pour exécuter des requêtes Spark à partir d'un interpréteur de commandes Spark interactif. Dans cette section, nous allons exécuter une requête Spark sur un exemple de fichier de données (/example/data/gutenberg/davinci.txt) qui est disponible sur les clusters HDInsight par défaut.

1. Dans le portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec Spark installé, puis connectez-vous à distance au cluster. Pour la marche à suivre, consultez [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Dans la session RDP, ouvrez la ligne de commande Hadoop (à partir d’un raccourci sur le Bureau), puis naviguez jusqu’à l’emplacement d’installation de Spark (par exemple, **C:\\apps\\dist\\spark-1.2.0**.


3. Exécutez la commande suivante pour démarrer l’interpréteur de commandes Spark :

		 .\bin\spark-shell --master yarn

	Une fois la commande terminée, vous devez obtenir une invite Scala :

		 scala>

5. À l'invite Scala, entrez la requête Spark indiquée ci-dessous. Cette requête compte les occurrences de chaque mot dans le fichier davinci.txt disponible à l’emplacement /example/data/gutenberg/ sur le stockage d’objets blob Azure associé au cluster.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. La sortie doit ressembler à ce qui suit :

	![Sortie de l’exécution de l’interpréteur de commandes interactif Scala dans un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Entrez :q pour quitter l'invite Scala.

		:q

###<a name="sparksql"></a>Utilisation de l’interpréteur de commandes Spark pour exécuter des requêtes Spark SQL

Spark SQL vous permet d’utiliser Spark pour exécuter des requêtes relationnelles exprimées en langage SQL (Structured Query), HiveQL ou Scala. Dans cette section, nous allons examiner l’utilisation de Spark pour exécuter une requête Hive sur un exemple de table Hive. La table Hive utilisée dans cette section (appelée **hivesampletable**) est disponible par défaut quand vous créez un cluster.

>[AZURE.NOTE]L’exemple ci-dessous a été créé par rapport à **Spark 1.2.0**, qui est installé si vous exécutez l’action de script pendant la création du cluster HDInsight 3.2.

1. Dans le portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec Spark installé, puis connectez-vous à distance au cluster. Pour la marche à suivre, consultez [Connexion à des clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Dans la session RDP, ouvrez la ligne de commande Hadoop (à partir d’un raccourci sur le Bureau), puis naviguez jusqu’à l’emplacement d’installation de Spark (par exemple, **C:\\apps\\dist\\spark-1.2.0**.


3. Exécutez la commande suivante pour démarrer l’interpréteur de commandes Spark :

		 .\bin\spark-shell --master yarn

	Une fois la commande terminée, vous devez obtenir une invite Scala :

		 scala>

4. À l’invite Scala, définissez le contexte Hive. Cela est nécessaire pour travailler avec des requêtes Hive en utilisant Spark.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Notez que **sc** est le contexte Spark par défaut qui est défini lorsque vous démarrez l’interpréteur de commandes Spark.

5. Exécutez une requête Hive en utilisant le contexte Hive et imprimez le résultat sur la console. La requête extrait des données sur des appareils d’une marque spécifique et limite le nombre d’enregistrements récupérés à 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Un résultat similaire à ce qui suit s’affiche normalement :

	![Sortie de l’exécution de Spark SQL sur un cluster HDInsight](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Entrez :q pour quitter l'invite Scala.

		:q

### <a name="standalone"></a>Utilisation d’un programme Scala autonome

Dans cette section, nous allons écrire une application Scala qui compte le nombre de lignes contenant les lettres « a » et « b » dans un exemple de fichier de données (/example/data/gutenberg/davinci.txt) disponible, par défaut, sur des clusters HDInsight. Pour écrire et utiliser un programme Scala autonome avec un cluster personnalisé sur lequel Spark est installé, vous devez suivre les étapes suivantes :

- Écrire un programme Scala
- Générer le programme Scala pour obtenir le fichier .jar
- Exécuter la tâche sur le cluster

#### Écrire un programme Scala
Dans cette section, vous allez écrire un programme Scala qui compte le nombre de lignes contenant les lettres « a » et « b » dans un exemple de fichier de données.

1. Ouvrez un éditeur de texte et collez le code suivant :


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf

		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
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
Dans cette section, vous utilisez <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (ou sbt) pour générer le programme Scala. Cet outil nécessitant Java version 1.6 ou ultérieure, assurez-vous que vous disposez d’une version de Java compatible avant de poursuivre cette section.

1. Installez sbt à partir de http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Créez un dossier appelé **SimpleScalaApp**, puis, dans celui-ci, créez un fichier appelé **simple.sbt**. Il s’agit d’un fichier de configuration qui contient des informations sur la version de Scala, les dépendances de bibliothèque, etc. Collez les éléments suivants dans le fichier simple.sbt que vous enregistrez :


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Veillez à conserver les lignes vides dans le fichier.


3. Dans le dossier **SimpleScalaApp**, créez une structure de répertoires **\\src\\main\\scala**, puis collez le programme Scala (**SimpleApp.scala**) créé précédemment dans le dossier \\src\\main\\scala.
4. Ouvrez une invite de commandes, accédez au répertoire SimpleScalaApp, puis entrez la commande suivante :


		sbt package


	Une fois l’application compilée, le fichier **simpleapp\_2.10-1.0.jar** est créé dans le répertoire **\\target\\scala-2.10** dans le dossier racine SimpleScalaApp.


#### Exécuter la tâche sur le cluster
Dans cette section, vous allez vous connecter à distance au cluster sur lequel Spark est installé, puis copier le dossier cible du projet SimpleScalaApp. Vous allez ensuite utiliser la commande **spark-submit** pour envoyer la tâche au cluster.

1. Accédez à distance au cluster sur lequel Spark est installé. Sur l’ordinateur où vous avez écrit et créé le programme SimpleApp.scala, copiez le dossier **SimpleScalaApp\\target** et collez-le à l’emplacement de votre choix sur le cluster.
2. Dans la session RDP, sur le Bureau, ouvrez la ligne de commande Hadoop, puis naviguez jusqu’à l’emplacement dans lequel vous avez collé le dossier **target**.
3. Entrez la commande suivante pour exécuter le programme SimpleApp.scala :


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Une fois l'exécution du programme terminée, la sortie s'affiche dans la console.


		Lines with a: 21374, Lines with b: 11430

## Installation de Spark à l’aide d'Azure PowerShell

Dans cette section, nous allons utiliser l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d’informations sur la configuration d’une station de travail pour exécuter des applets de commande Azure PowerShell pour HDInsight, consultez la page [Installation et configuration d’Azure PowerShell][powershell-install-configure].

Procédez comme suit :

1. Ouvrez une fenêtre Azure PowerShell et déclarez les variables suivantes :

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Utilisez l’applet de commande **Add-AzureHDInsightScriptAction** pour ajouter une action de script à la configuration du cluster. L'action de script sera exécutée plus tard au moment de la création du cluster.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	L’applet de commande **Add-AzureHDInsightScriptAction** accepte les paramètres suivants :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Paramètre</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Définition</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuration</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Objet de configuration auquel des informations d'action de script sont ajoutées.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom de l'action de script.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Les valeurs correctes sont HeadNode (pour une installation sur le nœud principal) ou DataNode (pour une installation sur tous les nœuds de données). Vous pouvez utiliser l'une de ces valeurs ou les deux.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie l’URI du script qui est exécuté.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres requis par le script. L'exemple de script utilisé dans cette rubrique ne requiert aucun paramètre. Par conséquent, ce paramètre n'apparaît pas dans l'extrait de code ci-dessus.
</td></tr>
</table>

4. Enfin, commencez à créer un cluster personnalisé sur lequel Spark est installé.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.

## Installation de Spark à l’aide de PowerShell

Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_powershell).

## Installation de Spark à l’aide du Kit de développement logiciel (SDK) .NET

Consultez [Personnalisation de clusters HDInsight à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md#call_scripts_using_azure_powershell).


## Voir aussi

- [Installation de Spark sur des clusters HDInsight Linux](hdinsight-hadoop-spark-install-linux.md) : installez Spark sur des clusters HDInsight Linux.
- [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md) : créez des clusters HDInsight.
- [Prise en main d’Apache Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md) : faites vos premiers pas avec Spark sur HDInsight.
- [Personnalisation d’un cluster HDInsight à l’aide d’une action de script][hdinsight-cluster-customize] : personnalisez des clusters HDInsight à l’aide d’une action de script.
- [Développement de scripts d’action de script pour HDInsight](hdinsight-hadoop-script-actions.md) : développez des scripts d’action de script.
- [Installation de R sur des clusters HDInsight][hdinsight-install-r] fournit des instructions sur l’utilisation de la personnalisation des clusters pour installer et utiliser R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.
- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.
- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d'effectuer de puissantes opérations de recherche sur des données stockées.

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md

<!---HONumber=AcomDC_1203_2015-->