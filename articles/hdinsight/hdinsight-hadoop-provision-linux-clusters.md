<properties
   	pageTitle="Approvisionnement de clusters Hadoop Linux dans HDInsight | Microsoft Azure"
	description="Découvrez comment approvisionner des clusters Hadoop Linux pour HDInsight à l'aide du portail de gestion, de la ligne de commande et du Kit de développement logiciel (SDK) .NET."
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
	ms.date="08/07/2015"
	ms.author="nitinme"/>


#Approvisionnement de clusters Hadoop Linux dans HDInsight à l’aide d’options personnalisées (version préliminaire)

Dans cet article, vous allez découvrir les différentes méthodes pour approvisionner de façon personnalisée un cluster Hadoop Linux sur Azure HDInsight, en utilisant le portail Azure, Azure PowerShell, l’interface de ligne de commande Azure ou le Kit de développement logiciel (SDK) .NET HDInsight.

## Qu’est-ce qu’un cluster HDInsight ?

Vous êtes-vous déjà demandé pourquoi nous évoquons les clusters à chaque fois que nous parlons d’Hadoop ou des données volumineuses ? C'est parce qu'Hadoop permet le traitement distribué de grandes quantités de données, diffusées entres les différents nœuds d'un cluster. Le cluster dispose d’une architecture maître/esclave composée d’un nœud maître (également nommé nœud principal ou nœud de nom) et de plusieurs nœuds de travail (également nommés nœuds de données). Pour plus d’informations, consultez <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>.

![Cluster HDInsight][img-hdi-cluster]


Un cluster HDInsight résume les détails de l'implémentation Hadoop pour que vous n'ayez pas à vous demander comment communiquer avec différents nœuds d'un cluster. Lorsque vous approvisionnez un cluster HDInsight, vous approvisionnez les ressources de calcul Azure contenant Hadoop et ses applications associées. Pour plus d'informations, consultez la rubrique [Introduction à Hadoop dans HDInsight](hdinsight-hadoop-introduction.md). Les données destinées à l’attrition sont stockées dans un stockage d’objets blob Azure. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).


Cet article fournit des instructions sur les différentes méthode d'approvisionnement de cluster. Si vous recherchez une approche de démarrage rapide pour approvisionner un cluster, consultez la rubrique [Prise en main d’Azure HDInsight sur Linux](../hdinsight-hadoop-linux-get-started.md).

