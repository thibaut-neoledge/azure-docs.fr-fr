<properties
   pageTitle="Configuration de PowerShell pour créer une machine virtuelle pour Marketplace | Microsoft Azure"
   description="Instructions pour configurer Azure PowerShell et l’utiliser comme processus facultatif pour créer des images de machines virtuelles à déployer et à vendre sur Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# Configurer Azure PowerShell pour créer une offre pour Azure Marketplace
Pour plus d’informations sur la configuration de PowerShell dans Azure, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Une approche simple consiste à utiliser la méthode de certificat, qui télécharge et importe le certificat nécessaire à l’authentification. Pour obtenir le certificat requis, utilisez l’applet de commande **Get-AzurePublishSettingsFile** . Enregistrez le fichier lorsque vous y êtes invité. Pour importer le certificat dans une session PowerShell, utilisez l’applet de commande **Import-AzurePublishSettingsFile**.

Pour configurer et stocker les paramètres d’abonnement Microsoft Azure communs pour la session PowerShell, utilisez les applets de commande **Set-AzureSubscription** et **Select-AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

La première commande associe un compte de stockage par défaut à l’abonnement (nécessaire pour certaines opérations d’approvisionnement de la machine virtuelle). La seconde définit l’abonnement comme abonnement actif (reconnu par les autres applets de commande).

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)
- [Création d’une image de machine virtuelle pour Azure Marketplace](marketplace-publishing-vm-image-creation.md)

<!---HONumber=AcomDC_0211_2016-->