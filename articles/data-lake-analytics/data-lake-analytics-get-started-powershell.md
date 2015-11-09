<properties 
   pageTitle="Prise en main du service d’analyse Analytique Data Lake à l’aide d’Azure PowerShell | Azure" 
   description="Découvrez comment utiliser Azure PowerShell pour créer un compte Data Lake Store, créer un travail Analytique Data Lake avec U-SQL et le soumettre." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/22/2015"
   ms.author="jgao"/>

# Prise en main du service Analytique Data Lake Azure à l’aide d’Azure PowerShell

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser Azure PowerShell pour créer des comptes Analytique Data Lake Azure, définir des travaux Analytique Data Lake dans [U-SQL](data-lake-analytics-u-sql-get-started.md), et envoyer des travaux à des comptes Analytique Data Lake. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertir en fichier .csv (valeurs séparées par des virgules). Pour suivre ce même didacticiel à l’aide d’autres outils pris en charge, cliquez sur les onglets en haut de cette section.

**Processus Analytique Data Lake de base :**

![Diagramme du flux de processus Analytique Data Lake Azure](./media/data-lake-analytics-get-started-portal/data-lake-analytics-process.png)

1. Créer un compte Analytique Data Lake.
2. Préparer les données source. Les travaux Analytique Data Lake peuvent lire les données depuis les comptes Azure Data Store Lake ou les comptes de stockage d’objets blobs Azure.   
3. Développer un script U-SQL.
4. Envoyer un travail (script U-SQL) vers le compte Analytique Data Lake. Le travail lit les données source, traite les données conformément au script U-SQL, puis enregistre la sortie dans un compte Data Lake Store ou un compte de stockage d’objets Blobs.


