<properties
	pageTitle="Gestion des clusters Hadoop dans HDInsight avec le Kit de développement logiciel (SDK) .NET | Microsoft Azure"
	description="Découvrez comment effectuer des tâches d’administration pour les clusters Hadoop dans HDInsight au moyen du Kit de développement logiciel (SDK) .NET HDInsight."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight au moyen du Kit de développement logiciel (SDK) .NET

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

Apprenez à gérer des clusters HDInsight à l’aide du [Kit de développement logiciel (SDK) HDInsight.NET](https://msdn.microsoft.com/library/mt271028.aspx)


**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


##Se connecter à Azure HDInsight

Les packages Nuget suivants doivent être installés :

	Install-Package Microsoft.Azure.Common.Authentication -Pre
	Install-Package Microsoft.Azure.Management.HDInsight -Pre

L’exemple de code suivant montre comment vous connecter à Azure avant que vous puissiez gérer les clusters HDInsight dans votre abonnement Azure.

	using System;
	using System.Security;
	using Microsoft.Azure;
	using Microsoft.Azure.Common.Authentication;
	using Microsoft.Azure.Common.Authentication.Factories;
	using Microsoft.Azure.Common.Authentication.Models;
	using Microsoft.Azure.Management.HDInsight;
	using Microsoft.Azure.Management.HDInsight.Models;

	namespace HDInsightManagement
	{
		class Program
		{
			private static HDInsightManagementClient _hdiManagementClient;
			private static Guid SubscriptionId = new Guid("<Your Azure Subscription ID>");

			static void Main(string[] args)
			{
				var tokenCreds = GetTokenCloudCredentials();
				var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

				_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

				// insert code here

				System.Console.WriteLine("Press ENTER to continue");
				System.Console.ReadLine();
			}

			public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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

			public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
			{
				return new TokenCloudCredentials(subId.ToString(), creds.Token);
			}
		}
	}

Vous devriez voir une invite de commandes lorsque vous exécutez ce programme. Si vous ne voulez pas que l’invite s’affiche, consultez [Créer des applications .NET HDInsight d’authentification non interactive](hdinsight-create-non-interactive-authentication-dotnet-applications.md).

##Créer des clusters

Consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)

##Énumérer les clusters

L’extrait de code suivant répertorie les clusters et certaines propriétés :

    var results = _hdiManagementClient.Clusters.List();
    foreach (var name in results.Clusters) {
        Console.WriteLine("Cluster Name: " + name.Name);
        Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
        Console.WriteLine("\t Cluster location: " + name.Location);
        Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
    }

##Suppression des clusters

Pour supprimer un cluster de façon synchrone ou asynchrone, utilisez l’extrait de code suivant :

    _hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
    _hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
            
##Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

>[AZURE.NOTE] Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés. Voir [Énumération et affichage des clusters](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

- Hadoop

	Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

	Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

- HBase

	Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

- Storm

	Vous pouvez ajouter ou supprimer des nœuds de données en continu dans votre cluster Storm lorsque celui-ci s'exécute. Mais une fois l’opération de mise à l’échelle terminée avec succès, vous devrez rééquilibrer la topologie.

	Cela peut se faire de deux façons à l’aide de :

	* l'interface utilisateur Web de Storm
	* l’outil d’interface de ligne de commande (CLI)

	Pour plus d’informations, consultez la documentation [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	L’interface utilisateur web de Storm est disponible dans le cluster HDInsight :

	![hdinsight storm mise à l’échelle rééquilibrage](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Voici un exemple relatif à l'utilisation de la commande de l'interface en ligne de commande pour rééquilibrer la topologie Storm :

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Pour redimensionner un cluster de façon synchrone ou asynchrone, utilisez l’extrait de code suivant :

    _hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
    _hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
	

##Octroyer/Révoquer l’accès

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour révoquer :

	var httpParams = new HttpSettingsParameters
	{
		HttpUserEnabled = false,
		HttpUsername = "admin",
		HttpPassword = "*******",
	};
	_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);

Pour octroyer :

	var httpParams = new HttpSettingsParameters
	{
		HttpUserEnabled = enable,
		HttpUsername = "admin",
		HttpPassword = "*******",
	};
	_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);


>[AZURE.NOTE] En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

Vous pouvez également le faire via le portail Azure. Consultez [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

##Mettre à jour les informations d’identification de l’utilisateur HTTP

Il s’agit de la même procédure que [l’octroi et la révocation de l’accès HTTP](#grant/revoke-access). Si l’accès HTTP a été octroyé au cluster, vous devez tout d’abord le révoquer. Octroyez ensuite l’accès avec les informations d’identification de l’utilisateur HTTP.


##Trouvez le compte de stockage par défaut

L’extrait de code suivant montre comment obtenir le nom de compte de stockage par défaut et la clé de compte de stockage par défaut pour un cluster.

	var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
	foreach (var key in results.Configuration.Keys)
	{
	    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
	}


##Soumettre les travaux

**Pour envoyer des tâches MapReduce**

Consultez [Exécution des exemples Hadoop MapReduce dans HDInsight](hdinsight-hadoop-run-samples-linux.md).

**Pour envoyer des tâches Hive**

Consultez [Exécution de requêtes Hive avec le Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md).

**Pour envoyer des tâches Pig**

Consultez [Exécution de tâches Pig avec le Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-use-pig-dotnet-sdk.md).

**Pour envoyer des tâches Sqoop**

Consultez l'article [Utilisation de Sqoop avec HDInsight](hdinsight-hadoop-use-sqoop-dotnet-sdk.md).

**Pour envoyer des tâches Oozie**

Consultez [Utilisation d’Oozie avec Hadoop pour définir et exécuter un workflow dans HDInsight](hdinsight-use-oozie-linux-mac.md).

##Téléchargement de données vers le stockage d'objets blob Azure
Consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].


## Voir aussi
* [Documentation de référence sur le Kit de développement logiciel (SDK) .NET HDInsight](https://msdn.microsoft.com/library/mt271028.aspx)
* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide de l’interface de ligne de commande][hdinsight-admin-cli]
* [Création de clusters HDInsight][hdinsight-provision]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Prise en main d'Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-portal-linux.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

<!---HONumber=AcomDC_0504_2016-->