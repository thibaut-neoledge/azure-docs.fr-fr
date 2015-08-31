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
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight au moyen d’Azure PowerShell

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article vous explique comment gérer les clusters Hadoop dans Azure HDInsight en utilisant la console locale Azure PowerShell par le biais de Windows PowerShell. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].



**Configuration requise**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et utilisation d'Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	> [AZURE.NOTE]Les scripts PowerShell fournis dans cet article utilisent le mode de gestionnaire de ressources Azure. Pour que les exemples fonctionnent correctement, téléchargez la dernière version Azure PowerShell à l’aide de Microsoft Web Platform Installer.

##Approvisionnement de clusters HDInsight

Les clusters HDInsight nécessitent un groupe de ressources Azure et un conteneur d’objets blob sur un compte de stockage Azure :

- Le groupe de ressources Azure est un conteneur logique pour les ressources Azure. Le groupe de ressources Azure et le cluster HDInsight ne doivent pas nécessairement se trouver au même emplacement. Pour en savoir plus, voir [Utilisation d’Azure PowerShell avec le Gestionnaire de ressources Azure](powershell-azure-resource-manager.md).
- HDInsight utilise le conteneur d’objets blob d’un compte de stockage Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. Le compte de stockage par défaut doit se situer au même emplacement que le cluster HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Pour créer un groupe de ressources Azure**

1. Assurez-vous que vous êtes en mode ressource Azure :

		Switch-AzureMode -Name AzureResourceManager

2. Connectez-vous à votre compte Azure et sélectionnez un abonnement (au cas où vous en ayez plusieurs).

		Add-AzureAccount
		Select-AzureSubscription

3. Créez un groupe de ressources :

	New-AzureResourceGroup -name <AzureResourceGroupName> -Location <AzureDataCente> # Par exemple, « Ouest des États-Unis »┬

	[AZURE.INCLUDE [liste de centre de données](../../includes/hdinsight-pricing-data-centers-clusters.md)]

**Pour créer un compte Azure Storage**

	New-AzureStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

	For a full list of the storage account types, see [https://msdn.microsoft.com/fr-fr/library/azure/hh264518.aspx](https://msdn.microsoft.com/fr-fr/library/azure/hh264518.aspx).


Pour plus d’informations sur la création d’un compte Azure Storage au moyen du portail Azure en version préliminaire, consultez la rubrique [Création, gestion ou suppression d’un compte de stockage](storage-create-storage-account.md).

Si vous disposez déjà d’un compte de stockage, mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

Pour plus d’informations sur l’obtention d’informations au moyen du portail Azure en version préliminaire, consultez la section « Affichage, copie et régénération des clés d’accès de stockage » de la rubrique [Création, gestion ou suppression d’un compte de stockage](storage-create-storage-account.md).

**Pour créer un conteneur de stockage Azure**

Azure PowerShell n’est pas en mesure de créer un conteneur de stockage d’objets blob pendant l’approvisionnement de HDInsight. Vous pouvez en créer un à l’aide du script suivant :

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Pour configurer un cluster**

Une fois que le compte de stockage et le conteneur d’objets blob sont prêts, vous êtes prêt à créer un cluster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Liste des détails du cluster
Utilisez la commande suivante pour afficher la liste de tous les clusters de l’abonnement actif :

	Get-AzureHDInsightCluster

Utilisez la commande suivante pour afficher les détails d’un cluster spécifique dans l’abonnement actif :

	Get-AzureHDInsightCluster -ResourceGroupName <ResouceGroupName> -ClusterName <ClusterName>

##Suppression des clusters
Utilisez les commandes suivantes pour supprimer un cluster :

	Remove-AzureHDInsightCluster -Name <ClusterName>



##Octroi/révocation de l'accès aux services HTTP

Les clusters HDInsight disposent des services web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour cela, vous pouvez par exemple utiliser la commande suivante :

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Dans l’exemple, <i>hdiv2</i> correspond au nom d’un cluster HDInsight.

>[AZURE.NOTE]En octroyant/révoquant l’accès, vous réinitialisez le nom d’utilisateur et le mot de passe du cluster.

Vous pouvez également le faire via le portail Azure en version préliminaire. Voir [Administration de HDInsight à l’aide du portail Azure en version préliminaire][hdinsight-admin-portal].

##Mise à l’échelle des clusters
La fonctionnalité de mise à l’échelle d’un cluster vous permet de modifier le nombre de nœuds de travail utilisés par un cluster exécuté dans Azure HDInsight sans avoir à recréer ce cluster.

>[AZURE.NOTE]Seuls les clusters ayant la version 3.1.3 de HDInsight ou une version ultérieure sont pris en charge. Si vous n’êtes pas sûr de la version de votre cluster, vous pouvez consulter la page Propriétés. Voir [Se familiariser avec l’interface du portail cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

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

	Set-AzureHDInsightClusterSize -Name <ClusterName> -ClusterSizeInNodes <NewSize>



##Envoi de tâches MapReduce
La distribution du cluster HDInsight intègre certains exemples MapReduce. L'un de ces exemples permet de compter la fréquence des mots dans les fichiers source.

**Pour envoyer une tâche MapReduce**

Le script Azure PowerShell suivant envoie l’exemple de tâche de comptage des mots :

	$clusterName = "<HDInsightClusterName>"

	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	# Run the job and show the standard error
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

Pour plus d’informations concernant le préfixe **wasb**, consultez la rubrique [Utilisation du stockage d’objets blob Azure pour HDInsight][hdinsight-storage].

**Pour télécharger le résultat de la tâche MapReduce**

Le script Azure PowerShell suivant extrait le résultat de la tâche MapReduce de la dernière procédure :

	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez l'article [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].






































##Envoi de tâches Hive
La distribution du cluster HDInsight intègre un exemple de table Hive intitulé *hivesampletable*. Vous pouvez utiliser la commande HiveQL **AFFICHER LES TABLES** pour afficher la liste des tables Hive sur un cluster.

**Pour envoyer une tâche Hive**

Le script suivant envoie une tâche Hive afin d’afficher la liste des tables Hive :

	$clusterName = "<HDInsightClusterName>"

	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

La tâche Hive affiche d’abord les tables Hive créées sur le cluster et les données renvoyées par l’exemple de table Hive hivesampletable.

Pour plus d'informations sur l'utilisation de Hive, consultez l'article [Utilisation de Hive avec HDInsight][hdinsight-use-hive].


##Téléchargement de données vers le stockage d'objets blob Azure
Consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].

##Téléchargement du résultat de la tâche à partir du stockage d’objets blob Azure
Consultez la section [Envoi de tâches MapReduce](#mapreduce) de cet article.

## Voir aussi
* [Documentation de référence des applets de commande HDInsight][hdinsight-powershell-reference]
* [Administration de HDInsight à l’aide du portail Azure en version préliminaire][hdinsight-admin-portal]
* [Administration de HDInsight à l’aide de l’interface de ligne de commande][hdinsight-admin-cli]
* [Approvisionnement de clusters HDInsight][hdinsight-provision]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main d’Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=August15_HO8-->