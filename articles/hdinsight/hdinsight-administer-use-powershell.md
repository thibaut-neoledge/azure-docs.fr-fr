<properties
	pageTitle="Gestion des clusters Hadoop dans HDInsight avec PowerShell | Microsoft Azure"
	description="Découvrez comment effectuer des tâches d'administration pour les clusters Hadoop dans HDInsight au moyen d'Azure PowerShell."
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
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight au moyen d’Azure PowerShell

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article vous explique comment gérer les clusters Hadoop dans Azure HDInsight en utilisant la console locale Azure PowerShell par le biais de Windows PowerShell. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].



**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Installer Azure PowerShell 1.0 et versions ultérieures

Vous devez d’abord désinstaller les versions 0.9x.

Pour connaître la version de PowerShell installée :

	Get-Module *azure*
	
Pour désinstaller l’ancienne version, exécutez Programmes et fonctionnalités dans le Panneau de configuration.

Il existe deux options principales pour l’installation d’Azure PowerShell.

- [PowerShell Gallery](https://www.powershellgallery.com/). Exécutez les commandes suivantes à partir de PowerShell ISE avec élévation de privilèges ou de la console Windows PowerShell avec élévation de privilèges :

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Pour plus d’informations, consultez [PowerShell Gallery](https://www.powershellgallery.com/).

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). Si vous disposez d’Azure PowerShell 0.9.x, vous êtes invité à désinstaller cette version. Si vous avez installé des modules Azure PowerShell à partir de PowerShell Gallery, vous devez les supprimer avant l’installation pour garantir un environnement Azure PowerShell cohérent. Pour obtenir des instructions, consultez [Installer Azure PowerShell 1.0 via WebPI](https://azure.microsoft.com/blog/azps-1-0/).

WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu. Si vous êtes familiarisé avec l’installation à partir de PowerShell Gallery, il s’agit du premier canal pour bénéficier des dernières nouveautés d’Azure PowerShell.

##Créer des clusters

Les clusters HDInsight nécessitent un groupe de ressources Azure et un conteneur d'objets blob dans un compte de stockage Azure :

- Le groupe de ressources Azure est un conteneur logique pour les ressources Azure. Le groupe de ressources Azure et le cluster HDInsight ne doivent pas nécessairement se trouver au même emplacement. Pour plus d'informations, consultez [Utilisation d'Azure PowerShell avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).
- HDInsight utilise le conteneur d’objets blob d’un compte de stockage Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Le compte de stockage par défaut doit se situer au même emplacement que le cluster HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Pour se connecter à Azure**

	Login-AzureRmAccount
	Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
	Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

**Select-AzureRMSubscription** est appelé si vous avez plusieurs abonnements Azure.
	
**Pour créer un groupe de ressources**

	New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**Pour créer un compte Azure Storage**

	New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
	
	Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [liste de centre de données](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Pour plus d’informations sur la création d’un compte de stockage Azure au moyen du portail Azure, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

Si vous disposez déjà d’un compte de stockage, mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

Pour en savoir plus sur l’obtention d’informations au moyen du portail Azure, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de la rubrique [Création, gestion ou suppression d’un compte de stockage](../storage/storage-create-storage-account.md).

**Pour créer un conteneur de stockage Azure**

Azure PowerShell n'est pas en mesure de créer un conteneur de stockage d'objets blob pendant la création de HDInsight. Vous pouvez en créer un à l’aide du script suivant :

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<Azure Storage Account Name>"
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Pour créer un cluster**

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<Azure Storage Account Name>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Énumérer les clusters
Utilisez la commande suivante pour afficher la liste de tous les clusters de l’abonnement actif :

	Get-AzureRmHDInsightCluster

##Afficher le cluster

Utilisez la commande suivante pour afficher les détails d’un cluster spécifique dans l’abonnement actif :

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Suppression des clusters
Utilisez les commandes suivantes pour supprimer un cluster :

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

>[AZURE.NOTE] Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés. Voir [Se familiariser avec l’interface du portail de cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Impact de la modification du nombre de nœuds de données pour chaque type de cluster pris en charge par HDInsight :

- Hadoop

	Vous pouvez augmenter de façon continue le nombre de nœuds de travail dans un cluster Hadoop exécuté sans affecter aucune tâche en attente ou en cours. De nouvelles tâches peuvent également être soumises lorsque l'opération est en cours. Les défaillances dans l'opération de mise à l'échelle sont correctement gérées de sorte que le cluster reste toujours fonctionnel.

	Lorsqu’un cluster Hadoop est diminué par la réduction du nombre de nœuds de données, certains services du cluster sont redémarrés. Pour cette raison, toutes les tâches en cours ou en attente échouent lors de la réalisation de l'opération de mise à l'échelle. Toutefois, vous pouvez soumettre à nouveau les tâches une fois l'opération terminée.

- HBase

	Vous pouvez ajouter ou supprimer des nœuds en continu dans votre cluster HBase lorsque celui-ci s’exécute. Les serveurs régionaux sont équilibrés automatiquement quelques minutes après la fin de l’opération de mise à l’échelle. Cependant, vous pouvez équilibrer manuellement des serveurs régionaux en vous connectant au nœud principal du cluster et en exécutant les commandes suivantes à partir d’une fenêtre d’invite de commandes :

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Pour plus d’informations sur l’utilisation de l’interpréteur de commandes HBase, voir
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

Pour modifier la taille du cluster Hadoop à l’aide d’Azure PowerShell, exécutez la commande suivante depuis un ordinateur client :

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
	

##Octroyer/Révoquer l’accès

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour révoquer :

	Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Pour octroyer :

	$clusterName = "<HDInsight Cluster Name>"

	# Credential option 1
	$hadoopUserName = "admin"
	$hadoopUserPassword = "<Enter the Password>"
	$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
	$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

	# Credential option 2
	#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
	
	Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

Vous pouvez également le faire via le portail Azure. Consultez [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal].

##Mettre à jour les informations d’identification de l’utilisateur HTTP

Il s’agit de la même procédure que [l’octroi et la révocation de l’accès HTTP](#grant/revoke-access). Si l’accès HTTP a été octroyé au cluster, vous devez tout d’abord le révoquer. Octroyez ensuite l’accès avec les informations d’identification de l’utilisateur HTTP.


##Trouvez le compte de stockage par défaut

Le script Powershell suivant montre comment obtenir le nom de compte de stockage par défaut et la clé de compte de stockage par défaut pour un cluster.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Trouvez le groupe de ressources

En mode ARM, chaque cluster HDInsight appartient à un groupe de ressources Azure. Pour rechercher le groupe de ressources :

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Soumettre les travaux

**Pour envoyer des tâches MapReduce**

Consultez [Exécution des exemples Hadoop MapReduce dans HDInsight basé sur Windows](hdinsight-run-samples.md)

**Pour envoyer des tâches Hive**

Consultez [Exécution de requêtes Hive avec PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Pour envoyer des tâches Pig**

Consultez [Exécution de tâches Pig avec PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Pour envoyer des tâches Sqoop**

Consultez l'article [Utilisation de Sqoop avec HDInsight](hdinsight-use-sqoop.md).

**Pour envoyer des tâches Oozie**

Consultez [Utilisation d’Oozie avec Hadoop pour définir et exécuter un workflow dans HDInsight](hdinsight-use-oozie.md).

##Téléchargement de données vers le stockage d'objets blob Azure
Consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].


## Voir aussi
* [Documentation de référence des applets de commande HDInsight][hdinsight-powershell-reference]
* [Administration de HDInsight à l’aide du portail Azure][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide de l’interface de ligne de commande][hdinsight-admin-cli]
* [Création de clusters HDInsight][hdinsight-provision]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=AcomDC_0218_2016-->