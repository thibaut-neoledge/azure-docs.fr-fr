<properties 
	pageTitle="Installation et utilisation de Giraph sur des clusters Hadoop dans HDInsight | Microsoft Azure" 
	description="Découvrez comment personnaliser un cluster HDInsight avec Giraph. Vous allez utiliser une option de configuration Action de script afin d’utiliser un script pour installer Giraph." 
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


# Installation de Giraph sur les clusters Hadoop HDInsight et utilisation de Giraph pour traiter des graphiques à grande échelle

Vous pouvez installer Giraph sur n’importe quel type de cluster dans Hadoop sur Azure HDInsight à l’aide de la personnalisation de cluster **Action de script**. Une action de script vous permet d'exécuter des scripts pour personnaliser un cluster, uniquement au moment de sa création. Pour plus d’informations, consultez la page [Personnaliser des clusters HDInsight à l’aide d’une action de script][hdinsight-cluster-customize].

Dans cette rubrique, vous allez apprendre à installer Giraph à l'aide d'une action de script. Après avoir installé Giraph, vous apprendrez également comment l'utiliser pour la plupart des applications courantes, à savoir traiter des graphiques à grande échelle.


## <a name="whatis"></a>Présentation de Giraph

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight. Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

- identifier les amis potentiels en fonction de vos relations actuelles ;
- identifier le chemin le plus court entre deux ordinateurs d'un réseau ;
- calculer le classement de pages web.

   
## <a name="install"></a>Installation de Giraph

Pour obtenir un exemple de script pour installer Giraph sur un cluster HDInsight, téléchargez l’objet blob de stockage Azure en lecture seule à l’adresse [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). Cette section explique comment utiliser l'exemple de script dans le cadre de l'approvisionnement du cluster à l'aide du portail Azure.

> [AZURE.NOTE]L'exemple de script fonctionne uniquement avec un cluster HDInsight version 3.1. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](hdinsight-component-versioning.md).

