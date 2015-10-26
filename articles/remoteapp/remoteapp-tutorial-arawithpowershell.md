<properties
   pageTitle="Prise en main d’Azure RemoteApp avec Powershell"
   description="Apprenez à prendre en main Azure RemoteApp avec Powershell"
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/12/2015"
   ms.author="guscatal;spatnaik;elizapo"/>



# Prise en main d’Azure RemoteApp avec Powershell
=====================================


## Récupération des applets de commande 
-------------
Pour commencer, téléchargez les applets de commande Azure Powershell [ici](http://go.microsoft.com/?linkid=9811175), qui incluent des applets de commande RemoteApp.

Consultez l'aide pour l'applet de commande Azure RemoteApp [ici](https://msdn.microsoft.com/library/mt428031.aspx).

## Configuration des applets de commande Azure pour utiliser votre abonnement
------------------
Suivez [ce guide](../powershell-install-configure.md) pour apprendre à utiliser les applets de commande sur votre abonnement Azure.

## Création d'une collection cloud
--------------------
Exécutez simplement la commande suivante :

    New-AzureRemoteAppCollection -Collectionname RAppO365Col1 -ImageName "Office 365 ProPlus (Subscription required)" -Plan Basic -Location "West US" - Description "Office 365 Collection."

La commande ci-dessus publie automatiquement les applications Microsoft Office 365 (Excel, OneNote, Outlook, PowerPoint, Visio et Word).

La création de la collection peut prendre 30 minutes ou plus. Par conséquent, cette commande renvoie un ID de suivi que vous pouvez utiliser de la manière suivante :


    Get-AzureRemoteAppOperationResult -TrackingId xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

Une fois la collection terminée, vous pouvez ajouter des utilisateurs à la collection avec la commande suivante :

    Add-AzureRemoteAppUser -CollectionName RAppO365Col1 -Type microsoftAccount -UserUpn someone@domain.com

Vous avez terminé ! Cet utilisateur doit pouvoir se connecter à l’application à l’aide du client Azure RemoteApp disponible [ici](https://www.remoteapp.windowsazure.com/).

## Applets de commande disponibles
Nous proposons de nombreuses autres commandes. Leur documentation sera disponible prochainement :

Applets de commande de base de la collection RemoteApp :

- New-AzureRemoteAppCollection
- Get-AzureRemoteAppCollection
- Set-AzureRemoteAppCollection
- Update-AzureRemoteAppCollection
- Remove-AzureRemoteAppCollection
- Add-AzureRemoteAppUser
- Get-AzureRemoteAppUser
- Remove-AzureRemoteAppUser
- Get-AzureRemoteAppSession
- Disconnect-AzureRemoteAppSession
- Invoke-AzureRemoteAppSessionLogoff
- Send-AzureRemoteAppSessionMessage
- Get-AzureRemoteAppProgram
- Get-AzureRemoteAppStartMenuProgram
- Publish-AzureRemoteAppProgram
- Unpublish-AzureRemoteAppProgram
- Get-AzureRemoteAppCollectionUsageDetails
- Get-AzureRemoteAppCollectionUsageSummary
- Get-AzureRemoteAppPlan

Applets de commande du réseau virtuel RemoteApp :

- New-AzureRemoteAppVNet
- Get-AzureRemoteAppVNet
- Set-AzureRemoteAppVNet
- Remove-AzureRemoteAppVNet
- Get-AzureRemoteAppVpnDevice
- Get-- AzureRemoteAppVpnDeviceConfigScript
- Reset-AzureRemoteAppVpnSharedKey

Applets de commande de l’image du modèle RemoteApp :

- New-AzureRemoteAppTemplateImage
- Get-AzureRemoteAppTemplateImage
- Rename-AzureRemoteAppTemplateImage
- Remove-AzureRemoteAppTemplateImage

Autres applets de commande RemoteApp :

- Get-AzureRemoteAppLocation
- Get-AzureRemoteAppWorkspace
- Set-AzureRemoteAppWorkspace
- Get-AzureRemoteAppOperationResult
 

<!---HONumber=Oct15_HO3-->