<properties
   pageTitle="Approvisionner des clusters Apache Spark dans HDInsight | Microsoft Azure"
   description="Découvrez comment configurer des clusters Spark pour Azure HDInsight à l’aide du portail Azure, d’Azure PowerShell, d’une ligne de commande ou du Kit de développement logiciel (SDK) .NET HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/22/2015"
    ms.author="nitinme"/>

# Approvisionner des clusters Apache Spark dans HDInsight à l’aide d’options personnalisées

Dans la plupart des scénarios, vous pouvez approvisionner un cluster Spark à l’aide de la méthode de création rapide décrite dans la rubrique [Prendre en main Apache Spark sur HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Dans certains scénarios, vous souhaiterez peut-être approvisionner un cluster personnalisé. Par exemple, vous pouvez souhaiter attacher un magasin de métadonnées externe pour que vos métadonnées Hive demeurent persistantes au-delà de la durée de vie d’un cluster, ou utiliser un espace de stockage supplémentaire avec le cluster.

Pour ces scénarios, entre autres, cet article fournit des instructions sur l’utilisation du portail Azure, d’Azure PowerShell ou du Kit de développement logiciel (SDK) .NET HDInsight pour approvisionner un cluster Spark personnalisé sur HDInsight.


**Configuration requise :**

Avant de suivre les instructions de cet article, vous devez disposer d’un abonnement Azure. Consultez [Obtenir une version d'évaluation gratuite d'Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quelles sont les différentes options de configuration ?

###Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier un compte de stockage Azure supplémentaire, également associé à votre cluster.

>[AZURE.NOTE]Ne partagez pas un conteneur de stockage d’objets blob sur plusieurs clusters. Ce n’est pas pris en charge.

Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez [Utilisation du stockage d’objets blob Azure avec HDInsight](hdinsight-use-blob-storage.md).

###Metastore

Spark permet de définir un schéma et des tables Hive sur des données brutes. Vous pouvez enregistrer ces schémas et ces métadonnées de table dans des metastores externes. L’utilisation de metastores vous permet de conserver vos métadonnées Hive, afin de ne pas devoir recréer de tables Hive lorsque vous approvisionnez un nouveau cluster. Hive utilise par défaut une base de données intégrée pour stocker ces informations. La base de données incorporée ne peut pas conserver les métadonnées lorsque le cluster est supprimé.

Pour obtenir des instructions sur la création d’une base de données SQL, consultez [Créer votre première base de données SQL Azure](sql-database-get-started.md).

### Personnalisation des clusters

Vous pouvez installer des composants supplémentaires ou personnaliser la configuration de cluster à l’aide de scripts lors de la configuration. Ces scripts sont appelés à l’aide de l’option **action de script**, une option de configuration qui peut être utilisée à partir du portail Azure, de cmdlets HDInsight Windows PowerShell ou du Kit de développement logiciel (SDK) HDInsight .NET. Pour plus d’informations, consultez la page [Personnalisation d’un cluster HDInsight à l’aide d’une d’action de script][hdinsight-customize-cluster].


###Réseau virtuel

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) vous permet de créer un réseau sécurisé et persistant contenant les ressources dont vous avez besoin pour votre solution. Un réseau virtuel vous permet de :

* Connecter différentes ressources de cloud dans un réseau privé (uniquement dans le cloud).

	![diagram of cloud-only configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* connecter vos ressources de cloud à votre réseau de centre de données local (de site à site ou d’un point vers un site) en utilisant un réseau privé virtuel (ou VPN).

	Une configuration de site à site vous permet de connecter plusieurs ressources de votre centre de données vers Azure Virtual Network en utilisant un VPN matériel ou le service RRAS (Routing and Remote Access Service).

	![diagram of site-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	Une configuration de point à site vous permet de connecter une ressource spécifique à Azure Virtual Network en utilisant un VPN logiciel.

	![diagram of point-to-site configuration](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Pour plus d'informations sur les fonctions, les avantages et les capacités d'Azure Virtual Network, consultez la page [Vue d'ensemble d'Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Vous devez créer le réseau Azure Virtual Network avant d’approvisionner un cluster. Pour plus d’informations, consultez [Création d’un réseau virtuel](virtual-networks-create-vnet.md).
>
> Azure HDInsight prend en charge uniquement les réseaux virtuels basés sur l’emplacement et ne fonctionne pas pour le moment avec des réseaux virtuels basés sur des groupes d’affinités.
>
> Il est vivement recommandé de désigner un seul sous-réseau pour un cluster.

##<a id="portal"></a> Utilisation du portail Azure en version préliminaire

Les clusters Spark sur HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage se trouvant dans le même centre de données est nécessaire avant de pouvoir créer un cluster HDInsight. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](hdinsight-hadoop-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la rubrique [Création d’un compte de stockage][azure-create-storageaccount].

**Pour créer un cluster HDInsight en utilisant l’option Création personnalisée**

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Données + analyse**, puis sur **HDInsight**.

    ![Création d’un cluster dans le portail Azure en version préliminaire](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.1.png "Création d’un cluster dans le portail Azure en version préliminaire")

3. Saisissez un **Nom de cluster**, sélectionnez **Spark** comme **Type de cluster**, et à partir du menu déroulant **Système d'exploitation du cluster**, sélectionnez **Windows Server 2012 R2 Datacenter**. Une coche verte apparaît en regard du nom de cluster s'il est disponible.

	![Saisir le nom et le type du cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.2.png "Saisir le nom et le type du cluster")

4. Si vous avez plusieurs abonnements, cliquez sur l’entrée **Abonnement** pour sélectionner l’abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **Groupe de ressources** pour afficher une liste des groupes de ressources existants, puis sélectionnez celui dans lequel créer le cluster. Vous pouvez également cliquer sur **Créer un nouveau**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s’affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **Informations d’identification**, puis saisissez une valeur dans les champs **Nom de connexion au cluster** et **Mot de passe de connexion au cluster**. Si vous souhaitez activer le Bureau à distance sur le nœud de cluster, cliquez sur l’option **Oui** pour **Activer le Bureau à distance**. Sélectionnez une date d’expiration pour l’accès au cluster via le Bureau à distance et fournissez le nom d’utilisateur/mot de passe de l’utilisateur du Bureau à distance. Cliquez sur **Sélectionner** au bas de l’écran pour enregistrer la configuration des informations d’identification.

	![Fournir les informations d’identification du cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.3.png "Fournir les informations d’identification du cluster")

7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster ou en créer une.

	![Panneau Source de données](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.4.png "Fournir la configuration de la source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Lisez ce qui suit pour comprendre les entrées du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l’exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d’accès** si vous souhaitez saisir le **nom de stockage** et la **clé d’accès** d’un compte de stockage existant.

	- **Sélectionner le compte de stockage / Créer un compte** : cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer un compte** pour créer un nouveau compte de stockage. Utilisez le champ qui s’affiche pour saisir le nom du compte de stockage. Une coche verte s’affiche si le nom est disponible.

	- **Choisir le conteneur par défaut** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir n’importe quel nom, mais nous vous conseillons d’utiliser le même nom que le cluster pour pouvoir facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l’emplacement de la source de données par défaut définira également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de la source de données.

8. Cliquez sur **Niveaux de tarification du nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds Worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s’affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.5.png "Spécifier le nombre de nœuds de cluster")

	Cliquez sur **Sélectionner** pour enregistrer la configuration de la tarification du nœud.

9. Cliquez sur **Configuration facultative** pour sélectionner la version de cluster et configurer d’autres paramètres facultatifs (connexion à un **réseau virtuel**, configuration d’un **metastore externe** pour conserver les données Hive et Oozie, etc.), utiliser des actions de script pour personnaliser un cluster et installer des composants personnalisés ou encore utiliser des comptes de stockage supplémentaires avec le cluster.

	* Cliquez sur le menu déroulant **Version HDInsight** et sélectionnez la version à utiliser pour le cluster. Pour plus d’informations, consultez la rubrique [Versions de clusters HDInsight](hdinsight-component-versioning.md).

	* Cliquez sur **Réseau virtuel** pour fournir les informations permettant de configurer le cluster en tant que composant d’un réseau virtuel. Dans le panneau **Réseau virtuel**, cliquez sur **Réseau virtuel**, puis sur le réseau à utiliser. En procédant de même, sélectionnez un **sous-réseau** pour le réseau, puis cliquez sur **Sélectionner** pour enregistrer la configuration du réseau virtuel.

		![Panneau Réseau virtuel](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.6.png "Spécifier les détails du réseau virtuel")

	* Cliquez sur **Metastores externes** pour spécifier la base de données SQL à utiliser pour enregistrer les métadonnées Hive et Oozie associées au cluster.

		![Panneau de metastores personnalisés](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.7.png "Spécification des metastores externes")

		Pour **Utiliser une base de données SQL existante pour les métadonnées Hive**, cliquez sur **Oui**, sélectionnez une base de données SQL, puis indiquez le nom d’utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **Sélectionner** jusqu’à ce que vous reveniez au panneau **Configuration facultative**.

		>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

	* Cliquez sur **Actions de script** si vous souhaitez utiliser un script personnalisé pour personnaliser un cluster au moment de sa création. Pour plus d’informations sur les actions de script, consultez l’article [Personnaliser des clusters HDInsight à l’aide d’une d’action de script](hdinsight-hadoop-customize-cluster.md). Dans le panneau Actions de script, saisissez les informations requises, comme illustré dans la capture d’écran ci-dessous.

		![Panneau Action de script](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.8.png "Spécifier l'action de script")

		Cliquez sur **Sélectionner** pour enregistrer les modifications apportées à la configuration d’action de script.

	* Cliquez sur **Clés de stockage Azure** pour spécifier les comptes de stockage supplémentaires à associer au cluster. Dans le panneau **Clés de stockage Azure**, cliquez sur **Ajouter une clé de stockage**, puis sélectionnez un compte de stockage existant ou créez-en un.

		![Panneau Stockage supplémentaire](./media/hdinsight-apache-spark-provision-clusters/HDI.CreateCluster.9.png "Spécifier des comptes de stockage supplémentaires")

		Cliquez sur **Sélectionner** jusqu’à ce que vous reveniez au panneau **Nouveau cluster HDInsight **.

10. Dans le panneau **Nouveau cluster HDInsight**, assurez-vous que l’option **Épingler au tableau d’accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d’accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur d’approvisionnement sur le tableau d’accueil](./media/hdinsight-apache-spark-provision-clusters/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-apache-spark-provision-clusters/provisioned.png) |

	> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour suivre la progression du processus d’approvisionnement.

11. Une fois l’approvisionnement terminé, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.

	![Panneau Cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")

	Lisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans les sections **Bases** et **Liens rapides** :

	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d’accéder aux informations de configuration détaillées du cluster.

	* **Tableau de bord** et **URL** : permettent d’accéder au tableau de bord du cluster, portail web permettant d’exécuter des tâches sur le cluster.

	* **Bureau à distance** : permet d’activer/de désactiver le Bureau à distance sur les nœuds de cluster.

	* **Mise à l’échelle du cluster** : permet de modifier le nombre de nœuds de travail pour ce cluster.

	* **Supprimer** : permet de supprimer le cluster HDInsight.

	* **Démarrage rapide** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-apache-spark-provision-clusters/quickstart.png)) : affiche des informations qui vous aideront à prendre en main HDInsight.

	* **Utilisateurs** (![icône d’utilisateurs](./media/hdinsight-apache-spark-provision-clusters/users.png)) : permet de définir des autorisations pour la _gestion via le portail_ de ce cluster pour d’autres utilisateurs dans votre abonnement Azure.

		> [AZURE.IMPORTANT]Ce paramètre affecte _uniquement_ les autorisations d’accès à ce cluster dans le portail Azure en version préliminaire et n’a aucun effet sur les personnes autorisées à se connecter ou à soumettre des tâches au cluster HDInsight.

	* **Balises** (![icône de balise](./media/hdinsight-apache-spark-provision-clusters/tags.png)) : permettent de spécifier des paires clé/valeur pour définir une classification personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés à un projet spécifique.

	* **Tableau de bord du cluster** : permet d’ouvrir le panneau Tableau de bord du cluster, d’où vous pouvez lancer le tableau de bord du cluster lui-même ou les blocs-notes Zeppelin et Jupyter.


##<a id="powershell"></a> Utilisation d’Azure PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la configuration d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant Azure PowerShell :

- Création d’un groupe de ressources Azure
- Création d’un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight


		# Use the new Azure Resource Manager mode
		Switch-AzureMode AzureResourceManager

		###########################################
		# Create required items, if none exist
		###########################################

		# Sign in
		Add-AzureAccount

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Register your subscription to use HDInsight
		Register-AzureProvider -ProviderNamespace "Microsoft.HDInsight" -Force

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Spark


	![HDI.CLI.Provision](./media/hdinsight-apache-spark-provision-clusters/HDI.ps.provision.png)


## Utilisation du Kit de développement logiciel (SDK) .NET HDInsight sur ARM
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Suivez les instructions ci-dessous pour créer une application de console Visual Studio et collez le code pour la création d’un cluster.

**Pour créer une application console Visual Studio**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Cliquez sur **OK** pour créer le projet.

5. Dans le menu **Outils**, cliquez sur **Gestionnaire de package NuGet**, puis sur **Gérer les packages NuGet pour la solution**. Dans la zone de texte de recherche de la boîte de dialogue, recherchez **HDInsight**. Parmi les résultats qui s’affichent, installez les éléments suivants :

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Recherchez « Azure Authentication » et, parmi les résultats qui s’affichent, installez **Microsoft.Azure.Common.Authentication**.

6. Dans l’Explorateur de solutions, double-cliquez sur **Program.cs** pour l’ouvrir, collez le code suivant et fournissez des valeurs pour les variables :


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;


		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//should be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Spark;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster provisioning");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Appuyez sur **F5** pour exécuter l'application. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. Vous êtes invité à saisir les informations d’identification de votre compte Azure. La création d'un cluster HDInsight peut prendre plusieurs minutes.


##<a id="nextsteps"></a>Étapes suivantes

* Consultez [Effectuer une analyse interactive des données à l’aide de Spark sur HDInsight avec des outils décisionnels](hdinsight-apache-spark-use-bi-tools.md) pour découvrir comment utiliser Apache Spark dans HDInsight avec des outils décisionnels tels que Power BI et Tableau.
* Consultez [Utiliser Spark sur HDInsight pour créer des applications d’apprentissage automatique](hdinsight-apache-spark-ipython-notebook-machine-learning.md) pour découvrir comment créer des applications d’apprentissage automatique à l’aide d’Apache Spark sur HDInsight.
* Consultez [Utiliser Spark sur HDInsight pour créer des applications de diffusion en continu en temps réel](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) pour découvrir comment créer des applications de diffusion en continu à l’aide d’Apache Spark sur HDInsight.
* Consultez [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](hdinsight-apache-spark-resource-manager.md) pour découvrir comment utiliser le gestionnaire de ressources pour gérer les ressources allouées au cluster Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/


[azure-preview-portal]: https://portal.azure.com

[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!---HONumber=Oct15_HO3-->