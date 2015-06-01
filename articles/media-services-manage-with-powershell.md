<properties 
	pageTitle="Gérer des comptes Azure Media Services avec PowerShell" 
	description="Apprenez à gérer des comptes Azure Media Services avec les cmdlets PowerShell." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/14/2015" 
	ms.author="juliako"/>


# Gérer des comptes Azure Media Services avec PowerShell

## Vue d'ensemble 

Cet article vous explique comment utiliser les cmdlets PowerShell pour gérer des comptes Azure Media Services.

>[AZURE.NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Version d'évaluation gratuite d'Azure</a>.

## Installer les cmdlets Microsoft Azure PowerShell

Pour installer les dernières cmdlets PowerShell Azure, consultez la page [Installation et configuration d'Azure PowerShell](powershell-install-configure.md)

## Sélectionner l'abonnement Azure

Une fois les cmdlets PowerShell installées et configurées, vous devez spécifier l'abonnement dans lequel vous souhaitez travailler. 

Pour obtenir la liste des abonnements disponibles, exécutez la cmdlet suivante :

	PS C:> Get-AzureSubscription

Ensuite, sélectionnez-en un en exécutant :

	PS C:> Select-AzureSubscription "TestSubscription"

 
## Obtenir le nom du compte de stockage

Azure Media Services utilise Azure Storage pour stocker le contenu multimédia. Lorsque vous créez un compte Media Services, vous devez l'associer à un compte de stockage. Le compte de stockage doit appartenir au même abonnement que celui que vous prévoyez d'utiliser pour votre compte Media Services. 

Dans cet exemple, un compte de stockage existant est utilisé. La cmdlet [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) permet d'obtenir les comptes de stockage au sein de l'abonnement actuel. Obtenez le nom (StorageAccountName) du compte de stockage auquel vous souhaitez associer votre compte Media Services actuel.

	StorageAccountDescription :
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

## Créer un compte Media Services

Pour créer un compte Azure Media Services, utilisez la cmdlet [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) en fournissant le nom du compte Media Services, l'emplacement du centre de données où il sera créé et le nom du compte de stockage. 


	PS C:> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

## Obtenir les comptes Media Services

Une fois que vous avez créé un ou plusieurs comptes Media Services, vous pouvez répertorier les informations associées à l'aide de [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)

	
	PS C:> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

En fournissant le paramètre Name, vous obtiendrez des informations plus détaillées, notamment les clés des comptes.

	PS C:> Get-AzureMediaServicesAccount -Name amstestaccount001

## Régénérer les clés d'accès Media Services

Si vous souhaitez mettre à jour la clé d'accès primaire ou secondaire Media Services, utilisez [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx). 
Vous devez fournir le nom du compte et indiquer quelle clé vous souhaitez régénérer (primaire ou secondaire). 

Spécifiez un commutateur -Force si vous ne souhaitez pas que PowerShell pose de questions de confirmation.

	PS C:> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

## Supprimer le compte Media Services

Lorsque vous êtes prêt à supprimer le compte Azure Media, utilisez [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force



<!--HONumber=52-->