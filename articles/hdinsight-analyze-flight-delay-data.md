<properties 
	pageTitle="Analyse des données sur les retards de vol avec Hadoop dans HDInsight | Azure" 
	description="Apprenez à utiliser un script PowerShell pour configurer le cluster HDInsight, exécuter la tâche Hive, exécuter la tache Sqool et supprimer le cluster." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/04/2014" 
	ms.author="jgao"/>

#Analyse des données sur les retards de vol avec Hadoop dans HDInsight

Hive permet d'exécuter des tâches Hadoop MapReduce via un langage de création de scripts semblable à SQL, nommé *[HiveQL][hadoop-hiveql]*, qui peut être appliqué à la synthèse, à l'interrogation et à l'analyse d'importants volumes de données. 

L'un des principaux avantages de HDInsight est la séparation du calcul et du stockage des données. HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Un processus MapReduce habituel peut être scindé en trois parties :

1. **Stockage de données dans un stockage d'objets blob.**  Il peut s'agir d'un processus continu. Par exemple, les données météorologiques, les données de capteur, les journaux web, et en l'occurrence, les données liées aux retards de vol, sont enregistrées dans un stockage d'objets blob.
2. **Exécution des tâches.**  Au moment de traiter les données, vous exécutez un script PowerShell (ou une application cliente) pour approvisionner un cluster HDInsight, exécuter des tâches et supprimer le cluster.  Les tâches enregistrent les données de sortie dans le stockage d'objets blob Azure. Les données de sortie sont conservées même après la suppression du cluster. De cette façon, vous ne devez payer que ce que vous avez consommé. 
3. **Extrayez la sortie à partir du stockage d'objets blob**ou, dans le cas présent, exportez les données vers une base de données SQL Azure.

Le schéma suivant illustre le scénario et la structure de cet article :

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Remarque** : les nombres indiqués dans le schéma correspondent aux titres des sections.

La partie principale de ce didacticiel indique comment utiliser un script PowerShell pour effectuer les tâches suivantes :

- Approvisionnement d'un cluster HDInsight
- Exécution d'une tâche Hive sur le cluster pour calculer les retards moyens enregistrés dans les aéroports.  Les données sur les retards de vol sont stockées dans un compte de stockage d'objets blob Azure. 
- Exécution d'une tâche Sqoop pour exporter le résultat de la tâche Hive dans une base de données SQL Azure.
- Suppression du cluster HDInsight. 

Dans les annexes, vous trouverez les instructions permettant de télécharger les données sur les retards de vol, de créer/télécharger une chaîne de requête Hive et de préparer une base de données SQL Azure pour la tâche Sqoop.

##Dans ce didacticiel

