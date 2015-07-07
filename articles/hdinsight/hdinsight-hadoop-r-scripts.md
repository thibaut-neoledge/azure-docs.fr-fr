<properties 
	pageTitle="Utilisation de R dans HDInsight pour personnaliser des clusters| Microsoft Azure" 
	description="Découvrez comment installer et utiliser R pour personnaliser des clusters Hadoop." 
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
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Installation et utilisation de R sur des clusters HDInsight Hadoop

Vous pouvez installer R sur n’importe quel type de cluster dans Hadoop sur HDInsight à l’aide de la personnalisation de cluster **Action de script**. Les scientifiques et les analystes de données peuvent ainsi utiliser R pour déployer l’infrastructure de programmation MapReduce/YARN pour traiter de grandes quantités de données sur des clusters Hadoop déployés dans HDInsight.

Une action de script vous permet d'exécuter des scripts pour personnaliser un cluster, uniquement au moment de sa création. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-cluster-customize].


## <a name="whatIs"></a>Présentation de R

Le <a href="http://www.r-project.org/" target="_blank">projet R pour le calcul de statistiques</a> se compose d’un langage et d’un environnement open source destinés au calcul de statistiques. R intègre des centaines de fonctions statistiques et possède son propre langage de programmation qui regroupe des aspects de la programmation fonctionnelle et de la programmation orientée objet. Il offre également des fonctionnalités graphiques étendues. R est l'environnement de programmation préféré des statisticiens professionnels et des scientifiques dans de nombreux domaines.

Les scripts R peuvent être exécutés dans HDInsight sur des clusters Hadoop personnalisés au moment de leur création à l'aide d'une action de script pour installer l'environnement R. R est compatible avec le stockage d'objets Blob Azure (WASB), ce qui permet aux données stockées à cet emplacement d'être traitées avec R sur HDInsight.

## <a name="install"></a>Installation de R

Un [exemple de script](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) d’installation de R sur un cluster HDInsight est disponible à partir d’un objet blob en lecture seule dans Azure Storage. Cette section explique comment utiliser l’exemple de script dans le cadre de l’approvisionnement du cluster à l’aide du portail Azure.

> [AZURE.NOTE]L'exemple de script a été introduit dans la version 3.1 du cluster HDInsight. Pour plus d’informations sur les versions des clusters HDInsight, consultez la page [Versions des clusters HDInsight](../hdinsight-component-versioning/).

1. Commencez l’approvisionnement d’un cluster à l’aide de l’option **CRÉATION PERSONNALISÉE**, comme cela est décrit dans la rubrique [Approvisionnement d’un cluster à l’aide d’options personnalisées](../hdinsight-provision-clusters/#portal). 
2. Sur la page **Actions de script** de l’Assistant, cliquez sur **Ajouter l’action de script** pour fournir des informations sur l’action de script, comme ceci :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Utilisation d’une action de script pour personnaliser un cluster")
	
	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l’action de script, par exemple, <b>Installer&#160;R</b>.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l’URI du script appelé pour personnaliser le cluster, par exemple, <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i>.</td></tr>
	<tr><td>Type de nœud</td>
		<td>Spécifiez les nœuds sur lesquels le script de personnalisation est exécuté. Vous avez le choix entre <b>Tous les nœuds</b>, <b>Nœuds principaux uniquement</b> et <b>Nœuds de travail uniquement</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande. Cependant, le script d’installation de&#160;R ne nécessite aucun paramètre, vous pouvez donc laisser ce champ vide.</td></tr>
</table>Vous pouvez ajouter plusieurs actions de script pour installer plusieurs composants sur le cluster. Une fois que vous avez ajouté les scripts, cliquez sur la coche pour démarrer l'approvisionnement du cluster.

Vous pouvez également utiliser le script pour installer R sur HDInsight à l’aide d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight. Vous trouverez des instructions relatives à ces procédures dans la suite de cet article.

## <a name="useR"></a>Exécution des scripts R dans HDInsight
Cette section décrit comment exécuter un script R sur le cluster Hadoop avec HDInsight.

1. **Établissez une connexion Bureau à distance au cluster :** dans le portail de gestion Azure, activez le Bureau à distance pour le cluster que vous avez créé et sur lequel vous avez installé R, puis accédez à distance au cluster. Pour consulter des instructions à ce sujet, consultez la page <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Connexion à des clusters HDInsight à l’aide de RDP</a>.

2. **Ouvrez la console R** : l’installation de R crée un lien vers la console R sur le Bureau du nœud principal. Cliquez sur celui-ci pour ouvrir la console R.

3. **Exécutez le script R** : pour exécuter directement le script R à partir de la console R, collez-le dans la console, sélectionnez-le, puis appuyez sur Entrée. L'exemple simple de script suivant génère les nombres de 1 à 100, puis les multiplie par 2.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Les deux premières lignes appellent les bibliothèques RHadoop installées avec R. La dernière ligne imprime les résultats dans la console. La sortie doit ressembler à ceci :

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Installation de R sur HDInsight à l’aide d’Azure PowerShell

Dans cette section, nous allons utiliser l’applet de commande **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** pour appeler des scripts avec une action de script pour personnaliser un cluster. Avant de poursuivre, assurez-vous que vous avez installé et configuré Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des applets de commande HDInsight Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell][powershell-install-configure].