**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/fr-FR/pricing/free-trial/).
- **Azure PowerShell 1.0 ou version ultérieure**. Consultez [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md) Après avoir installé Azure PowerShell 1.0 ou version ultérieure, vous devez exécuter l’applet de commande suivante pour installer le module Analytique Data Lake Azure.

		Install-Module AzureRM.DataLakeStore
		Install-Module AzureRM.DataLakeAnalytics

	Pour plus d’informations sur le module **AzureRM.DataLakeStore**, consultez [PowerShell Gallery](http://www.powershellgallery.com/packages/AzureRM.DataLakeStore). Pour plus d’informations sur le module **AzureRM.DataLakeAnalytics**, consultez [PowerShell Gallery](http://www.powershellgallery.com/packages/AzureRM.DataLakeAnalytics).

	Si vous créez un compte Data Lake pour la première fois, exécutez :

		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeAnalytics"

	Pour vous connecter à Azure, utilisez les applets de commande suivantes :

		Login-AzureRmAccount
		Get-AzureRmSubscription  # for finding the Azure Subscription ID
		Set-AzureRmContext -SubscriptionID <Azure Subscription ID>

##Créer un compte Analytique Data Lake

Vous devez disposer d’un compte Analytique Data Lake avant de pouvoir exécuter un travail quelconque. Pour créer un compte Analytique Data Lake, vous devez spécifier les éléments suivants :

- **Groupe de ressources Azure** : un compte Analytique Data Lake doit être créé au sein d’un groupe de ressources Azure. [Azure Resource Manager](resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.  

	Pour répertorier les groupes de ressources dans votre abonnement :
    
    	Get-AzureRmResourceGroup
    
	Pour créer un groupe de ressources :

    	New-AzureRmResourceGroup `
			-Name "<Your resource group name>" `
			-Location "<Azure Data Center>" # For example, "East US 2"

- **Nom du compte Analytique Data Lake**
- **Emplacement** : un des centres de données Azure prenant en charge Analytique Data Lake.
- **Compte Data Lake par défaut** : chaque compte Analytique Data Lake possède un compte Data Lake par défaut.

	Pour créer un compte Azure Data Lake :

	    New-AzureRmDataLakeStoreAccount `
	        -ResourceGroupName "<Your Azure resource group name>" `
	        -Name "<Your Data Lake account name>" `
	        -Location "<Azure Data Center>"  # For example, "East US 2"

	> [AZURE.NOTE]Le nom de compte Data Lake doit contenir uniquement des lettres minuscules et des chiffres.



**Pour créer un compte Analytique Data Lake**

1. Dans votre station de travail Windows, ouvrez PowerShell ISE.
2. Exécutez le script qui suit :

		$resourceGroupName = "<ResourceGroupName>"
		$dataLakeStoreName = "<DataLakeAccountName>"
		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$location = "East US 2"
		
		Write-Host "Create a resource group ..." -ForegroundColor Green
		New-AzureRmResourceGroup `
			-Name  $resourceGroupName `
			-Location $location
		
		Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
		New-AzureRmDataLakeStoreAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeStoreName `
			-Location $location 
		
		Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
		New-AzureRmDataLakeAnalyticsAccount `
			-Name $dataLakeAnalyticsName `
			-ResourceGroupName $resourceGroupName `
			-Location $location `
			-DefaultDataLake $dataLakeStoreName
		
		Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
		Get-AzureRmDataLakeAnalyticsAccount `
			-ResourceGroupName $resourceGroupName `
			-Name $dataLakeAnalyticsName  

##Téléchargement de données vers Data Lake

Dans ce didacticiel, vous allez traiter des journaux de recherche. Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Un exemple de fichier journal de recherche a été copié vers un conteneur d’objets blobs Azure public. Utilisez le script PowerShell suivant pour télécharger le fichier sur votre station de travail, puis téléchargez le fichier dans le compte Data Lake Store par défaut de votre compte Analytique Data Lake.

	$dataLakeStoreName = "<The default Data Lake Store account name>"
	
	$localFolder = "C:\Tutorials\Downloads" # A temp location for the file. 
	$storageAccount = "adltutorials"  # Don't modify this value.
	$container = "adls-sample-data"  #Don't modify this value.

	# Create the temp location	
	New-Item -Path $localFolder -ItemType Directory -Force 

	# Download the sample file from Azure Blob storage
	$context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
	$blobs = Azure\Get-AzureStorageBlob -Container $container -Context $context
	$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

	# Upload the file to the default Data Lake Store account	
	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

Le script PowerShell suivant montre comment obtenir le nom de magasin Data Lake Store pour un compte Analytique Data Lake :


	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

>[AZURE.NOTE]Le portail Azure en version préliminaire fournit une interface utilisateur permettant de copier les fichiers de données exemple dans le compte Data Lake Store par défaut. Pour obtenir des instructions, consultez [Prise en main d’Analytique Data Lake à l’aide du portail Azure en version préliminaire](data-lake-analytics-get-started-portal.md#upload-data-to-the-default-data-lake-store-account).

Analytique Data Lake peut également accéder au stockage d’objets blobs Azure. Pour télécharger des données dans le stockage d’objets blobs Azure, consultez [Utilisation d’Azure PowerShell avec Azure Storage](storage-powershell-guide-full.md).

##Envoyer des travaux Analytique Data Lake

Les travaux Analytique Data Lake sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail Analytique Data Lake**

- Créez un fichier texte avec le script U-SQL suivant, puis enregistrez le fichier texte sur votre station de travail :

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**.
    
    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement. L’analyse de données Analytique Data Lake créera le dossier de sortie s’il n’existe pas encore.
	
	Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus. Par exemple :
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés. La syntaxe des fichiers stockés dans le compte de stockage Azure lié est la suivante :
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE]Les conteneurs d’objets blobs Azure avec autorisations d’accès aux objets blobs publics ou aux conteneurs publics ne sont pas pris en charge actuellement.
    
	
**Pour soumettre le travail**

1. Dans votre station de travail Windows, ouvrez PowerShell ISE.
2. Exécutez le script qui suit :

		$dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
		$usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"
		
		Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript 
		                
		While (($t = Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId).State -ne "Ended"){
			Write-Host "Job status: "$t.State"..."
			Start-Sleep -seconds 5
		}
		
		Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

	Dans le script, le fichier de script U-SQL est stocké à c:\\tutorials\\data-lake-analytics\\copyFile.usql. Mettez à jour le chemin d’accès en conséquence.
 
Une fois le travail terminé, vous pouvez utiliser les applets de commande suivantes pour répertorier le fichier et télécharger le fichier :
	
	$resourceGroupName = "<Resource Group Name>"
	$dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
	$destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"
	
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount
	
	Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"
	
	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## Voir aussi

- Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
- Pour voir une requête plus complexe, consultez [Analyse de journaux de site web à l’aide d’Analytique Data Lake Azure](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec les outils Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
- Pour les tâches de gestion, consultez la section [Gérer le service d’analyse Azure Data Lake à l’aide du portail Azure en version préliminaire](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

<!---HONumber=Nov15_HO1-->