* [Conditions préalables](#prerequisite)
* [Approvisionnement d'un cluster  HDInsight et exécution de tâches Hive/Sqoop (M1)](#runjob)
* [Annexe A : Téléchargement des données de retard de vol dans un stockage d'objets blob Azure (A1)](#appendix-a)
* [Annexe B : Création et téléchargement d'un script HiveQL (A2)](#appendix-b)
* [Annexe C : Préparation d'une base de données SQL Azure pour le résultat de tâche Sqoop (A3)](#appendix-c)
* [Étapes suivantes](#nextsteps)

##<a id="prerequisite"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* Un poste de travail sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].
* Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les pages traitant des [formules d'abonnement][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].

###Présentation du stockage HDInsight

Les clusters Hadoop dans HDInsight utilisent le stockage d'objets blob Azure pour stocker des données.  Ce stockage s'intitule   *WASB* ou  *Azure Storage - Blob*. WASB correspond à l'implémentation Microsoft  *HDFS* sur le stockage d'objets blob Azure. Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

Lorsque vous approvisionnez un cluster HDInsight, un conteneur de stockage d'objets blob d'un compte de stockage Azure est désigné comme système de fichiers par défaut, comme dans HDFS. Ce compte de stockage est connu sous le nom de  *default storage account*, et le conteneur d'objets blob sous le nom de  *default Blob container* ou  *default container*. Le compte de stockage par défaut doit se situer dans le même centre de données que le cluster HDInsight. La suppression d'un cluster HDInsight ne permet pas de supprimer le conteneur par défaut ou le compte de stockage par défaut.

Outre le compte de stockage par défaut, d'autres comptes de stockage Azure peuvent être associés à un cluster HDInsight pendant l'approvisionnement. L'association consiste à ajouter le compte de stockage et la clé du compte de stockage au fichier de configuration. Cela permet au cluster d'accéder à ces comptes de stockage à l'exécution. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision]. 

La syntaxe WASB est :

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[AZURE.NOTE] Le chemin d'accès WASB est le chemin d'accès virtuel.  Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight][hdinsight-storage]. 

Vous pouvez accéder à un fichier stocké dans le conteneur par défaut à partir de HDInsight en utilisant l'un des URI suivants (flightdelays.hql est utilisé comme exemple) :

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Pour accéder directement au fichier à partir du compte de stockage, le nom de l'objet blob du fichier est :

	tutorials/flightdelays/flightdelays.hql

Remarquez l'absence de " / " devant le nom d'objet blob.

**Fichiers utilisés dans ce didacticiel**

Ce didacticiel utilise les données de ponctualité des vols des compagnies aériennes de la [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA). Les données ont été téléchargées dans un conteneur de stockage d'objets blob Azure avec l'autorisation d'accès aux objets blob publics. Étant donné qu'il s'agit d'un conteneur d'objets blob publics, il n'est pas nécessaire d'associer ce compte de stockage au cluster HDInsight exécutant le script Hive. Le script HiveQL est également téléchargé sur le même conteneur d'objets blob. Pour savoir comment obtenir/télécharger les données sur votre propre compte de stockage, et comment créer/télécharger le fichier de script HiveQL, consultez l'[annexe A](#appendix-a) et l'[annexe B](#appendix-b).

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><th>Fichiers</th><th>Description</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Fichier script HiveQL utilisé par la tâche Hive que vous exécuterez. Ce script a été téléchargé dans un conteneur de stockage d'objets blob Azure avec l'autorisation d'accès publique.  L' <a href="#appendix-b">annexe B</a> présente des instructions sur la préparation et le téléchargement de ce fichier sur votre propre compte de stockage d'objets blob Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Données d'entrée pour les tâches Hive. Les données ont été téléchargées dans un conteneur de stockage d'objets blob Azure avec l'autorisation d'accès publique.  L' <a href="#appendix-a">annexe A</a> présente des instructions sur l'obtention des données et leur téléchargement sur votre propre compte de stockage d'objets blob Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Chemin de sortie pour la tâche Hive. Le conteneur par défaut est utilisé pour le stockage des données de sortie.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Le dossier de statut Hive sur le conteneur par défaut.</td></tr>
</table>



###Présentation des tables interne et externe Hive

Voici quelques éléments à connaître sur les tables interne et externe Hive :

- La commande CREATE TABLE permet de créer une table interne. Le fichier de données doit se trouver dans le conteneur par défaut.
- La commande CREATE TABLE transfère le fichier de données vers le dossier /hive/warehouse/<TableName>.
- La commande CREATE EXTERNAL TABLE permet de créer une table externe. Le fichier de données peut se trouver à l'extérieur du conteneur par défaut.
- La commande CREATE EXTERNAL TABLE ne déplace pas le fichier de données.
- La commande CREATE EXTERNAL TABLE n'autorise aucun dossier à l'EMPLACEMENT. C'est la raison pour laquelle le didacticiel réalise une copie du fichier sample.log.

Pour plus d'informations, consultez la rubrique [HDInsight : introduction aux tables interne et externe Hive][cindygross-hive-tables].

> [AZURE.NOTE] Une des instructions HiveQL crée une table externe Hive. La table externe Hive conserve le fichier de données à son emplacement d'origine. La table interne Hive transfère le fichier de données vers hive\warehouse. Pour les besoins de la table interne Hive, le fichier de données doit être situé dans le conteneur par défaut. Pour les données stockées en dehors du conteneur d'objets blob par défaut, vous devez utiliser des tables externes Hive.









##<a id="runjob"></a>Approvisionnement d'un cluster  HDInsight et exécution de tâches Hive/Sqoop 

Hadoop MapReduce correspond au traitement par lots. La façon la plus économique d'exécuter une tâche Hive consiste à approvisionner un cluster pour la tâche, et à supprimer cette dernière une fois qu'elle est terminée. Le script suivant couvre le processus dans son intégralité. Pour plus d'informations sur l'approvisionnement d'un cluster HDInsight et l'exécution de tâches Hive, consultez les rubriques [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision] et  [Utilisation de Hive avec HDInsight][hdinsight-use-hive]. 

**Pour exécuter des requêtes Hive à l'aide de PowerShell**

1. Créez une base de données SQL Azure et la table pour le résultat de tâche Sqoop au moyen des instructions figurant dans l'[annexe C](#appendix-c).
2. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$hdinsightClusterName</td><td>Nom du cluster HDInsight. Si le cluster n'existe pas, le script va en créer un avec le nom entré.</td></tr>
	<tr><td>$storageAccountName</td><td>Compte de stockage Azure qui sera utilisé comme compte de stockage par défaut. Cette valeur est uniquement requise lorsque le script doit créer un cluster HDInsight. Laissez-la vide si vous avez spécifié un nom de cluster HDInsight existant pour $hdinsightClusterName. Si le compte de stockage avec la valeur entrée n'existe pas, le script en créera un avec ce nom.</td></tr>
	<tr><td>$blobContainerName</td><td>Conteneur d'objets blob qui sera utilisé pour le système de fichiers par défaut. Si vous laissez cet argument vide, la valeur $hdinsightClusterName sera utilisée. </td></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nom du serveur de base de données SQL Azure. Il doit s'agir d'un serveur existant. Consultez <a href="#appendix-c">l'annexe C</a> pour en créer un.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nom de la connexion du serveur de base de données SQL Azure.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Mot de passe de la connexion du serveur de base de données SQL Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Base de données SQL vers laquelle Sqoop exporte les données. Le nom par défaut est " HDISqoop ". Le nom de la table pour le résultat de tâche Sqooop est " AvgDelays ". </td></tr>
	</table>
2. Ouvrez PowerShell ISE.
2. Copiez-collez le script suivant dans le volet du script :

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDinsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial.
		[String]$location = "Central US"     # For better performance, choose a data center near you.
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as te the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script.  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs.
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist.
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default storage account/container names of the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned.
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsigtht cluster
		        # Create an Azure storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive -File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

4. Appuyez sur **F5** pour exécuter le script. Le résultat doit ressembler à ceci :

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
5. Connectez-vous à votre base de données SQL et consultez les retards de vol moyens par ville dans la  *AvgDelays* table :

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Annexe A - Téléchargement de données de retard de vol vers le stockage d'objets blob Azure
Avant de télécharger le fichier de données et les fichiers de script HiveQL (voir l'[annexe B](#appendix-b), un certain de niveau de planification est nécessaire. Il s'agit de stocker les fichiers de données et le fichier HiveQL avant d'approvisionner un cluster HDInsight et d'exécuter la tâche Hive.  Deux options s'offrent à vous :

- **Utiliser le même compte de stockage Azure que celui qui sera utilisé comme système de fichiers par défaut par le cluster HDInsight.** Étant donné que le cluster HDInsight disposera de la clé d'accès au compte de stockage, il n'est pas nécessaire d'effectuer des modifications supplémentaires.
- **Utiliser un compte de stockage différent du système de fichiers par défaut du cluster HDInsight.** Le cas échéant, vous devez modifier la partie d'approvisionnement du script PowerShell figurant dans [Approvisionnement d'un cluster HDInsight et exécution de tâches Hive/Sqoop](#runjob) pour inclure le compte de stockage en tant que compte de stockage supplémentaire. Pour plus d'informations, consultez la rubrique [Approvisionnement de clusters Hadoop dans HDInsight][hdinsight-provision]. En procédant ainsi, le cluster HDInsight connaît la clé d'accès pour le compte de stockage.

>[AZURE.NOTE] Le chemin WASB pour le fichier de données est codé en dur dans le fichier de script HiveQL. Vous devez le mettre à jour en conséquence.

**Pour télécharger les données de vol**

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Sur la page, sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Filtre année</td><td>2013 </td></tr>
	<tr><td>Filtre période</td><td>Janvier</td></tr>
	<tr><td>Champs :</td><td>*Années*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (effacer tous les autres champs)</td></tr>
	</table>

3. Cliquez sur **Télécharger**. 
4. Décompressez le fichier dans le dossier **C:\Tutorials\FlightDelays\Data**.  Chaque fichier correspond à un fichier CSV d'environ 60 Go.
5.	Donnez au fichier le nom du mois dont il contient les données. Par exemple, renommez le fichier contenant les données du mois de janvier  *January.csv*.
6. Répétez les étapes 2 et 5 pour télécharger chaque fichier correspondant au 12 mois de l'année 2013. Vous devez disposer d'au moins un fichier pour exécuter le didacticiel.  

**Pour télécharger les données de retard de vol vers le stockage d'objets blob Azure**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$storageAccountName</td><td>Compte de stockage Azure sur lequel vous voulez télécharger les données.</td></tr>
	<tr><td>$blobContainerName</td><td>Conteneur d'objets blob dans lequel vous voulez télécharger les données.</td></tr>
	</table>
2. Ouvrez PowerShell ISE.
2. Collez le script suivant dans le volet du script :

		[CmdletBinding()]
		Param(
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)
		
		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # the source folder
		$destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user inpute
		# Validate the storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
		    Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
		    Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
		    foreach ($item in Get-ChildItem -Path $localFolder){
		        $fileName = "$localFolder\$item"
		        $blobName = "$destFolder/$item"
		
		        Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
		        Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
		    }
		}
		else
		{
		    Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDinsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

3. Appuyez sur **F5** pour exécuter le script.

Si vous décidez d'utiliser une autre méthode pour télécharger les fichiers, vérifiez que le chemin d'accès au fichier est bien  *tutorials/flightdelays/data*. La syntaxe permettant d'accéder aux fichiers est la suivante :

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* correspond au dossier virtuel que vous avez créé lors du téléchargement des fichiers. Assurez-vous qu'il existe 12 fichiers, un par mois.

>[AZURE.NOTE] Vous devez mettre à jour la requête Hive pour lire à partir du nouvel emplacement.

> Vous devez configurer l'autorisation d'accès au conteneur pour qu'elle soit publique ou lier le compte de stockage au cluster HDInsight.  Dans le cas contraire, la chaîne de requête Hive ne pourra pas accéder aux fichiers de données. 

---
##<a id="appendix-b"></a>Annexe B - Création et téléchargement d'un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Cette section explique comment créer un script HiveQL et télécharger celui-ci vers le stockage d'objets blob Azure en utilisant Azure PowerShell. Hive requiert le stockage de scripts HiveQL sur WASB.

Le script HiveQL exécutera les opérations suivantes :

1. **Il supprime la table delays_raw**, le cas échéant.
2. **Il crée la table externe Hive delays_raw** pointant vers l'emplacement WASB où se trouvent les fichiers de retard de vol. Cette requête spécifie les champs délimités par " , " et les lignes se terminant par " \n ". Cela pose un problème lorsque les valeurs des champs *contain* des virgules, car Hive n'est pas en mesure de différencier une virgule utilisée comme délimiteur de champ d'une virgule faisant partie d'une valeur de champ (ce qui est le cas pour les valeurs des champs ORIGIN\_CITY\_NAME et DEST\_CITY\_NAME). Pour y remédier, la requête crée des colonnes TEMP afin de contenir les données incorrectement réparties dans les colonnes.  
3. **Il supprime la table des retards**, le cas échéant.
4. **Il crée la table des retards**. Il est conseillé de nettoyer les données avant tout traitement plus approfondi. Cette requête crée une table,  *delays* à partir de la table *delays_raw*. Notez que les colonnes TEMP (comme indiqué précédemment) ne sont pas copiées et que la fonction the *substring* est utilisée pour supprimer les guillemets présents dans les données. 
5. **Il calcule les retards moyens liés aux conditions météo et regroupe les résultats par nom de ville.** Il transmet également les résultats à WASB. Notez que la requête supprime les apostrophes des données et exclut les lignes dans lesquelles la valeur correspondant à  *weather_deal*y est *null*, ce qui est nécessaire car Sqoop, utilisé plus tard dans ce didacticiel, ne gère pas ces valeurs par défaut

Pour obtenir la liste complète des commandes HiveQL, consultez la rubrique [Langage de définition des données Hive][hadoop-hiveql]. Chaque commande HiveQL doit se terminer par un point virgule.

**Pour créer un fichier de script HiveQL**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$storageAccountName</td><td>Compte de stockage Azure sur lequel vous voulez télécharger le script HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>Conteneur d'objets blob dans lequel vous voulez télécharger le script HiveQL.</td></tr>
	</table>
2. Ouvrez Azure PowerShell ISE.

2. Copiez-collez le script suivant dans le volet du script :

		[CmdletBinding()]
		Param(
		
		    # Azure blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# the HQL script file is exported as this file before uploaded to WASB
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# the HQL script file will be upload to WASB as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# this two constants are used by the HQL scrpit file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# check if a file with the same file name already exist on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
		            -ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
		        "YEAR string, " +
		        "FL_DATE string, " +
		        "UNIQUE_CARRIER string, " +
		        "CARRIER string, " +
		        "FL_NUM string, " +
		        "ORIGIN_AIRPORT_ID string, " +
		        "ORIGIN string, " +
		        "ORIGIN_CITY_NAME string, " +
		        "ORIGIN_CITY_NAME_TEMP string, " +
		        "ORIGIN_STATE_ABR string, " +
		        "DEST_AIRPORT_ID string, " +
		        "DEST string, " +
		        "DEST_CITY_NAME string, " +
		        "DEST_CITY_NAME_TEMP string, " +
		        "DEST_STATE_ABR string, " +
		        "DEP_DELAY_NEW float, " +
		        "ARR_DELAY_NEW float, " +
		        "CARRIER_DELAY float, " +
		        "WEATHER_DELAY float, " +
		        "NAS_DELAY float, " +
		        "SECURITY_DELAY float, " +
		        "LATE_AIRCRAFT_DELAY float) " +
		    "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
		    "LINES TERMINATED BY '\n' " +
		    "STORED AS TEXTFILE " +
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
		    "SELECT YEAR AS year, " +
		        "FL_DATE AS flight_date, " +
		        "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
		        "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
		        "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
		        "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
		        "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
		        "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
		        "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
		        "DEST_AIRPORT_ID AS dest_airport_id, " +
		        "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
		        "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
		        "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
		        "DEP_DELAY_NEW AS dep_delay_new, " +
		        "ARR_DELAY_NEW AS arr_delay_new, " +
		        "CARRIER_DELAY AS carrier_delay, " +
		        "WEATHER_DELAY AS weather_delay, " +
		        "NAS_DELAY AS nas_delay, " +
		        "SECURITY_DELAY AS security_delay, " +
		        "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
		    "FROM delays_raw;" 
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
		    "SELECT regexp_replace(origin_city_name, '''', ''), " +
		        "avg(weather_delay) " +
		    "FROM delays " +
		    "WHERE weather_delay IS NOT NULL " +
		    "GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to WASB
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Voici les variables utilisées dans le script :

	- **$hqlLocalFileName** : le script enregistre le fichier script HiveQL en local avant de le télécharger sur WASB. Il s'agit du nom de fichier. La valeur par défaut est <u>C:\tutorials\flightdelays\flightdelays.hql</u>.
	- **$hqlBlobName** : il s'agit du nom d'objet blob du fichier de script HiveQL utilisé dans le stockage d'objets blob Azure. La valeur par défaut est <u>tutorials/flightdelays/flightdelays.hql</u>. Étant donné que le fichier sera écrit directement dans le stockage d'objets blob Azure, il n'y a PAS de " / " au début du nom d'objet blob. Si vous voulez accéder au fichier à partir de WASB, il vous faudra ajouter " / " au début du nom de fichier.
	- **$srcDataFolder** et **$dstDataFolder** :  = "tutorials/flightdelays/data" 
 = "tutorials/flightdelays/output"


---
##<a id="appendix-c"></a>Annexe C - Préparation d'une base de données SQL Azure pour le résultat de tâche Sqoop
**Pour préparer la base de données SQL (fusionnez celle-ci avec le script Sqoop)**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nom du serveur de base de données SQL Azure. N'entrez rien pour créer un nouveau serveur.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nom de la connexion du serveur de base de données SQL Azure. Si $sqlDatabaseServerName est un serveur existant, la connexion et le mot de passe de connexion permettent de s'authentifier auprès du serveur.  Sinon, ils permettent de créer un nouveau serveur.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Mot de passe de la connexion du serveur de base de données SQL Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>cette valeur est uniquement utilisée lors de la création d'un serveur de base de données Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Base de données SQL utilisée pour créer la table AvgDelays pour la tâche Sqoop. Si vous laissez cette valeur vide, une base de données intitulée " HDISqoop " est créée. Le nom de la table pour le résultat de tâche Sqooop est " AvgDelays ". </td></tr>
	</table>
2. Ouvrez PowerShell ISE. 
2. Copiez-collez le script suivant dans le volet du script :
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
		            [origin_city_name] [nvarchar](50) NOT NULL,
		            [weather_delay] float,
		        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
		        (
		            [origin_city_name] ASC
		        )
		        )"
		#endregion

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL Database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Excute a SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Le script utilise un service REST, http://bot.whatismyipaddress.com, pour extraire votre adresse IP externe. L'adresse IP permet de créer une règle de pare-feu pour votre serveur de base de données SQL.  

	Voici quelques variables utilisées dans le script :

	- **$ipAddressRestService** : la valeur par défaut est <u>http://bot.whatismyipaddress.com</u>. Il s'agit d'un service REST d'adresse IP publique permettant d'obtenir votre adresse IP externe. Vous pouvez utiliser d'autres services si vous voulez. Les adresses IP externes extraites au moyen du service serviront à créer une règle de pare-feu pour votre serveur de base de données SQL Azure, ce qui vous permet d'accéder à la base de données à partir de votre poste de travail (au moyen d'un script PowerShell).
	- **$fireWallRuleName** : il s'agit du nom de règle de pare-feu du serveur de base de données SQL Azure. Le nom par défaut est <u>FlightDelay</u>. Vous pouvez le renommer si vous voulez.
	- **$sqlDatabaseMaxSizeGB** : cette valeur est uniquement utilisée lors de la création d'un serveur de base de données SQL Azure. La valeur par défaut est <u>10 Go</u>. Une capacité de 10 Go est suffisante pour ce didacticiel.
	- **$sqlDatabaseName** : cette valeur est uniquement utilisée lors de la création d'une base de données SQL Azure. La valeur par défaut est <u>HDISqoop</u>. Si vous la renommez, vous devez mettre à jour le script Sqoop PowerShell en conséquence. 

4. Appuyez sur **F5** pour exécuter le script. 
5. Validez la sortie du script. Vérifiez que le script s'est correctement exécuté.	

##<a id="nextsteps"></a> Étapes suivantes
Vous savez à présent télécharger un fichier vers le stockage d'objets blob, renseigner une table Hive à l'aide des données du stockage d'objets blob, exécuter des requêtes Hive et utiliser Sqoop pour exporter des données entre HDFS et la base de données SQL Azure. Pour en savoir plus, consultez les articles suivants :

* [Prise en main de HDInsight][hdinsight-get-started]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation d'Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]
* [Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/fr-fr/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/fr-fr/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/fr-fr/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=42-->