Procédez comme suit :

1. Ouvrez la console Azure PowerShell et déclarez les variables suivantes :

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. Spécifiez les valeurs de configuration telles que les nœuds du cluster et le stockage par défaut à utiliser.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Utilisez l’applet de commande **Add-AzureHDInsightScriptAction** pour appeler l’exemple de script pour installer R, par exemple :

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


	L’applet de commande **Add-AzureHDInsightScriptAction** accepte les paramètres suivants :

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Paramètre</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Définition</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Configuration</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Objet de configuration auquel des informations d’action de script sont ajoutées.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nom de l'action de script</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie les nœuds sur lesquels le script de personnalisation est exécuté. Les valeurs correctes sont **HeadNode** (pour une installation sur le nœud principal) ou **DataNode** (pour une installation sur tous les nœuds de données). Vous pouvez utiliser l'une de ces valeurs ou les deux.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Paramètres requis par le script. 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Spécifie l'URI du script qui est exécuté.</td></tr>
</table>
	
4. Enfin, approvisionnez le cluster que vous avez personnalisé pour installer R.
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Lorsque vous y êtes invité, entrez les informations d'identification du cluster. La création du cluster peut prendre plusieurs minutes.


## <a name="usingSDK"></a>Installation de R sur HDInsight à l’aide du Kit de développement logiciel (SDK) .NET

Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation d'HDInsight à partir d'une application .NET.

Effectuez les procédures suivantes pour approvisionner un cluster HDInsight en utilisant le Kit de développement logiciel (SDK) :

- [Installation du Kit de développement logiciel (SDK) HDInsight](#installSDK)
- [Création d’un certificat auto-signé](#createCert)
- [Création d’une application .NET dans Visual Studio](#createApp)
- [Exécution de l’application](#runApp)

Les sections suivantes montrent comment effectuer ces procédures.

### <a name="installSDK"></a>Pour installer le Kit de développement logiciel (SDK) .NET HDInsight

Vous pouvez installer la dernière version du Kit de développement logiciel (SDK) sur [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Vous pourrez consulter les instructions dans la procédure suivante.

### <a name="createCert"></a>Pour créer un certificat auto-signé

Créez un certificat auto-signé, installez-le sur votre poste de travail et téléchargez-le dans votre abonnement Azure. Pour obtenir des instructions, consultez la page [Création d'un certificat auto-signé](http://go.microsoft.com/fwlink/?LinkId=511138).


### <a name="createApp"></a>Pour créer une application .NET dans Visual Studio

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Console du Gestionnaire de package**.

6. Exécutez la commande suivante dans la console pour installer le package.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Cette commande ajoute des bibliothèques .NET et leurs références à partir du projet Visual Studio en cours.

7. Dans l'**Explorateur de solutions**, double-cliquez sur **Program.cs** pour l'ouvrir.

8. Ajoutez les instructions **using** suivantes au début du fichier :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Dans la fonction **Main()**, collez le code suivant, puis entrez des valeurs pour les variables :
		
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

10. Ajoutez le code suivant à la fonction **Main()** pour utiliser la classe [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx) et appeler un script personnalisé pour installer R.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Enfin, créez le cluster :

		client.CreateCluster(clusterInfo);

11. Enregistrez les modifications apportées à l'application et générez la solution.

### <a name="runApp"></a>Pour exécuter l’application

Ouvrez la console Azure PowerShell, naviguez jusqu’à l’emplacement dans lequel vous avez enregistré le projet, accédez au répertoire \\bin\\debug du projet, puis exécutez la commande suivante :

	.\CreateRCluster <cluster-name>

Entrez un nom de cluster, puis appuyez sur Entrée pour approvisionner un cluster sur lequel R est installé.

## <a name="seeAlso"></a>Voir aussi

- [Installation et utilisation de Spark sur des clusters HDInsight][hdinsight-install-spark] pour obtenir des instructions sur la personnalisation de clusters pour installer et utiliser Spark sur des clusters Hadoop HDInsight. Spark est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d'analyse de « big data ».
- [Installation de Giraph sur des clusters HDInsight](../hdinsight-hadoop-giraph-install). Utilisez la personnalisation de clusters pour installer Giraph sur des clusters HDInsight Hadoop. Giraph permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.
- [Installation de Solr sur des clusters HDInsight](../hdinsight-hadoop-solr-install). Utilisez la personnalisation de clusters pour installer Solr sur des clusters HDInsight Hadoop. Solr vous permet d’effectuer de puissantes opérations de recherche sur des données stockées.

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/

<!--HONumber=54--> 