---
title: Utiliser les applets de commande PowerShell avec Azure RemoteApp | Microsoft Docs
description: "Apprenez à utiliser les applets de commande Windows PowerShell dans Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 7d3d5ded-6f73-4de6-a8ac-c1d622e842a2
ms.service: remoteapp
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: c40545833da86426d3e71955b8eb8627db3c1e4b
ms.openlocfilehash: 4f2b61e7c97beb7556bffca89c3608aaee831ddc


---
# <a name="use-windows-powershell-cmdlets-with-azure-remoteapp"></a>Utiliser les applets de commande Windows PowerShell avec Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp n’est plus disponible. Pour plus d’informations, lisez [l’annonce](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

 Vous pouvez utiliser les applets de commande PowerShell pour Azure RemoteApp afin d’administrer et de gérer vos collections. Utilisez les informations suivantes pour commencer.

## <a name="get-the-cmdlets"></a>Récupération des applets de commande
- - -
Téléchargez d’abord les applets de commande Azure PowerShell [ici](http://go.microsoft.com/?linkid=9811175); les applets de commande RemoteApp sont incluses. 

Consultez [l’aide sur les applets de commande Azure RemoteApp](https://msdn.microsoft.com/library/mt428031.aspx).

## <a name="configure-azure-cmdlets-to-use-your-subscription"></a>Configuration des applets de commande Azure pour utiliser votre abonnement
- - -
Suivez [ce guide](/powershell/azureps-cmdlets-docs) pour apprendre à utiliser les applets de commande sur votre abonnement Azure.

Vous pouvez utiliser ces étapes pour démarrer rapidement :

1. Téléchargez et installez les applets de commande [Azure PowerShell](http://go.microsoft.com/?linkid=9811175).
2. Lancez Microsoft Azure PowerShell.
3. Exécutez **Add-AzureAccount** pour vous authentifier auprès de votre abonnement Azure. À l’invite, entrez le nom d’utilisateur et le mot de passe que vous utilisez pour vous connecter au portail Azure.  
4. Exécutez **Get-AzureSubscription** pour répertorier les abonnements associés à votre compte utilisateur. 
5. Exécutez **Select-AzureSubscription** et spécifiez le nom ou l’ID d’abonnement à utiliser dans la console PowerShell.

Félicitations, votre console Azure PowerShell est configurée et opérationnelle. N'oubliez pas que vous devez répéter les étapes 2 à 5 chaque fois que vous démarrez la console Azure PowerShell.  

## <a name="create-a-cloud-collection"></a>Création d'une collection cloud
- - -
Exécutez simplement la commande suivante :

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

La commande ci-dessus publie automatiquement les applications Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio et Word).

La création de la collection peut prendre 30 minutes ou plus. Par conséquent, cette commande renvoie un ID de suivi que vous pouvez utiliser de la manière suivante :

    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Une fois la collection terminée, vous pouvez ajouter des utilisateurs à la collection avec la commande suivante :

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Vous avez terminé ! Cet utilisateur doit pouvoir se connecter à l’application à l’aide du client Azure RemoteApp disponible [ici](https://www.remoteapp.windowsazure.com/).

## <a name="available-cmdlets"></a>Applets de commande disponibles
Nous proposons de nombreuses autres commandes. Leur documentation sera disponible prochainement :

Applets de commande de base de la collection RemoteApp : 

* New-AzureRemoteAppCollection
* Get-AzureRemoteAppCollection
* Set-AzureRemoteAppCollection
* Update-AzureRemoteAppCollection
* Remove-AzureRemoteAppCollection
* Add-AzureRemoteAppUser
* Get-AzureRemoteAppUser
* Remove-AzureRemoteAppUser
* Get-AzureRemoteAppSession
* Disconnect-AzureRemoteAppSession
* Invoke-AzureRemoteAppSessionLogoff
* Send-AzureRemoteAppSessionMessage
* Get-AzureRemoteAppProgram
* Get-AzureRemoteAppStartMenuProgram
* Publish-AzureRemoteAppProgram
* Unpublish-AzureRemoteAppProgram
* Get-AzureRemoteAppCollectionUsageDetails
* Get-AzureRemoteAppCollectionUsageSummary
* Get-AzureRemoteAppPlan

Applets de commande du réseau virtuel RemoteApp :

* New-AzureRemoteAppVNet
* Get-AzureRemoteAppVNet
* Set-AzureRemoteAppVNet
* Remove-AzureRemoteAppVNet
* Get-AzureRemoteAppVpnDevice
* Get-- AzureRemoteAppVpnDeviceConfigScript
* Reset-AzureRemoteAppVpnSharedKey

Applets de commande de l’image du modèle RemoteApp :

* New-AzureRemoteAppTemplateImage
* Get-AzureRemoteAppTemplateImage
* Rename-AzureRemoteAppTemplateImage
* Remove-AzureRemoteAppTemplateImage

Autres applets de commande RemoteApp :

* Get-AzureRemoteAppLocation
* Get-AzureRemoteAppWorkspace
* Set-AzureRemoteAppWorkspace
* Get-AzureRemoteAppOperationResult




<!--HONumber=Feb17_HO3-->


