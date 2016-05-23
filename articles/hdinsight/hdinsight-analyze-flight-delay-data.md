<properties
	pageTitle="Analyse des données sur les retards de vol avec Hadoop dans HDInsight | Microsoft Azure"
	description="Apprenez à utiliser un script Windows PowerShell pour créer un cluster HDInsight, exécuter une tâche Hive, exécuter une tache Sqoop et supprimer le cluster."
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
	ms.date="03/04/2016"
	ms.author="jgao"/>

#Analyse des données sur les retards de vol avec Hive dans HDInsight

Hive permet d’exécuter des tâches Hadoop MapReduce via un langage de création de scripts semblable à SQL, nommé *[HiveQL][hadoop-hiveql]*, qui peut être appliqué à la synthèse, à l’envoi de requêtes et à l’analyse d’importants volumes de données.

> [AZURE.NOTE] Les étapes décrites dans ce document nécessitent un cluster HDInsight Windows. Pour les étapes fonctionnant avec un cluster basé sur Linux, consultez la rubrique [Analyse des données sur les retards de vol avec Hive dans HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

L’un des principaux avantages d’Azure HDInsight est la séparation du calcul et du stockage des données. HDInsight utilise le stockage d'objets blob Azure pour stocker des données. Une tâche classique comprend 3 parties :

1. **Le stockage des données dans un stockage d’objets blob Azure.** Il peut s'agir d'un processus continu. Par exemple, les données météorologiques, les données de capteur, les journaux web et, en l’occurrence, les données liées aux retards de vol, sont enregistrés dans un stockage d’objets blob Azure.
2. **L’exécution des tâches.** Au moment de traiter des données, exécutez un script Windows PowerShell (ou une application cliente) pour créer un cluster HDInsight, exécuter des tâches et supprimer le cluster. Les tâches enregistrent les données de sortie dans le stockage d'objets blob Azure. Les données de sortie sont conservées même après la suppression du cluster. De cette façon, vous ne payez que ce que vous avez consommé.
3. **La récupération du résultat à partir du stockage d’objets blob Azure** ou, dans le cas présent, l’exportation des données vers une base de données SQL Azure.

Le schéma suivant illustre le scénario et la structure de ce didacticiel :

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Remarque** : les nombres indiqués dans le schéma correspondent aux titres des sections. **M** représente le processus principal. **A** représente le contenu dans l'annexe.

La partie principale de ce didacticiel indique comment utiliser un script Windows PowerShell pour effectuer les tâches suivantes :

- Création d’un cluster HDInsight
- Exécution d’une tâche Hive sur le cluster pour calculer les retards moyens enregistrés dans les aéroports. Les données sur les retards de vol sont stockées dans un compte de stockage d’objets blob Azure.
- Exécution d'une tâche Sqoop pour exporter le résultat de la tâche Hive dans une base de données SQL Azure.
- Suppression du cluster HDInsight.

Dans les annexes, vous trouverez les instructions permettant de télécharger les données sur les retards de vol, de créer/télécharger une chaîne de requête Hive et de préparer une base de données SQL Azure pour la tâche Sqoop.

> [AZURE.NOTE] Les étapes de cette procédure sont spécifiques aux clusters HDInsight basés sur Windows. Pour les étapes fonctionnant avec un cluster basé sur Linux, consultez la rubrique [Analyse des données sur les retards de vol avec Hive dans HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un poste de travail sur lequel est installé Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Fichiers utilisés dans ce didacticiel**

Ce didacticiel utilise les données de ponctualité des vols des compagnies aériennes de la [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA). Une copie des données a été téléchargée dans un conteneur de stockage d’objets blob Azure avec l’autorisation d’accès aux objets blob publics. Une partie de votre script PowerShell copie les données à partir du conteneur d'objets blob public dans le conteneur d'objets blob par défaut de votre cluster. Le script HiveQL est également copié dans le même conteneur d’objets blob. Pour savoir comment obtenir/télécharger les données sur votre propre compte de stockage et comment créer/télécharger le fichier de script HiveQL, consultez l’[annexe A](#appendix-a) et l’[annexe B](#appendix-b).

Le tableau suivant répertorie les fichiers utilisés dans ce didacticiel :

<table border="1">
<tr><th>Fichiers</th><th>Description</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>Fichier script HiveQL utilisé par la tâche Hive que vous exécuterez. Ce script a été téléchargé dans un conteneur de stockage d'objets blob Azure avec l'autorisation d'accès publique. <a href="#appendix-b">L’annexe&#160;B</a> présente des instructions sur la préparation et le téléchargement de ce fichier sur votre propre compte de stockage d’objets blob Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Données d’entrée pour la tâche Hive. Les données ont été téléchargées dans un compte de stockage d’objets blob Azure avec une autorisation d’accès public. <a href="#appendix-a">L’annexe&#160;A</a> présente des instructions sur l’obtention des données et leur téléchargement sur votre propre compte de stockage d’objets blob Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Chemin de sortie pour la tâche Hive. Le conteneur par défaut est utilisé pour le stockage des données de sortie.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>Le dossier de statut Hive sur le conteneur par défaut.</td></tr>
</table>


##Création d’un cluster et exécution de tâches Hive/Sqoop

Hadoop MapReduce correspond au traitement par lots. La manière la plus économique d’exécuter une tâche Hive consiste à créer un cluster pour la tâche et à supprimer cette dernière une fois qu’elle est terminée. Le script suivant couvre le processus dans son intégralité. Pour plus d’informations sur la création d’un cluster HDInsight et l’exécution de tâches Hive, consultez les rubriques [Création de clusters Hadoop dans HDInsight][hdinsight-provision] et [Utilisation de Hive avec HDInsight][hdinsight-use-hive].

**Pour exécuter des requêtes Hive à l’aide d’Azure PowerShell**

1. Créez une base de données SQL Azure et la table pour le résultat de tâche Sqoop au moyen des instructions figurant dans l’[annexe C](#appendix-c).
3. Ouvrez Windows PowerShell ISE et exécutez le script suivant :

		$subscriptionID = "<Azure Subscription ID>"
		$nameToken = "<Enter an Alias>" 
		
		###########################################
		# You must configure the follwing variables
		# for an existing Azure SQL Database
		###########################################
		$existingSqlDatabaseServerName = "<Azure SQL Database Server>"
		$existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
		$existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
		$existingSqlDatabaseName = "<Azure SQL Database name>"
		
		$localFolder = "E:\Tutorials\Downloads" # A temp location for copying files. 
		$azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
		
		###########################################
		# (Optional) configure the following variables
		###########################################
		
		$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

		$resourceGroupName = $namePrefix + "rg"
		$location = "EAST US 2"
		
		$HDInsightClusterName = $namePrefix + "hdi"
		$httpUserName = "admin"
		$httpPassword = "<Enter the Password>"
		
		$defaultStorageAccountName = $namePrefix + "store"
		$defaultBlobContainerName = $HDInsightClusterName # use the cluster name
		
		$existingSqlDatabaseTableName = "AvgDelays"
		$sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
		
		$hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
		
		$jobStatusFolder = "/tutorials/flightdelays/jobstatus"
		
		###########################################
		# Login
		###########################################
		try{
			$acct = Get-AzureRmSubscription
		}
		catch{
			Login-AzureRmAccount
		}
		Select-AzureRmSubscription -SubscriptionID $subscriptionID
		
		###########################################
		# Create a new HDInsight cluster
		###########################################
		
		# Create ARM group
		New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
		
		# Create the default storage account
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
		
		# Create the default Blob container
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
		$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
		New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
		
		# Create the HDInsight cluster
		$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
		$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
		
		New-AzureRmHDInsightCluster `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-Location $location `
			-ClusterType Hadoop `
			-OSType Windows `
			-ClusterSizeInNodes 2 `
			-HttpCredential $httpCredential `
			-DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultStorageContainer $existingDefaultBlobContainerName 
		
		
		###########################################
		# Prepare the HiveQL script and source data
		###########################################
		
		# Create the temp location  
		New-Item -Path $localFolder -ItemType Directory -Force 
		
		# Download the sample file from Azure Blob storage
		$context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
		$blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
		#$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
		
		# Upload data to default container
		
		$azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
		
		Invoke-Expression -Command:$azcopycmd
		
		###########################################
		# Submit the Hive job
		###########################################
		Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
		$response = Invoke-AzureRmHDInsightHiveJob `
						-Files $hqlScriptFile `
						-DefaultContainer $defaultBlobContainerName `
						-DefaultStorageAccountName $defaultStorageAccountName `
						-DefaultStorageAccountKey $defaultStorageAccountKey `
						-StatusFolder $jobStatusFolder 
		
		write-Host $response
		
		
		###########################################
		# Submit the Sqoop job
		###########################################
		$exportDir = "wasb://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		$sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
						-Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $hdinsightClusterName `
						-HttpCredential $httpCredential `
						-JobDefinition $sqoopDef #-Debug -Verbose
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $HDInsightClusterName `
			-HttpCredential $httpCredential `
			-WaitTimeoutInSeconds 3600 `
			-Job $sqoopJob.JobId
		
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $hdinsightClusterName `
			-HttpCredential $httpCredential `
			-DefaultContainer $existingDefaultBlobContainerName `
			-DefaultStorageAccountName $defaultStorageAccountName `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-JobId $sqoopJob.JobId `
			-DisplayOutputType StandardError
		
		###########################################
		# Delete the cluster
		###########################################
		Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Connectez-vous à votre base de données SQL et consultez les retards de vol moyens par ville dans la table AvgDelays :

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Annexe A - Téléchargement de données de retard de vol vers le stockage d’objets blob Azure
Le téléchargement du fichier de données et des fichiers de script HiveQL (voir l’[annexe B](#appendix-b)) nécessite un minimum de planification. Il s’agit de stocker les fichiers de données et le fichier HiveQL avant de créer un cluster HDInsight et d’exécuter la tâche Hive. Deux options s'offrent à vous :

- **Utiliser le même compte Azure Storage qui sera utilisé par le cluster HDInsight en tant que système de fichier par défaut.** Étant donné que le cluster HDInsight disposera de la clé d’accès au compte de stockage, vous n’avez pas besoin d’effectuer des modifications supplémentaires.
- **Utiliser un compte Azure Storage différent du système de fichier par défaut du cluster HDInsight.** Le cas échéant, vous devez modifier la partie de création du script Windows PowerShell figurant dans [Création d’un cluster HDInsight et exécution de tâches Hive/Sqoop](#runjob) de manière à lier le compte de stockage comme compte supplémentaire. Pour plus d’informations, consultez la rubrique [Création de clusters Hadoop dans HDInsight][hdinsight-provision]. Le cluster HDInsight connaît ainsi la clé d’accès du compte de stockage.

>[AZURE.NOTE] Le chemin d’accès au stockage d’objets blob pour le fichier de données est codé en dur dans le fichier de script HiveQL. Vous devez le mettre à jour en conséquence.

**Pour télécharger les données de vol**

1. Accédez à [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website].
2. Sur la page, sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Filtre année</td><td>2013 </td></tr>
	<tr><td>Filtre période</td><td>Janvier</td></tr>
	<tr><td>Champs</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (effacez tous les autres champs)</td></tr>
	</table>

3. Cliquez sur **Télécharger**.
4. Décompressez le fichier dans le dossier **C:\\Tutorials\\FlightDelay\\2013Data**. Chaque fichier correspond à un fichier CSV d’environ 60 Go.
5.	Donnez au fichier le nom du mois dont il contient les données. Par exemple, renommez le fichier contenant les données du mois de janvier *Janvier.csv*.
6. Répétez les étapes 2 et 5 pour télécharger chaque fichier correspondant aux 12 mois de l’année 2013. Vous devez disposer d’au moins un fichier pour exécuter le didacticiel.  

**Pour télécharger les données de retard de vol vers le stockage d’objets blob Azure**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$storageAccountName</td><td>Compte Azure Storage sur lequel vous voulez télécharger les données.</td></tr>
	<tr><td>$blobContainerName</td><td>Conteneur d'objets blob dans lequel vous voulez télécharger les données.</td></tr>
	</table>
2. Ouvrez Azure PowerShell ISE.
3. Collez le script suivant dans le volet du script :

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
		$localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
		$destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
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
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion




4. Appuyez sur **F5** pour exécuter le script.

Si vous décidez d’utiliser une autre méthode pour télécharger les fichiers, vérifiez que le chemin d’accès au fichier est tutorials/flightdelay/data. La syntaxe permettant d'accéder aux fichiers est la suivante :

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Le chemin d’accès tutorials/flightdelay/data correspond au dossier virtuel que vous avez créé lors du chargement des fichiers. Assurez-vous qu'il existe 12 fichiers, un par mois.

>[AZURE.NOTE] Vous devez mettre à jour la requête Hive pour lire à partir du nouvel emplacement.

> Vous devez configurer l’autorisation d’accès au conteneur pour qu’il soit public ou lier le compte de stockage au cluster HDInsight. Dans le cas contraire, la chaîne de requête Hive ne pourra pas accéder aux fichiers de données.

---
##<a id="appendix-b"></a>Annexe B - Création et téléchargement d’un script HiveQL

À l'aide d'Azure PowerShell, vous pouvez exécuter plusieurs instructions HiveQL, une par une, ou empaqueter l'instruction HiveQL dans un fichier de script. Cette section explique comment créer un script HiveQL et télécharger celui-ci vers le stockage d’objets blob Azure en utilisant Azure PowerShell. Hive requiert le stockage de scripts HiveQL dans le stockage d’objets blob Azure.

Le script HiveQL exécutera les opérations suivantes :

1. **Il supprime la table delays\_raw**, le cas échéant.
2. **Il crée la table externe Hive delays\_raw ** pointant vers l’emplacement du stockage d’objets blob où se trouvent les fichiers de retard de vol. Cette requête spécifie les champs délimités par « , » et les lignes se terminant par « \\n ». Cela pose un problème lorsque les valeurs des champs contiennent des virgules, car Hive n'est pas en mesure de différencier une virgule utilisée en tant que délimiteur de champ d'une virgule faisant partie d'une valeur de champ (ce qui est le cas pour les valeurs des champs ORIGIN\_CITY\_NAME et DEST\_CITY\_NAME). Pour y remédier, la requête crée des colonnes TEMP afin de contenir les données incorrectement réparties dans les colonnes.  
3. **Il supprime la table des retards**, le cas échéant.
4. **Il crée la table des retards**. Il est conseillé de nettoyer les données avant tout traitement plus approfondi. Cette requête crée une nouvelle table, *retards*, à partir de la table delays\_raw. Notez que les colonnes TEMP (comme indiqué précédemment) ne sont pas copiées et que la fonction **substring** est utilisée pour supprimer les guillemets présents dans les données.
5. **Il calcule les retards moyens liés aux conditions météo et regroupe les résultats par nom de ville.** Il transmet également les résultats au stockage d’objets blob. Notez que la requête supprime les apostrophes des données et exclut les lignes dans lesquelles la valeur de **weather\_delay** est de type null. Ces mesures sont nécessaires, car Sqoop, qui est utilisé ultérieurement dans ce didacticiel, ne gère pas correctement ces valeurs par défaut.

Pour obtenir la liste complète des commandes HiveQL, consultez la rubrique [Langage de définition des données Hive][hadoop-hiveql]. Chaque commande HiveQL doit se terminer par un point virgule.

**Pour créer un fichier de script HiveQL**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$storageAccountName</td><td>Compte Azure Storage sur lequel vous voulez télécharger le script HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>Conteneur d'objets blob dans lequel vous voulez télécharger le script HiveQL.</td></tr>
	</table>
2. Ouvrez Azure PowerShell ISE.

3. Copiez-collez le script suivant dans le volet du script :

		[CmdletBinding()]
		Param(

		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,

		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)

		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelay/data"
		$dstDataFolder = "/tutorials/flightdelay/output"
		#endregion

		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#Region - Validate user input
		Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
		# Validate the Storage account
		if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
		{
			Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
			exit
		}
		else{
			$resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
		}
		
		# Validate the container
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
			Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
			Exit
		}
		#EngRegion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
			$isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
			if ($isDelete.ToLower() -ne "y")
			{
				Exit
			}
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
			Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
			new-item $folder -ItemType directory  
		}
		#end region
		
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
		$storageAccountKey = Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName | %{$_.Key1}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	Voici les variables utilisées dans le script :

	- **$hqlLocalFileName** - Le script enregistre le fichier script HiveQL en local avant de le télécharger dans le stockage d’objets blob. Il s'agit du nom de fichier. La valeur par défaut est <u>C:\\tutorials\\flightdelay\\flightdelays.hql</u>.
	- **$hqlBlobName** - Il s’agit du nom d’objet blob du fichier de script HiveQL utilisé dans le stockage d’objets blob Azure. La valeur par défaut est tutorials/flightdelay/flightdelays.hql. Étant donné que le fichier sera écrit directement dans le stockage d'objets blob Azure, il n'y a PAS de « / » au début du nom d'objet blob. Si vous voulez accéder au fichier à partir du stockage d’objets blob, il vous faudra ajouter « / » au début du nom de fichier.
	- **$srcDataFolder** et **$dstDataFolder** - = "tutorials/flightdelay/data" = "tutorials/flightdelay/output"


---
##<a id="appendix-c"></a>Annexe C - Préparation d’une base de données SQL Azure pour le résultat de tâche Sqoop
**Pour préparer la base de données SQL (fusionnez celle-ci avec le script Sqoop)**

1. Préparez les paramètres :

	<table border="1">
	<tr><th>Nom de la variable</th><th>Remarques</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nom du serveur de la base de données SQL Azure. N'entrez rien pour créer un nouveau serveur.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nom de connexion pour le serveur de la base de données SQL&#160;Azure. Si $sqlDatabaseServerName est un serveur existant, la connexion et le mot de passe de connexion permettent de s'authentifier auprès du serveur. Sinon, ils permettent de créer un nouveau serveur.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Mot de passe pour se connecter au serveur de la base de données SQL&#160;Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Cette valeur est uniquement utilisée lors de la création d’un nouveau serveur de base de données Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>Base de données SQL utilisée pour créer la table AvgDelays pour la tâche Sqoop. Si vous laissez cette valeur vide, une base de données intitulée HDISqoop est créée. La table créée pour le résultat de la tâche Sqoop s’appelle AvgDelays. </td></tr>
	</table>
2. Ouvrez Azure PowerShell ISE.
3. Copiez-collez le script suivant dans le volet du script :

		[CmdletBinding()]
		Param(
		
			# Azure Resource group variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
			[String]$resourceGroupName,
		
			# SQL database server variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseServer, 
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user.")]
			[String]$sqlDatabaseLogin,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the Azure SQL Database admin user password.")]
			[String]$sqlDatabasePassword,
		
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the region to create the Database in.")]
			[String]$sqlDatabaseLocation,   #For example, West US.
		
			# SQL database variables
			[Parameter(Mandatory=$True,
					HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
			[String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
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
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		try{Get-AzureRmContext}
		catch{Login-AzureRmAccount}
		#EndRegion
		
		#region - Create and validate Azure resouce group
		try{
			Get-AzureRmResourceGroup -Name $resourceGroupName
		}
		catch{
			New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
		}
		
		#EndRegion
		
		#region - Create and validate Azure SQL database server
		try{
			Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
		catch{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
		
			$sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
			$credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
		
			$sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
			Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
			Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
			$workstationIPAddress = Invoke-RestMethod $ipAddressRestService
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
		
			#To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
			New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
		}
		
		#endregion
		
		#region - Create and validate Azure SQL database
		
		try {
			Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		}
		catch {
			Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
			New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
		}
		
		#endregion
		
		#region -  Execute an SQL command to create the AvgDelays table
		
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
		
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE] Le script utilise un service REST (representational state transfer), http://bot.whatismyipaddress.com, pour extraire votre adresse IP externe. L’adresse IP permet de créer une règle de pare-feu pour votre serveur de base de données SQL.

	Voici quelques variables utilisées dans le script :

	- **$ipAddressRestService** - La valeur par défaut est http://bot.whatismyipaddress.com. Il s’agit d’un service REST d’adresse IP publique permettant d’obtenir votre adresse IP externe. Vous pouvez utiliser d'autres services si vous voulez. L’adresse IP externe extraite au moyen du service servira à créer une règle de pare-feu pour votre serveur de base de données SQL Azure, ce qui vous permet d’accéder à la base de données à partir de votre poste de travail (au moyen d’un script Windows PowerShell).
	- **$fireWallRuleName** - Il s’agit du nom de la règle de pare-feu pour le serveur de la base de données SQL Azure. Le nom par défaut est <u>FlightDelay</u>. Vous pouvez le renommer si vous voulez.
	- **$sqlDatabaseMaxSizeGB** - Cette valeur est uniquement utilisée lors de la création d’un nouveau serveur de base de données SQL Azure. La valeur par défaut est 10 Go. Une capacité de 10 Go est suffisante pour ce didacticiel.
	- **$sqlDatabaseName** - Cette valeur est uniquement utilisée lors de la création d’une nouvelle base de données SQL Azure. La valeur par défaut est HDISqoop. Si vous la renommez, vous devez mettre à jour le script Sqoop Windows PowerShell en conséquence.

4. Appuyez sur **F5** pour exécuter le script.
5. Validez la sortie du script. Vérifiez que le script s'est correctement exécuté.

##<a id="nextsteps"></a> Étapes suivantes
Vous savez à présent télécharger un fichier vers le stockage d’objets blob Azure, renseigner une table Hive à l’aide des données du stockage d’objets blob Azure, exécuter des requêtes Hive et utiliser Sqoop pour exporter des données entre HDFS et une base de données SQL Azure. Pour en savoir plus, consultez les articles suivants :

* [Prise en main de HDInsight][hdinsight-get-started]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation d’Oozie avec HDInsight][hdinsight-use-oozie]
* [Utilisation de Sqoop avec HDInsight][hdinsight-use-sqoop]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

<!---HONumber=AcomDC_0511_2016-->