1. Commencez l’approvisionnement d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE**, comme cela est décrit dans la rubrique [Approvisionnement d’un cluster à l’aide d’options personnalisées](hdinsight-provision-clusters.md#portal). 
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **Ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Utilisation d’une action de script pour personnaliser un cluster")
	
	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script. Par exemple, <b>Installation Giraph</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l'URI (Uniform Resource Identifier) du script appelé pour personnaliser le cluster. Par exemple, <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Le script d'installation de Giraph ne nécessite aucun paramètre. Vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Après avoir ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

Vous pouvez également utiliser le script pour installer Giraph sur HDInsight à l'aide d'Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Vous trouverez des instructions relatives à ces procédures dans la suite de cette rubrique.

## <a name="usegiraph"></a>Utilisation de Giraph dans HDInsight

L’exemple SimpleShortestPathsComputation indique l’implémentation basique de <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> pour trouver le chemin le plus court entre des objets d’un graphique. Procédez comme suit pour télécharger les exemples de données et l'exemple de fichier JAR, exécuter une tâche avec l'exemple SimpleShortestPathsComputation, puis afficher les résultats.

1. Téléchargez un exemple de fichier de données dans le stockage d'objets blob Azure. Sur votre poste de travail local, créez un nouveau fichier nommé **tiny\_graph.txt**. Il doit contenir les lignes suivantes :

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Téléchargez le fichier tiny\_graph.txt dans le stockage principal pour votre cluster HDInsight. Pour plus d’informations sur le téléchargement de données, consultez la rubrique [Téléchargement de données pour les tâches Hadoop dans HDInsight](hdinsight-upload-data.md).

	Ces données décrivent une relation entre les objets d’un graphique dirigé, en utilisant le format [source\_id, source\_value,[[dest\_id\], [edge\_value\],...\]\]. Chaque ligne représente une relation entre un objet **source\_id** et un ou plusieurs objets **dest\_id**. La valeur **edge\\\_value** (ou pondération) peut être considérée comme l’intensité ou la distance de la connexion entre **source\_id** et **dest\_id**.

	Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela :

	![tiny\_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

	

4. Exécutez l'exemple SimpleShortestPathsComputation. Utilisez les applets de commande Azure PowerShell suivantes pour exécuter l'exemple en utilisant le fichier tiny\_graph.txt comme entrée. Pour cela, il est nécessaire que vous ayez installé et configuré [Azure PowerShell][powershell-install].

		$clusterName = "clustername"
		# Giraph examples jar
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	Dans l’exemple ci-dessus, remplacez **clustername** par le nom de votre cluster HDInsight sur lequel Giraph est installé.

5. Affichez les résultats. Une fois la tâche terminée, les résultats sont stockés dans deux fichiers de sortie dans le dossier \_\___wasb:///example/out/shotestpaths__. Les fichiers se nomment __part-m-00001__ et __part-m-00002__. Procédez comme suit pour télécharger et afficher le résultat :

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	Cette opération crée la structure de répertoires __example/output/shortestpaths__ dans le répertoire actuel de votre poste de travail et télécharge les deux fichiers de sortie à cet emplacement.

	Utilisez l’applet de commande __Cat__ pour afficher le contenu des fichiers :

		Cat example/output/shortestpaths/part*

	Le résultat doit ressembler à ce qui suit :


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme `destination_id distance`, où distance est la valeur (ou la pondération) des bords parcourus entre l’ID d’objet 1 et l’ID cible.
	
	En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et ID 4 est 5. Il s’agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis entre <span style="color:red">ID 3 et 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)


## <a name="usingPS"></a>Installation de Giraph sur des clusters HDInsight Hadoop à l’aide d’Azure PowerShell

Dans cette section, nous allons utiliser l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour en savoir plus sur la configuration d’une station de travail pour exécuter des applets de commande Azure PowerShell pour HDInsight, consultez la page [Installation et configuration d’Azure PowerShell][powershell-install].

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
		$version = "<HDInsightClusterVersion>"          # For example, "3.1"
	
2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilisez l’applet de commande **Add-AzureHDInsightScriptAction** pour ajouter une action de script à la configuration du cluster. L'action de script sera exécutée plus tard au moment de la création du cluster.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

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
	
4. Pour terminer, commencez à approvisionner un cluster personnalisé sur lequel Giraph est installé :
	
		# Start provisioning a cluster with Giraph installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.


## <a name="usingSDK"></a>Installation de Giraph sur des clusters HDInsight Hadoop à l’aide du Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Cette section fournit des instructions sur l'utilisation d'une action de script à partir du Kit de développement logiciel (SDK) pour approvisionner un cluster sur lequel Giraph est installé. Effectuez les étapes suivantes :

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
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

10. Ajoutez le code suivant à la fonction Main() pour utiliser la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) et appeler un script personnalisé pour installer Giraph :

		// Add the script action to install Giraph
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //Because the script used does not require any parameters
        ));

11. Enfin, créez le cluster :

		client.CreateCluster(clusterInfo);

12. Enregistrez les modifications apportées à l'application et générez la solution.

**Pour exécuter l’application**

Ouvrez une console Azure PowerShell, naviguez jusqu'à l'emplacement où vous avez enregistré le projet Visual Studio, accédez au répertoire \\bin\\debug du projet, puis exécutez la commande suivante :

	.\CreateGiraphCluster <cluster-name>

Entrez un nom de cluster, puis appuyez sur ENTRÉE pour approvisionner un cluster sur lequel Giraph est installé.


## Voir aussi##
- [Installation et utilisation de Spark sur des clusters HDInsight][hdinsight-install-spark] pour obtenir des instructions sur l’utilisation de la personnalisation de cluster pour installer et utiliser Spark sur des clusters Hadoop HDInsight. Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de « big data ».
- [Installation de R sur des clusters HDInsight][hdinsight-install-r] pour obtenir des instructions sur la personnalisation des clusters pour installer et utiliser R sur des clusters HDInsight Hadoop. R se compose d'un langage et d'un environnement open source destinés au calcul de statistiques. Il intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues.
- [Installation de Solr sur des clusters HDInsight](hdinsight-hadoop-solr-install.md). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d'effectuer de puissantes opérations de recherche sur des données stockées.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
 

<!---HONumber=August15_HO6-->