<properties 
   pageTitle="Prise en main de Data Lake Store | Azure" 
   description="Utiliser Azure PowerShell pour créer un compte Data Lake Store et effectuer des opérations de base" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="nitinme"/>

# Prise en main d'Azure Data Lake Store avec Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)

Apprenez à utiliser Azure PowerShell pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d'informations sur Data Lake Store, consultez [Vue d'ensemble de Data Lake Store](data-lake-store-overview.md).

## Configuration requise

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/fr-FR/pricing/free-trial/).
- **Activez votre abonnement Azure** pour la version d'évaluation publique de Data Lake Store. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).
- **Azure PowerShell 1.0**. Vous pouvez l'installer depuis [ici](https://github.com/MicrosoftBigData/AzureDataLake/releases/download/AzurePowerShell_2015_10_30/AzurePowerShell.msi).

## Créer un compte Azure Data Lake Store

1. Sur votre bureau, ouvrez une nouvelle fenêtre Azure PowerShell et entrez l'extrait de code suivant pour vous connecter à votre compte Azure, définir l'abonnement et inscrire le fournisseur Data Lake Store. Lorsque vous êtes invité à vous connecter, vérifiez que vous vous connectez en tant qu'administrateur/propriétaire de l'abonnement :

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>
        
		# Register for Azure Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore" 


2. Un compte Azure Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Créer un groupe de ressources Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")

2. Créer un compte Azure Data Lake Store. Le nom que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Créer un compte Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure Data Lake Store")

3. Vérifiez que le compte a bien été créé.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	Le résultat doit être **True**.

## Créer des structures de répertoires dans votre Azure Data Lake Store

Vous pouvez créer des répertoires sous votre compte Azure Data Lake Store pour gérer et stocker des données.

1. Spécifiez un répertoire racine.

		$myrootdir = "/"

2. Créez un répertoire appelé **mynewdirectory** sous la racine spécifiée.

		New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Vérifiez que le répertoire a bien été créé.

		Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

	Un résultat similaire à ce qui suit s'affiche :

	![Vérifier un répertoire](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifier un répertoire")


## Télécharger des données sur votre Azure Data Lake Store

Vous pouvez télécharger vos données sur Data Lake Store directement à la racine ou dans un répertoire que vous avez créé dans le compte. Les extraits de code ci-dessous montrent comment télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à télécharger, vous pouvez récupérer le dossier **Ambulance Data** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/ProjectKona/tree/master/SQLIPSamples/SampleData/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\\sampledata.

	Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Renommer, télécharger et supprimer des données de votre Data Lake Store

Utilisez la commande suivante pour renommer un fichier :

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Utilisez la commande suivante pour télécharger un fichier :

	Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Utilisez la commande suivante pour supprimer un fichier :

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv 
	
Lorsque vous y êtes invité, entrez **Y** pour supprimer l'élément. Si vous avez plusieurs fichiers à supprimer, vous pouvez fournir tous les chemins d'accès séparés par des virgules.

	Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Supprimer votre compte Azure Data Lake Store

Utilisez la commande suivante pour supprimer votre compte Data Lake Store.

	Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Lorsque vous y êtes invité, entrez **Y** pour supprimer le compte.


## Autres méthodes de création d'un compte Data Lake Store

- [Prise en main de Data Lake Store avec le portail](data-lake-store-get-started-portal.md)
- [Prise en main de Data Lake Store avec le Kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)


## Étapes suivantes

- [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
- [Utiliser Azure Data Lake Analytics avec Data Lake Store](data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=Nov15_HO2-->