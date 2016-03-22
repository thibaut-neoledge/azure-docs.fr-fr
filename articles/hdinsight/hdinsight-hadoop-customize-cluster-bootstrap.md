<properties
	pageTitle="Personnalisation de clusters HDInsight à l’aide de Bootstrap | Microsoft Azure"
	description="Apprenez à personnaliser des clusters HDInsight à l’aide de Bootstrap."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="jgao"/>

# Personnalisation de clusters HDInsight à l’aide de Bootstrap

Vous pouvez parfois être amené à vouloir configurer des fichiers de configuration, notamment :

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Les clusters ne peuvent pas conserver les modifications lorsqu’ils ont été réimagés. Pour plus d’informations sur le réimageage, consultez la page [Redémarrages d’instances de rôle pour cause de mise à jour du système d’exploitation](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Pour conserver les modifications apportées pendant la durée de vie des clusters, vous pouvez utiliser la personnalisation de cluster HDInsight au cours du processus de création. Il s’agit de la méthode recommandée pour modifier les configurations d’un cluster et les rendre persistantes dans l’ensemble de ces événements de redémarrage et de réinitialisation Azure. Ces modifications de configuration sont appliquées avant le démarrage du service. Il n’est donc pas nécessaire de redémarrer ce dernier.

Il existe 3 manières d’utiliser Bootstrap :

- Utilisation d’Azure PowerShell
- Utilisation du Kit de développement logiciel (SDK) .NET
- Utilisation du modèle ARM

Pour plus d’informations sur l’installation de composants supplémentaires sur un cluster HDInsight pendant le processus de création, consultez :

- [Personnalisation des clusters HDInsight à l'aide d'une action de script (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Personnalisation des clusters HDInsight à l'aide d'une action de script (Windows)](hdinsight-hadoop-customize-cluster.md)

## Utilisation d’Azure PowerShell

Le code PowerShell suivant permet de personnaliser une configuration Hive :

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

L’[annexe A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample) décrit un script PowerShell complet.

**Pour vérifier la modification :**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le volet gauche, cliquez sur **Parcourir**, puis cliquez sur **Clusters HDInsight**.
3. Cliquez sur le cluster que vous venez de créer en utilisant le script PowerShell.
4. Cliquez sur **Tableau de bord** en haut du panneau pour ouvrir l’interface utilisateur d’Ambari.
5. Cliquez sur **Hive** dans le menu de gauche.
6. Sous **Résumé**, cliquez sur **HiveServer2**.
7. Sélectionnez l’onglet **Configurations**.
8. Cliquez sur **Hive** dans le menu de gauche.
9. Cliquez sur l’onglet **Avancé**.
10. Faites défiler vers le bas, puis développez **Site hive avancé**.
11. Recherchez **hive.metastore.client.socket.timeout** dans la section.

Et d’autres exemples sur la personnalisation d’autres fichiers de configuration :

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Pour plus d’informations, consultez le billet de blog d’Azim Uddin intitulé [Customizing HDInsight Cluster provisioning](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).

## Utilisation du Kit de développement logiciel (SDK) .NET

Consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk#use-bootstrap).

## Utilisation du modèle Azure ARM

Vous pouvez utiliser Bootstrap dans le modèle ARM :

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop personnaliser cluster bootstrap modèle arm](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## Voir aussi

- [Création de clusters Hadoop dans HDInsight][hdinsight-provision-cluster] pour obtenir des instructions sur la création d’un cluster HDInsight à l’aide d’autres options personnalisées.
- [Développer des scripts d’action de script pour HDInsight][hdinsight-write-script]
- [Installer et utiliser Spark sur les clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur les clusters HDInsight][hdinsight-install-r]
- [Installer et utiliser Solr sur les clusters HDInsight](hdinsight-hadoop-solr-install.md)
- [Installez et utilisez Giraph sur les clusters HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Procédure de création d’un cluster"

## Annexe A : exemple PowerShell

Ce script PowerShell permet de créer un cluster HDInsight et de personnaliser un paramètre Hive :

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
    #endregion

    ####################################
    # Service names and varialbes
    ####################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---------HONumber=AcomDC_0309_2016-->