**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Clés Secure Shell (SSH)**. Si vous souhaitez gérer à distance un cluster Linux à l'aide de SSH avec une clé plutôt qu'un mot de passe. L'utilisation d'une clé constitue la méthode recommandée, car elle est plus sécurisée. Pour savoir comment générer des clés SSH, consultez les articles suivants :
	-  Depuis un ordinateur Linux : [Utilisation de HDInsight Linux (Hadoop) depuis Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	-  Depuis un ordinateur Windows : [Utilisation de SSH avec HDInsight Linux (Hadoop) depuis Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a id="configuration"></a>Options de configuration

### Clusters sur Linux

HDInsight offre la possibilité d'approvisionner des clusters Linux sur Azure. Approvisionnez un cluster Linux si vous maîtrisez Linux ou Unix, en effectuant une migration à partir d’une solution Hadoop Linux existante, ou si vous souhaitez intégrer facilement des composants de l’écosystème Hadoop conçus pour Linux. Pour en savoir plus sur Azure HDInsight sur Linux, consultez la page [Présentation d’Hadoop sur HDInsight](hdinsight-hadoop-introduction.md).


### Stockage supplémentaire

Durant la configuration, vous devez spécifier un compte de stockage d’objet blob Azure, ainsi qu’un conteneur par défaut. Ceci est utilisé par le cluster comme emplacement de stockage par défaut. Vous pouvez éventuellement spécifier des objets blob supplémentaires qui seront également associés à votre cluster.


Pour plus d’informations sur l’utilisation des magasins d’objets blob secondaires, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md).


### Metastore

Le metastore contient des informations concernant les tables Hive, les partitions, les schémas, les colonnes, etc. Ces informations sont utilisées par Hive pour trouver où sont stockées les données dans Hadoop HDFS (Distributed File System) ou dans le stockage d’objets blob Azure pour HDInsight. Hive utilise par défaut une base de données intégrée pour stocker ces informations.

Lors de l’approvisionnement d’un cluster HDInsight, vous pouvez indiquer une base de données SQL qui contiendra le metastore pour Hive. Ceci permet de conserver les informations des métadonnées lorsque vous supprimez un cluster, car elles sont alors stockées de manière externe dans la base de données SQL. Pour obtenir des instructions sur la création d'une base de données SQL, consultez [Créer votre première base de données SQL Azure](sql-database-get-started.md).


## <a id="options"></a> Options d’approvisionnement des clusters HDInsight Linux

Vous pouvez approvisionner un cluster HDInsight Hadoop Linux depuis un ordinateur Linux ou Windows. Le tableau suivant fournit des informations sur les options d'approvisionnement disponibles depuis les différents systèmes d'exploitation, ainsi que des liens vers des instructions pour chacun d'entre eux.

Approvisionnement de clusters Linux à partir d’un ordinateur doté de ce système d’exploitation| Utilisation du portail Azure | Utilisation de Microsoft Azure PowerShell | Utilisation du Kit de développement logiciel (SDK) .NET
-----------------| ------------------------| -------------------| ---------- | ---------
Linux| Cliquez [ici](#portal) | Non applicable | Non applicable
Windows | Cliquez [ici](#portal) | Cliquez [ici](#powershell) | Cliquez [ici](#sdk)

### <a id="portal"></a>Utilisation du portail Azure

Les clusters HDInsight utilisent un conteneur de stockage d’objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage doit se trouver dans le même centre de données pour que vous puissiez créer un cluster HDInsight. Pour plus d’informations, consultez la rubrique [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md). Pour plus d’informations sur la création d’un compte Azure Storage, consultez la rubrique [Création d’un compte de stockage](../storage-create-storage-account.md).


> [AZURE.NOTE]Actuellement, seules les régions **Asie du Sud-Est**, **Europe du Nord**, **Est des États-Unis** et **Sud du centre des États-Unis** peuvent héberger des clusters HDInsight Linux.

**Création d'un cluster HDInsight**

1. Connectez-vous à la [version préliminaire du portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, sur **Analyse de données**, puis sur **HDInsight**.

    ![Création d'un nouveau cluster dans la version préliminaire du portail Azure](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.1.png "Création d'un nouveau cluster dans la version préliminaire du portail Azure")

3. Saisissez un **Nom de Cluster**, sélectionnez **Hadoop** comme **Type de cluster**, et à partir du menu déroulant **Système d'exploitation du cluster**, sélectionnez **Ubuntu**. Une coche verte apparaît en regard du nom de cluster s'il est disponible.

	![Saisissez le nom et le type de cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.2.png "Saisissez le nom et le type de cluster")

4. Si vous avez plusieurs abonnements, cliquez sur l'élément **Abonnement** pour sélectionner l'abonnement Azure qui sera utilisé pour le cluster.

5. Cliquez sur **Groupe de ressources** pour afficher une liste des groupes de ressources existantes, puis sélectionnez celui dans lequel vous devez créer le cluster. Vous pouvez également cliquer sur **Créer nouveau**, puis saisir le nom du nouveau groupe de ressources. Une coche verte s'affiche pour indiquer si le nouveau nom de groupe est disponible.

	> [AZURE.NOTE]Cette entrée ira par défaut dans l'un des groupes de ressources existants, si l'un d'eux est disponible.

6. Cliquez sur **Informations d'identification**, puis saisissez un mot de passe pour l'utilisateur admin. Vous devez également saisir un **nom d'utilisateur SSH** et un **MOT DE PASSE** ou **CLÉ PUBLIQUE**, qui seront utilisés pour authentifier l'utilisateur SSH. L'utilisation d'une clé publique est l'approche recommandée. Cliquez sur **Sélectionner** en bas pour enregistrer la configuration des informations d'identification.

	![Fournissez les informations d'identification du cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.3.png "Fournissez les informations d'identification du cluster")

	Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Cliquez sur **Source de données** pour choisir une source de données existante pour le cluster, ou en créer une nouvelle.

	![Panneau Source de données](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.4.png "Fournissez la configuration de source de données")

	Actuellement, vous pouvez sélectionner un compte de stockage Azure comme source de données pour un cluster HDInsight. Utilisez ce qui suit pour comprendre les saisies du panneau **Source de données**.

	- **Méthode de sélection** : définissez cette propriété sur la valeur **De tous les abonnements** pour permettre l'exploration des comptes de stockage de tous vos abonnements. Affectez-lui la valeur **Clé d'accès** si vous souhaitez saisir le **nom de stockage** et la **clé d'accès** d'un compte de stockage existant.

	- **Sélectionner Compte de stockage/Créer** : cliquez sur **Sélectionner le compte de stockage** pour parcourir et sélectionner un compte de stockage existant à associer au cluster. Vous pouvez également cliquer sur **Créer** pour créer un nouveau compte de stockage. Utilisez le champ qui s'affiche pour saisir le nom du compte de stockage. Une coche verte s'affiche si le nom est disponible.

	- **Choisir le conteneur par défaut** : utilisez cette option pour saisir le nom du conteneur par défaut à utiliser pour le cluster. Vous pouvez saisir ensuite n'importe quel nom, mais nous vous conseillons d'utiliser le même nom que celui du cluster pour que vous puissiez facilement reconnaître le conteneur utilisé pour ce cluster spécifique.

	- **Emplacement** : zone géographique dans laquelle le compte de stockage se trouve ou dans laquelle il sera créé.

		> [AZURE.IMPORTANT]La sélection de l'emplacement de la source de données par défaut définit également l'emplacement du cluster HDInsight. Le cluster et la source de données par défaut doit se trouver dans la même zone.

	Cliquez sur **Sélectionner** pour enregistrer la configuration de source de données.

8. Cliquez sur **Niveaux de tarification du nœud** pour afficher des informations sur les nœuds qui seront créés pour ce cluster. Définissez le nombre de nœuds Worker dont vous avez besoin pour le cluster. Le coût estimé du cluster s'affiche dans le panneau.

	![Panneau Niveaux de tarification du nœud](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.5.png "Spécification du nombre de nœuds de cluster")

	Cliquez sur **Sélectionner** pour enregistrer la configuration de tarif de nœud.

9. Cliquez sur **Configuration facultative** pour sélectionner la version de cluster et configurer d'autres paramètres facultatifs, par exemple ajouter un **Réseau virtuel**, définir un **Metastore externe** pour conserver les données Hive et Oozie, utilisez des Actions de Script pour personnaliser un cluster et installer des composants personnalisés ou utiliser des comptes de stockage supplémentaires avec le cluster.

	* Cliquez sur le menu déroulant **Version HDInsight** et sélectionnez la version que vous souhaitez utiliser pour le cluster. Pour plus d'informations, consultez la rubrique [Versions de clusters HDInsight](hdinsight-component-versioning.md).

	* Cliquez sur **Metastores externe** pour spécifier la base de données SQL que vous souhaitez utiliser pour enregistrer les métadonnées Hive et Oozie associée au cluster.

		![Panneau de metastores personnalisés](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.7.png "Spécification des metastores externes")

		Pour les métadonnées **utiliser une base de données SQL existante pour Hive**, cliquez sur **Oui**, sélectionnez une base de données SQL, puis indiquez le nom d'utilisateur/mot de passe pour la base de données. Répétez ces étapes si vous souhaitez **utiliser une base de données SQL existante pour les métadonnées Oozie**. Cliquez sur **Sélectionner** jusqu'à ce que vous reveniez au panneau **Configuration facultative**.

		>[AZURE.NOTE]La base de données SQL Azure utilisée pour le metastore doit autoriser la connectivité aux autres services Azure, y compris Azure HDInsight. Sur le côté droit du tableau de bord de la base de données Azure SQL, cliquez sur le nom du serveur. Il s'agit du serveur sur lequel l'instance de base de données SQL est exécutée. Une fois sur l’écran du serveur, cliquez sur **Configurer**, puis pour **Services Azure**, cliquez sur **Oui**, puis sur **Enregistrer**.

	* Cliquez sur **clés de stockage Azure** pour spécifier les comptes de stockage supplémentaire à associer au cluster. Dans le panneau **Clés de stockage Azure**, cliquez sur **Ajouter une clé de stockage**, puis sélectionnez un compte de stockage existant ou créez un nouveau compte.

		![Panneau de stockage supplémentaire](./media/hdinsight-hadoop-provision-linux-clusters/HDI.CreateCluster.9.png "Spécifier des comptes de stockage supplémentaires")

		Cliquez sur **Sélectionnez** jusqu'à revenir au panneau **Nouveau cluster HDInsight **.

10. Dans le panneau **Nouveau cluster HDInsight**, assurez-vous que l'option **Épingler au tableau d'accueil** est sélectionnée, puis cliquez sur **Créer**. Le cluster est créé et la vignette correspondante ajoutée au tableau d'accueil de votre portail Azure. L'icône indique que le cluster est en cours de configuration et sera modifiée pour représenter l'icône HDInsight une fois la configuration terminée.

	| Pendant l’approvisionnement | Approvisionnement terminé |
	| ------------------ | --------------------- |
	| ![Indicateur de configuration sur le tableau d'accueil](./media/hdinsight-hadoop-provision-linux-clusters/provisioning.png) | ![Vignette de cluster approvisionné](./media/hdinsight-hadoop-provision-linux-clusters/provisioned.png) |

	> [AZURE.NOTE]La création du cluster prend un certain temps (en règle générale, environ 15 minutes). Utilisez la vignette du tableau d'accueil ou l'entrée **Notifications** à gauche de la page pour suivre la progression du processus d'approvisionnement.

11. Une fois la configuration terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.

	![Panneau Cluster](./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.Blade.png "Propriétés du cluster")

	Utilisez ce qui suit pour comprendre les icônes affichées en haut de ce panneau, et dans la section dans la section **Essentials** :

	* **Paramètres** et **Tous les paramètres** : affichent le panneau **Paramètres** du cluster, ce qui vous permet d'accéder aux informations de configuration détaillées du cluster.

	* **Tableau de bord**, **Tableau de bord du cluster** et **URL**: il s'agit de toutes les façons d'accéder au tableau de bord de cluster, qui est un portail web permettant d'exécuter des tâches sur le cluster.

	* **Secure Shell** : informations nécessaires à l'accès au cluster à l'aide de SSH.

	* **Supprimer** : permet de supprimer le cluster HDInsight

	* **Quickstart** (![icône nuage et foudre = démarrage rapide](./media/hdinsight-hadoop-provision-linux-clusters/quickstart.png)) : affiche des informations qui vous aideront à démarrer l'utilisation de HDInsight.

	* **Utilisateurs** (![icône d'utilisateurs](./media/hdinsight-hadoop-provision-linux-clusters/users.png)) : vous permet de définir des autorisations pour la _gestion de portail_ de ce cluster pour d'autres utilisateurs sur votre abonnement Azure.

		> [AZURE.IMPORTANT]Il affecte _uniquement_ les autorisations d'accès à ce cluster dans la version préliminaire du portail Azure et n'a aucun effet sur les personnes autorisées à se connecter au cluster HDInsight, ou à soumettre des travaux.

	* **Balises** (![icône de balise](./media/hdinsight-hadoop-provision-linux-clusters/tags.png)) : les balises vous permettent de définir des paires clé/valeur pour définir une taxonomie personnalisée de vos services cloud. Vous pouvez par exemple créer une clé nommée __projet__, puis utiliser une valeur commune pour tous les services associés au projet spécifique.



### <a id="powershell"></a> Utilisation d’Azure PowerShell
Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cette section fournit des instructions sur la configuration d’un cluster HDInsight à l’aide d’Azure PowerShell. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des cmdlets HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant Azure PowerShell :

- Création d'un groupe de ressources Azure
- Création d’un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight

Les deux paramètres les plus importants que vous devez définir pour approvisionner les clusters Linux sont ceux où vous spécifiez le type de système d’exploitation et les détails de l’utilisateur SSH :

- Vérifiez que le paramètre **-OSType** est défini sur **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les clusters, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez le mot de passe utilisateur SSH et la clé publique SSH, la clé sera ignorée. Si vous souhaitez utiliser la clé SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité. Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :

	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


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
			$sshCredentials = Get-Credential

			# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
			$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

			# Create a new HDInsight cluster
			New-AzureHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials


>[AZURE.NOTE]Les valeurs que vous spécifiez pour **$clusterCredentials** sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster. Les valeurs que vous spécifiez pour **$sshCredentials** sont utilisées pour créer le compte d’utilisateur SSH pour le cluster. Vous utilisez ce compte pour démarrer une session SSH à distance sur le cluster et pour exécuter des tâches. Si vous utilisez l’option Création rapide à partir du portail Azure pour approvisionner un cluster, le nom d’utilisateur Hadoop par défaut est « admin » et le nom d’utilisateur SSH par défaut est « hdiuser ».

L’approvisionnement du cluster peut prendre plusieurs minutes.

![HDI.CLI.Provision][image-hdi-ps-provision]

###<a id="sdk"></a> Utilisation du Kit de développement logiciel (SDK) HDInsight .NET
Le Kit de développement logiciel (SDK) .NET HDInsight fournit des bibliothèques clientes .NET facilitant l'utilisation de HDInsight à partir d'une application .NET. Suivez les instructions ci-dessous pour créer une application de console Visual Studio et collez le code pour la création d'un cluster.

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

5. Dans le menu **Outils**, cliquez sur **gestionnaire de packages NuGet**, puis sur **Manage NuGet Packages for Solution**. Dans la zone de texte de recherche de la boîte de dialogue, recherchez **HDInsight**. À partir des résultats qui s'affichent, vous devez installer les éléments suivants :

	 * Microsoft.Azure.Management.HDInsight
	 * Microsoft.Azure.Management.HDInsight.Job

	Rechercher l'authentification Azure et, parmi les résultats qui s'affichent, installer **Microsoft.Azure.Common.Authentication**.

6. Dans l'Explorateur de solutions, double-cliquez sur **Program.cs** pour l'ouvrir, coller le code suivant et fournir des valeurs pour les variables :


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
		        private const OSType NewClusterOsType = OSType.Linux;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";
				private const string NewClusterSshUserName = "sshuser";
        		private const string NewClusterSshPublicKey = "<ssh public key>";

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
		                OSType = NewClusterOsType,
						SshUserName = NewClusterSshUserName,
                		SshPublicKey = NewClusterSshPublicKey
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

7. Appuyez sur **F5** pour exécuter l'application. Une fenêtre de console doit s'ouvrir et afficher l'état de l'application. Vous êtes invité à saisir les informations d'identification de votre compte Azure. La création d'un cluster HDInsight peut prendre plusieurs minutes.



##<a id="nextsteps"></a> Étapes suivantes
Cet article vous a présenté différentes méthodes pour configurer un cluster HDInsight Hadoop sur Linux. Pour en savoir plus, consultez les articles suivants :

- [Utilisation de HDInsight sur Linux](hdinsight-hadoop-linux-information.md) : familiarisez-vous avec l’utilisation d’un cluster HDInsight sur Linux.
- [Gestion des clusters HDInsight avec Ambari](hdinsight-hadoop-manage-ambari.md) : découvrez comment gérer et contrôler votre Hadoop Linux sur le cluster HDInsight à l’aide d’Ambari Web ou de l’API REST d’Ambari.
- [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md) : découvrez les différentes façons d’exécuter des tâches MapReduce sur un cluster.
- [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md) : découvrez les différentes façons d’exécuter une requête Hive sur un cluster.
- [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md) : découvrez les différentes façons d’exécuter une tâche Pig sur un cluster.
- [Utilisation du stockage d’objets blob Azure avec HDInsight](../hdinsight-use-blob-storage.md) : découvrez comment HDInsight utilise le stockage d’objets blob Azure pour stocker des données pour les clusters HDInsight.
- [Téléchargement de données vers HDInsight](hdinsight-upload-data.md) : découvrez comment utiliser des données stockées dans un stockage d’objets blob Azure pour un cluster HDInsight.


[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png




[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Énumération et affichage des clusters"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Utilisation de Sqoop avec HDInsight"

<!---HONumber=August15_HO8-->