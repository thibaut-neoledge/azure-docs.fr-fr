<properties
	pageTitle="Personnaliser des clusters HDInsight à l'aide d’actions de script | Microsoft Azure"
	description="Découvrez comment personnaliser des clusters HDInsight à l'aide d'une action de script."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Personnalisation des clusters HDInsight à l'aide d'une action de script (Windows)

HDInsight fournit une option de configuration intitulée **Action de script**, qui appelle des scripts personnalisés qui définissent la personnalisation à effectuer sur le cluster pendant le processus de création. Ces scripts permettent d’installer des logiciels supplémentaires sur un cluster ou de modifier la configuration des applications d’un cluster.

[AZURE.INCLUDE [version préliminaire du portail hdinsight azure](../../includes/hdinsight-azure-preview-portal.md)]

* [Personnaliser des clusters HDInsight à l'aide d'une action de script](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]Les informations présentes dans cet article sont spécifiques aux clusters HDInsight sous Windows. Pour obtenir une version de cet article spécifique aux clusters basés sur Linux, consultez [Personnaliser des clusters HDInsight à l’aide de l’action de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

Il est également possible de personnaliser les clusters HDInsight de bien d’autres façons, notamment en ajoutant des comptes de stockage supplémentaires, en modifiant les fichiers de configuration hadoop (core-site.xml, hive-site.xml, etc.) ou encore en ajoutant des bibliothèques partagées (comme Hive ou Oozie) dans des emplacements communs du cluster. Ces personnalisations peuvent être effectuées dans Azure PowerShell, le Kit de développement logiciel (SDK) Azure HDInsight .NET ou la version préliminaire du portail Azure. Pour plus d'informations, consultez [Création de clusters Hadoop dans HDInsight][hdinsight-provision-cluster].

## Action de script dans le processus de création de cluster

L’action de script est utilisée uniquement pendant la création d’un cluster. Le diagramme suivant illustre le moment de l’exécution de l’action de script pendant le processus de création :

![Personnalisation du cluster HDInsight et procédure de création d’un cluster][img-hdi-cluster-states]

Quand le script est en cours d’exécution, le cluster entre dans la phase **ClusterCustomization**. À ce stade, le script est exécuté sous le compte de l'administrateur système, en parallèle sur tous les nœuds spécifiés dans le cluster, et fournit des privilèges d'administrateur complets sur les nœuds.

> [AZURE.NOTE]Étant donné que vous disposez de privilèges d’administrateur sur les nœuds du cluster au cours de la phase **ClusterCustomization**, vous pouvez utiliser le script pour effectuer des opérations comme arrêter et démarrer des services, y compris des services liés à Hadoop. Vous devez donc vous assurer, dans le cadre du script, que les services Ambari et autres services liés à Hadoop sont en cours d’exécution avant la fin de l’exécution du script. Ces services sont requis pour établir correctement l'intégrité et l'état du cluster pendant sa création. Si vous modifiez la configuration d'un cluster d'une manière qui affecte ces services, vous devez utiliser les fonctions d'assistance fournies. Pour plus d’informations sur les fonctions d’assistance, consultez [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script].

La sortie et les journaux des erreurs du script sont stockés dans le compte de stockage par défaut spécifié pour le cluster. Les journaux sont stockés dans une table nommée **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Il s’agit de journaux d’agrégation provenant du script exécuté sur tous les nœuds (nœud principal et nœuds de travail) dans le cluster.

Chaque cluster peut accepter plusieurs actions de script qui sont appelées dans l’ordre spécifié. Un script peut être exécuté sur le nœud principal et/ou les nœuds de travail.

HDInsight propose plusieurs scripts pour installer les composants suivants sur des clusters HDInsight :

Nom | Script
----- | -----
**Installation de Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Consultez [Installation et utilisation de Spark sur les clusters HDInsight][hdinsight-install-spark].
**Installation de R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Consultez [Installation et utilisation de R sur les clusters HDInsight][hdinsight-install-r].
**Installation de Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Consultez [Installation et utilisation de Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md).
- **Installation de Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Consultez [Installation et utilisation de Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).



## Appel de scripts à l’aide du portail Azure en version préliminaire

**Dans le portail Azure en version préliminaire**

1. Démarrez la création d'un cluster comme décrit dans [Création de clusters Hadoop dans HDInsight](hdinsight-provision-clusters.md#portal).
2. Dans Configuration facultative, sur le panneau **Actions de script**, cliquez sur **ajouter l’action de script** pour fournir des informations sur l’action de script, comme illustré ci-dessous :

	![Utilisation d’une action de script pour personnaliser un cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Utilisation d’une action de script pour personnaliser un cluster")

	<table border='1'>
	<tr><th>Propriété</th><th>Valeur</th></tr>
	<tr><td>Nom</td>
		<td>Indiquez un nom pour l'action de script.</td></tr>
	<tr><td>URI du script</td>
		<td>Spécifiez l’URI du script appelé pour personnaliser les clusters.</td></tr>
	<tr><td>Head/Worker</td>
		<td>Spécifiez les nœuds (**Head** ou **Worker**) sur lesquels le script de personnalisation est exécuté.</b>.
	<tr><td>Paramètres</td>
		<td>Spécifiez les paramètres, si le script le demande.</td></tr>
</table>Appuyez sur ENTRÉE pour ajouter plusieurs actions de script et installer plusieurs composants sur le cluster.

3. Cliquez sur **Sélectionner** pour enregistrer la configuration d’action de script et poursuivre la création du cluster.

## Appel de scripts à l’aide d’Azure PowerShell

Le script PowerShell suivant montre comment installer Spark sur un cluster HDInsight basé sur Windows. Pour installer d'autres logiciels, vous devrez remplacer le fichier de script dans le script :

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

## Appel de scripts à l'aide du Kit de développement logiciel (SDK) .NET 

L’exemple suivant montre comment installer Spark sur un cluster HDInsight basé sur Windows. Pour installer d'autres logiciels, vous devrez remplacer le fichier de script dans le code.

**Pour créer un cluster HDInsight avec Spark**

1. Créez une application console C# dans Visual Studio.
2. À partir de la console du gestionnaire de package Nuget, exécutez la commande suivante.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Utilisez les instructions using suivantes dans le fichier Program.cs :

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Placez le code dans la classe en procédant comme suit :

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Appuyez sur **F5** pour exécuter l'application.


## Prise en charge des logiciels open source utilisés sur les clusters HDInsight
Le service Microsoft Azure HDInsight est une plateforme flexible qui vous permet de créer des applications de données volumineuses (Big Data) dans le cloud à l’aide d’un écosystème de technologies open source articulées autour de Hadoop. Microsoft Azure fournit un niveau de support général pour les technologies open source, comme indiqué dans la section **Portée du support** du site web <a href="http://azure.microsoft.com/support/faq/" target="_blank">FAQ du support Azure</a>. Le service HDInsight fournit un niveau de support supplémentaire pour certains composants, comme indiqué ci-dessous.

Deux types de composant open source sont disponibles dans le service HDInsight :

- **Composants intégrés** : ces composants sont préinstallés sur les clusters HDInsight et fournissent la fonctionnalité principale du cluster. Par exemple, YARN ResourceManager, le langage de requête Hive (HiveQL) et la bibliothèque Mahout appartiennent à cette catégorie. Une liste complète des composants de cluster est disponible sur la page <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Nouveautés des versions de cluster Hadoop fournies par HDInsight</a>.
- **Composants personnalisés** : en tant qu’utilisateur du cluster, vous pouvez installer ou utiliser, dans votre charge de travail, tout composant qui est disponible dans la communauté ou que vous avez créé.

Les composants intégrés bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.

> [AZURE.WARNING]Les composants fournis avec le cluster HDInsight bénéficient d’une prise en charge totale, et le support Microsoft vous aidera à identifier et à résoudre les problèmes liés à ces composants.
>
> Les composants personnalisés bénéficient d'un support commercialement raisonnable pour vous aider à résoudre le problème. Cela signifie SOIT que le problème pourra être résolu, SOIT que vous serez invité à affecter les ressources disponibles pour les technologies Open Source. Vous pouvez, par exemple, utiliser de nombreux sites de communauté, comme le [forum MSDN sur HDInsight](https://social.msdn.microsoft.com/Forums/azure/fr-FR/home?forum=hdinsight) ou [http://stackoverflow.com](http://stackoverflow.com). En outre, les projets Apache ont des sites de projet sur [http://apache.org](http://apache.org) ; par exemple, [Hadoop](http://hadoop.apache.org/) ou [Spark](http://spark.apache.org/).

Le service HDInsight fournit plusieurs méthodes d’utilisation de ces composants personnalisés. Quel que soit le mode d’utilisation ou d’installation du composant sur le cluster, le même niveau de support s’applique. Vous trouverez, ci-dessous, la liste des méthodes les plus courantes d’utilisation des composants personnalisés sur des clusters HDInsight.

1. Envoi de travaux : il est possible d’envoyer des travaux Hadoop ou d’autres types de travaux au cluster qui exécute ou utilise des composants personnalisés.
2. Personnalisation de cluster : lors de la création du cluster, vous pouvez spécifier des paramètres supplémentaires et des composants personnalisés qui seront installés sur les nœuds de cluster.
3. Exemples : pour des composants personnalisés fréquemment utilisés, il arrive que Microsoft et d’autres éditeurs proposent des exemples illustrant leur utilisation sur les clusters HDInsight. Ces exemples sont fournis sans support.

## Développer des scripts d’action de script

Consultez [Développement de scripts d’action de script pour HDInsight][hdinsight-write-script].


## Voir aussi

- [Création de clusters Hadoop dans HDInsight][hdinsight-provision-cluster] pour obtenir des instructions sur la création d’un cluster HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters HDInsight][hdinsight-install-r]
- [Installation et utilisation de Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md)
- [Installation et utilisation de Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Procédure de création d’un cluster"

<!---HONumber=Oct15_HO3-->