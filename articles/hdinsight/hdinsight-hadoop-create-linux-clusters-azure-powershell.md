<properties
   	pageTitle="Créer des clusters Hadoop, HBase, Storm ou Spark sur Linux dans HDInsight à l’aide de l’interface de ligne de commande Azure | Microsoft Azure"
   	description="Découvrez comment créer des clusters Hadoop, HBase, Storm ou Spark sur Linux pour HDInsight avec l’interface de ligne de commande Azure."
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
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>

#Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell

[AZURE.INCLUDE [sélecteur](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Ce document fournit des informations sur l’approvisionnement d’un cluster HDInsight basé sur Linux avec Azure PowerShell, ainsi qu’un exemple de script.

> [AZURE.NOTE] Azure PowerShell est uniquement disponible sur les clients Windows. Si vous utilisez un client Linux, Unix ou Mac OS X, consultez [Créer un cluster HDInsight basé sur Linux avec l’interface de ligne de commande Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour créer un cluster.

###Configuration requise

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- __Azure PowerSHell__. Pour plus d’informations sur la configuration d’un poste de travail pour exécuter des applets de commande HDInsight Windows Powershell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation d’Azure PowerShell avec HDInsight, consultez la rubrique [Administration de HDInsight avec PowerShell](hdinsight-administer-use-powershell.md). Pour la liste des applets de commande Windows PowerShell pour HDInsight, consultez la rubrique [Référence des applets de commande HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

##Créer des clusters

Les procédures suivantes sont nécessaires pour mettre en service un cluster HDInsight en utilisant Azure PowerShell :

- Création d’un groupe de ressources Azure
- Création d’un compte Azure Storage
- Création d'un conteneur d'objets blob Azure
- Création d'un cluster HDInsight

Les deux paramètres les plus importants que vous devez définir pour approvisionner les clusters Linux sont ceux où vous spécifiez le type de système d’exploitation et les détails de l’utilisateur SSH :

- Vérifiez que le paramètre **-OSType** est défini sur **Linux**.
- Pour utiliser le protocole SSH pour les sessions à distance sur les clusters, vous pouvez spécifier le mot de passe utilisateur SSH ou la clé publique SSH. Si vous spécifiez le mot de passe utilisateur SSH et la clé publique SSH, la clé sera ignorée. Si vous souhaitez utiliser la clé SSH pour les sessions à distance, vous devez spécifier un mot de passe SSH vide lorsque vous y êtes invité. Pour plus d'informations sur l'utilisation de SSH avec HDInsight, consultez l'un des articles suivants :
    
    * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Le script suivant montre comment créer un nouveau cluster :

    ###########################################
    # Create required items, if none exist
    ###########################################

    # Sign in
    Add-AzureRmAccount

    # Select the subscription to use
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    $resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
    $location = "<Location>"                        # For example, "West US"
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account
    $storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

    # Create an Azure Blob Storage container
    $containerName = "<ContainerName>"              # Provide a container name
    $storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
    $destContext = New-AzureRmStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    New-AzureRmStorageContainer -Name $containerName -Context $destContext

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
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -OSType Linux -Version "3.2" -SshCredential $sshCredentials

Les valeurs que vous spécifiez pour **$clusterCredentials** sont utilisées pour créer le compte d’utilisateur Hadoop pour le cluster. Vous utiliserez ce compte pour vous connecter au cluster. Les valeurs que vous spécifiez pour **$sshCredentials** sont utilisées pour créer le compte d’utilisateur SSH pour le cluster. Vous utilisez ce compte pour démarrer une session SSH à distance sur le cluster et pour exécuter des tâches.

> [AZURE.IMPORTANT] Dans ce script, vous devez spécifier le nombre de nœuds de travail qui seront présents dans le cluster. Si vous envisagez d’utiliser plus de 32 nœuds de travail lors de la création du cluster ou en faisant évoluer le cluster après sa création, vous devez également spécifier une taille de nœud principal avec au moins 8 cœurs et 14 Go de RAM.
>
> Pour plus d’informations sur les tailles de nœud et les coûts associés, consultez la rubrique [Tarification HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

L’approvisionnement peut prendre jusqu’à 15 minutes.

##Personnalisation des clusters

- Voir [Personnalisation de clusters HDInsight à l'aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Voir [Personnalisation de clusters HDInsight basés sur Windows à l'aide d'une action de script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

##Étapes suivantes

Vous avez créé un cluster HDInsight. Pour apprendre à l’utiliser, consultez les rubriques ci-dessous :

###Clusters Hadoop

* [Utilisation de Hive avec HDInsight](hdinsight-use-hive.md)
* [Utilisation de Pig avec HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

###Clusters HBase

* [Prise en main de HBase sur HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Développement d’applications Java pour HBase sur HDInsight](hdinsight-hbase-build-java-maven-linux)

###Clusters Storm

* [Développement de topologies Java pour Storm sur HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilisation de composants Python dans Storm sur HDInsight](hdinsight-storm-develop-python-topology.md)
* [Déploiement et analyse des topologies avec Storm sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

###Clusters Spark

* [Créer une application autonome avec Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : utilisez Spark dans HDInsight pour créer des applications de streaming en continu en temps réel](hdinsight-apache-spark-eventhub-streaming.md)

<!---HONumber=AcomDC_0218_2016-->