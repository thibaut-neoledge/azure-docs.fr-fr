<properties 
	pageTitle="Utilisation d’une action de script dans HDInsight pour installer Solr sur un cluster Hadoop| Microsoft Azure" 
	description="Découvrez comment personnaliser un cluster HDInsight avec Solr. Vous allez utiliser une option de configuration Action de script afin d’utiliser un script pour installer Solr." 
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
	ms.date="07/11/2015" 
	ms.author="nitinme"/>

# Installation et utilisation de Solr sur des clusters HDInsight Hadoop

Vous pouvez installer Solr sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide de la personnalisation de cluster **Action de script**. Une action de script vous permet d'exécuter des scripts pour personnaliser un cluster, uniquement au moment de sa création. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-cluster-customize].

Dans cette rubrique, vous allez apprendre à installer Solr à l’aide d’une action de script. Solr est une puissante plateforme de recherche qui fournit des fonctionnalités de recherche au niveau de l'entreprise pour les données gérées par Hadoop. Après avoir installé Solr sur un cluster HDInsight, vous apprendrez également comment rechercher des données à l’aide de Solr.

> [AZURE.NOTE]L'exemple de script utilisé dans cette rubrique crée un cluster Solr avec une configuration spécifique. Si vous souhaitez configurer le cluster Solr avec d'autres collections, partitions, schémas, réplicas, etc., vous devez modifier le script et les fichiers binaires Solr en conséquence.


## <a name="whatis"></a>Présentation de Solr

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> est une plateforme de recherche d’entreprise qui permet d’effectuer de puissantes opérations de recherche en texte intégral sur des données. Alors que Hadoop permet de stocker et de gérer de grandes quantités de données, Apache Solr fournit les fonctionnalités de recherche nécessaires pour les récupérer rapidement. Cette rubrique contient les instructions de personnalisation d'un cluster HDInsight pour installer Solr.

## <a name="install"></a>Installation de Solr

Pour obtenir un exemple de script pour installer Solr sur un cluster HDInsight, téléchargez l’objet blob de stockage Azure en lecture seule à l’adresse [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1). Cette section explique comment utiliser l'exemple de script dans le cadre de l'approvisionnement du cluster à l'aide du portail Azure.


> [AZURE.NOTE]L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).


