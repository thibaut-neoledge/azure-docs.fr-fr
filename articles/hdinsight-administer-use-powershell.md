<properties 
	pageTitle="Gestion des clusters Hadoop dans HDInsight avec Azure PowerShell | Azure" 
	description="Découvrez comment effectuer des tâches d'administration pour les clusters Hadoop dans HDInsight au moyen d'Azure PowerShell." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Gestion des clusters Hadoop dans HDInsight au moyen d'Azure PowerShell

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article vous indique comment gérer les clusters Hadoop dans HDInsight en utilisant la console locale Azure PowerShell par le biais de Windows PowerShell. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight][hdinsight-powershell-reference].

**Conditions préalables :**

Avant de commencer cet article, vous devez disposer des éléments suivants :

- Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets HDInsight PowerShell permettent d'effectuer les tâches associées à votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].

- Un poste de travail sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][Powershell-install-configure].

			
	

## Dans cet article

* [Approvisionnement d'un cluster](#provision)
* [Énumération et affichage des clusters](#listshow)
* [Suppression d'un cluster](#delete)
* [Octroi/révocation de l'accès aux services HTTP](#httpservices)
* [Envoi de tâches MapReduce](#mapreduce)
* [Envoi de tâches Hive](#hive)
* [Téléchargement de données vers le stockage d'objets blob](#upload)
* [Téléchargement de données de sortie MapReduce à partir du stockage d'objets blob](#download)


##<a id="provision"></a> Approvisionnement d'un cluster HDInsight
HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte Azure Storage et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight. 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Création d'un compte de stockage Azure**

Après avoir importé le fichier de paramètres de publication, vous pouvez utiliser la commande suivante pour créer un compte de stockage :

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight. Pour l'heure, les clusters HDInsight ne peuvent être approvisionnés que dans les centres de données suivants :

><ul>
<li>Asie du Sud-Est</li>
<li>Europe du Nord</li>
<li>Europe de l'Ouest</li>
<li>Est des États-Unis</li>
<li>Ouest des États-Unis</li>
</ul>



Pour plus d'informations sur la création d'un compte Azure Storage au moyen du portail de gestion, consultez la rubrique [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account/).

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Pour plus d'informations sur l'obtention d'informations à l'aide du portail de gestion, consultez la section * Affichage, copie et régénération de clés d'accès de stockage* de la rubrique [Création, gestion ou suppression d'un compte de stockage](../storage-create-storage-account/).

**Pour créer un conteneur de stockage Azure**

PowerShell n'est pas en mesure de créer un conteneur de stockage d'objets blob lors de l'approvisionnement de HDInsight. Vous pouvez en créer un à l'aide du script suivant :

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Pour approvisionner un cluster**

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


La capture d'écran suivante représente l'exécution du script :

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Énumération et affichage des détails de cluster
Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

**Pour afficher la liste de tous les clusters de l'abonnement actif**

	Get-AzureHDInsightCluster 

**Pour afficher les détails du cluster spécifique de l'abonnement actif**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Suppression d'un cluster
Utilisez les commandes suivantes pour supprimer un cluster :

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Octroi/révocation de l'accès aux services HTTP

Les clusters HDInsight disposent des services Web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès.  Pour cela, vous pouvez par exemple utiliser la commande suivante :

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Dans l'exemple <i>hdiv2</i> est un nom de cluster HDInsight.

>[AZURE.NOTE] En octroyant/révoquant l'accès, vous réinitialisez le nom d'utilisateur et le mot de passe du cluster.

Vous pouvez également effectuer cette opération à l'aide du portail de gestion Azure. Consultez la rubrique [Administration de HDInsight à l'aide du portail de gestion][hdinsight-admin-portal].

##<a id="mapreduce"></a> Envoi de tâches MapReduce
La distribution du cluster HDInsight intègre certains exemples MapReduce. L'un de ces exemples permet de compter la fréquence des mots dans les fichiers source.

**Pour envoyer une tâche MapReduce**

Le script PowerShell suivant envoie l'exemple de tâche de comptage des mots : 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Le fichier a été renommé  *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

Pour plus d'informations sur le préfixe WASB, consultez la page [Utilisation du stockage d'objets blob Azure pour HDInsight][hdinsight-
storage].

**Pour télécharger le résultat de la tâche MapReduce**

Le script PowerShell suivant extrait le résultat de la tâche MapReduce de la dernière procédure :

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez la rubrique [Utilisation de MapReduce avec HDInsight][hdinsight-use-mapreduce].






































##<a id="hive"></a> Envoi de tâches Hive
La distribution du cluster HDInsight intègre un exemple de table Hive intitulée  *hivesampletable*. Vous pouvez utiliser la requête HiveQL " show tables " pour afficher la liste des tables sur un cluster.

**Pour envoyer une tâche Hive**

Le script suivant envoie une tâche Hive afin d'afficher la liste des tables Hives :
	
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

La tâche Hive affiche d'abord les tables Hive créées sur le cluster et les données renvoyées par l'exemple de table Hive hivesampletable.

Pour plus d'informations sur l'utilisation de Hive, consultez la rubrique [Utilisation de Hive avec HDInsight][hdinsight-use-hive].


##<a id="upload"></a>Téléchargement de données vers le stockage d'objets blob
Consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].

##<a id="download"></a>Téléchargement de données de sortie MapReduce à partir du stockage d'objets blob Azure
Consultez la session [Envoi de tâches MapReduce](#mapreduce) dans cet article.

## Voir aussi
* [Documentation de référence des cmdlets HDInsight][hdinsight-powershell-reference]
* [Administration de HDInsight à l'aide du portail de gestion][hdinsight-admin-portal]
* [Administration de HDInsight à l'aide de l'interface de ligne de commande][hdinsight-admin-cli]
* [Approvisionnement de clusters HDInsight][hdinsight-provision]
* [Téléchargement de données vers HDInsight][hdinsight-upload-data]
* [Envoi de tâches Hadoop par programme][hdinsight-submit-jobs]
* [Prise en main d'Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/fr-fr/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=42-->
