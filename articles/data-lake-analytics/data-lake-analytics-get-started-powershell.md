---
title: "Prise en main d’Azure Data Lake Analytics à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Découvrez comment utiliser Azure PowerShell pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et envoyer le travail. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 0994765e37dd8ee1fa6a639a2ed60c913cb170fe
ms.openlocfilehash: a1244ee080d333338b1f0d9e49d1776822f535ed


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Prise en main du service Analytique Data Lake Azure à l’aide d’Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser le Kit de développement logiciel (SDK) Azure PowerShell pour créer des comptes Azure Data Lake Analytics, définir des travaux Data Lake Analytics dans [U-SQL](data-lake-analytics-u-sql-get-started.md)et envoyer des travaux à des comptes Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertir en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l'aide d'autres outils pris en charge, cliquez sur les onglets en haut de cette section.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d’un compte Analytique Data Lake avant de pouvoir exécuter un travail quelconque. Pour créer un compte Analytique Data Lake, vous devez spécifier les éléments suivants :

* **Groupe de ressources Azure**: un compte Data Lake Analytics doit être créé au sein d'un groupe de ressources Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.  

    Pour répertorier les groupes de ressources dans votre abonnement :

        Get-AzureRmResourceGroup

    Pour créer un groupe de ressources :

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Nom du compte Analytique Data Lake**
* **Emplacement**: un des centres de données Azure prenant en charge Data Lake Analytics.
* **Compte Data Lake par défaut**: chaque compte Data Lake Analytics possède un compte Data Lake par défaut.

    Pour créer un compte Azure Data Lake :

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

  > [!NOTE]
  > Le nom de compte Data Lake doit contenir uniquement des lettres minuscules et des chiffres.
  >
  >

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

## <a name="upload-data-to-data-lake"></a>Téléchargement de données vers Data Lake
Dans ce didacticiel, vous allez traiter des journaux de recherche.  Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure.

Un exemple de fichier journal de recherche a été copié vers un conteneur d’objets blobs Azure public. Utilisez le script PowerShell suivant pour télécharger le fichier sur votre station de travail, puis téléchargez le fichier dans le compte Data Lake Store par défaut de votre compte Analytique Data Lake.

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
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
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeAccount
    echo $dataLakeStoreName

> [!NOTE]
> Le portail Azure fournit une interface utilisateur permettant de copier les fichiers d’exemples de données dans le compte Data Lake Store par défaut. Pour obtenir des instructions, consultez [Prise en main d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md#prepare-source-data).
>
>

Analytique Data Lake peut également accéder au stockage d’objets blobs Azure.  Pour télécharger des données dans le stockage d’objets blobs Azure, consultez [Utilisation d’Azure PowerShell avec Azure Storage](../storage/storage-powershell-guide-full.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envoyer des travaux Analytique Data Lake
Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail Analytique Data Lake**

* Créez un fichier texte avec le script U-SQL suivant, puis enregistrez le fichier texte sur votre station de travail :

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

    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  L’analyse de données Analytique Data Lake créera le dossier de sortie s’il n’existe pas encore.

    Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple :

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans des comptes Azure Storage liés est la suivante :

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.    
  >
  >

**Pour soumettre le travail**

1. Dans votre station de travail Windows, ouvrez PowerShell ISE.
2. Exécutez le script qui suit :

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"

        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    Dans le script, le fichier de script U-SQL est stocké à c:\tutorials\data-lake-analytics\copyFile.usql. Mettez à jour le chemin d’accès en conséquence.

Une fois le travail terminé, vous pouvez utiliser les applets de commande suivantes pour répertorier le fichier et télécharger le fichier :

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Voir aussi
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour obtenir une vue d’ensemble de Data Lake Analytics, consultez [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md).



<!--HONumber=Dec16_HO2-->