1. Commencez l’approvisionnement d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE**, comme cela est décrit dans la rubrique [Approvisionnement d’un cluster à l’aide d’options personnalisées](hdinsight-provision-clusters.md#portal). 
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **Ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Utilisation d’une action de script pour personnaliser un cluster")
	
	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Solr</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Le script d’installation de&#160;Solr ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

Vous pouvez également utiliser le script pour installer Solr sur HDInsight à l’aide d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Vous trouverez des instructions relatives à ces procédures dans la suite de cette rubrique.

## <a name="usesolr"></a>Utilisation de Solr dans HDInsight

Vous devez commencer par indexer Solr avec quelques fichiers de données. Vous pouvez ensuite utiliser Solr pour exécuter des requêtes de recherche sur les données indexées. Procédez comme suit pour utiliser Solr dans un cluster HDInsight :

1. **Utilisez le protocole RDP (Remote Desktop) pour vous connecter à distance au cluster HDInsight avec Solr installé**. À partir du portail Azure, activez le Bureau à distance pour le cluster que vous avez créé avec Solr installé, puis accédez à distance au cluster. Pour consulter des instructions à ce sujet, consultez la page <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.

2. **Indexez Solr en téléchargeant des fichiers de données**. Lorsque vous indexez Solr, vous y placez des documents sur lesquels vous devrez peut-être effectuer des recherches. Pour indexer Solr, utilisez le protocole RDP pour vous connecter à distance au cluster, accédez au Bureau, ouvrez la ligne de commande Hadoop et accédez à **C:\apps\dist\solr-4.7.2\example\exampledocs**. Exécutez la commande suivante :
	
		java -jar post.jar solr.xml monitor.xml

	Le résultat suivant s’affiche sur la console :

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	L’utilitaire post.jar indexe Solr avec deux exemples de documents : **solr.xml** et **monitor.xml**. L'utilitaire post.jar et les exemples de documents sont disponibles avec l'installation Solr.

3. **Utilisez le tableau de bord Solr pour effectuer une recherche dans les documents indexés**. Dans la session RDP au cluster HDInsight, ouvrez Internet Explorer, puis lancez le tableau de bord Solr à l’adresse **http://headnodehost:8983/solr/#/**. Dans la liste déroulante **Sélecteur de base** située dans le volet gauche, sélectionnez **collection1** et cliquez ensuite sur **Requête**. Par exemple, pour sélectionner et renvoyer tous les documents dans Solr, renseignez les valeurs suivantes :
	1. Dans la zone de texte **q**, entrez ***:***. Tous les documents indexés dans Solr sont alors renvoyés. Si vous souhaitez rechercher une chaîne spécifique dans les documents, vous pouvez la saisir à cet emplacement.
	2. Sélectionnez le format de sortie dans la zone de texte **wt**. La valeur par défaut est **json**. Cliquez sur **Exécuter la requête**.

		![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Exécution d’une requête sur le tableau de bord Solr")
	
	La sortie renvoie les deux documents que nous avions utilisés pour indexer Solr. La sortie se présente comme suit :

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Recommandé : sauvegardez les données indexées à partir de Solr dans un objet WASB (Azure Storage Blob) associé au cluster HDInsight**. Il est conseillé de sauvegarder les données indexées à partir de nœuds de cluster Solr sur un stockage d’objets blob Azure. Pour ce faire, procédez comme suit :

	1. À partir de la session RDP, ouvrez Internet Explorer et accédez à l'URL suivante :

			http://localhost:8983/solr/replication?command=backup

		La réponse doit ressembler à ceci :

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Dans la session à distance, accédez à {SOLR_HOME}{Collection}\data. Pour le cluster créé à l’aide de l’exemple de script, il doit s’agir de **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. Un dossier d’instantanés créé avec un nom semblable à **snapshot.*timestamp*** doit normalement figurer à cet emplacement.
	
	3. Compressez le dossier d’instantanés et téléchargez-le vers le stockage d’objets blob Azure. À partir de la ligne de commande Hadoop, accédez à l’emplacement du dossier d’instantanés à l’aide de la commande suivante :

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Cette commande copie l’instantané dans /example/data/ sous le conteneur, dans le compte de stockage par défaut associé au cluster.

## <a name="usingPS"></a>Installation de Solr sur des clusters HDInsight Hadoop à l’aide d’Azure PowerShell

Dans cette section, nous allons utiliser l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts en utilisant une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des applets de commande HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell][powershell-install-configure].

Procédez comme suit :

1. Ouvrez une fenêtre Azure PowerShell et déclarez les variables suivantes :

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilisez l’applet de commande **Add-AzureHDInsightScriptAction** pour ajouter une action de script à la configuration du cluster. L'action de script sera exécutée plus tard au moment de la création du cluster.

		# Add the script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

	L’applet de commande **Add-AzureHDInsightScriptAction** reçoit les paramètres suivants :

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nœuds sur lesquels le script de personnalisation est exécuté. Les valeurs correctes sont HeadNode (pour une installation sur le nœud principal) ou DataNode (pour une installation sur tous les nœuds de données). Vous pouvez utiliser l'une de ces valeurs ou les deux.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">URI du script qui est exécuté.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres requis par le script. L'exemple de script utilisé dans cette rubrique ne requiert aucun paramètre. Par conséquent, ce paramètre n'apparaît pas dans l'extrait de code ci-dessus.
</td></tr>
</table>
	
4. Pour terminer, commencez à approvisionner un cluster personnalisé sur lequel Solr est installé.
	
		# Start provisioning a cluster with Solr installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.


## <a name="usingSDK"></a>Installation de Solr sur des clusters HDInsight Hadoop à l’aide du Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Cette section fournit des instructions sur l'utilisation d'une action de script à partir du Kit de développement logiciel (SDK) pour approvisionner un cluster sur lequel Solr est installé. Effectuez les étapes suivantes :

- Installation du Kit de développement logiciel (SDK) HDInsight
- Créer un certificat auto-signé
- Création d'une application console
- Exécution de l'application


**Pour installer le Kit de développement logiciel (SDK) .NET HDInsight**

Vous pouvez installer la dernière version publiée du Kit de développement logiciel (SDK) disponible sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

**Pour créer un certificat auto-signé**

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


**Pour créer une application Visual Studio**

1. Ouvrez Visual Studio 2013.

2. Dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3. Dans **Nouveau projet**, entrez ou sélectionnez les valeurs suivantes :
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriété</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valeur</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Catégorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modèles/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modèle</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Application console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer le package :

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références à partir du projet Visual Studio en cours.

	
7. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions using suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Dans la fonction Main(), copiez et collez le code suivant, puis entrez des valeurs pour les variables :
		
        var clusterName = args[0];

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;
        
        // Provide the cluster information
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

10. Ajoutez le code suivant à la fonction Main() pour utiliser la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) et appeler un script personnalisé pour installer Solr.

		// Add the script action to install Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //Because the script used does not require any parameters
        ));

11. Enfin, créez le cluster.

		client.CreateCluster(clusterInfo);

11. Enregistrez les modifications apportées à l'application et générez la solution.

**Pour exécuter l’application**

Ouvrez une console Windows PowerShell ou Azure PowerShell, naviguez jusqu’à l’emplacement où vous avez enregistré le projet Visual Studio, accédez au répertoire \bin\debug du projet, puis exécutez la commande suivante :

	.\CreateSolrCluster <cluster-name>

Entrez un nom de cluster, puis appuyez sur ENTRÉE pour approvisionner un cluster sur lequel Solr est installé.


## Voir aussi##
- [Installation et utilisation de Spark sur des clusters HDInsight][hdinsight-install-spark]. Utilisez la personnalisation de clusters pour installer Spark sur des clusters HDInsight Hadoop. Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de « big data ».
- [Installation de R sur des clusters HDInsight][hdinsight-install-r]. Utilisez la personnalisation de clusters pour installer R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.
- [Installation de Giraph sur des clusters HDInsight](hdinsight-hadoop-giraph-install.md). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques à l’aide de Hadoop et peut être utilisé avec Azure HDInsight.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=July15_HO4-->