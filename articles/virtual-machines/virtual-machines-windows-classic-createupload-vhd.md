<properties
	pageTitle="Créer et télécharger une image Windows Server à l’aide de PowerShell | Microsoft Azure"
	description="Découvrez comment créer et télécharger une image Windows Server (VHD) généralisée à l’aide du modèle de déploiement classique et d’Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="cynthn"/>

# Création et téléchargement d’un disque dur virtuel Windows Server dans Azure

Cet article vous montre comment télécharger votre propre image de machine virtuelle généralisée afin de l’utiliser comme disque dur virtuel (VHD) pour créer des machines virtuelles. Pour en savoir plus sur les disques et les disques durs virtuels dans Microsoft Azure, consultez la rubrique [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]. Vous pouvez également [capturer](virtual-machines-windows-capture-image.md) et [télécharger](virtual-machines-windows-upload-image.md) une machine virtuelle à l'aide du modèle Resource Manager.

## Composants requis

Cet article suppose que vous disposez de :

1. **Un abonnement Azure** : si vous n’en avez pas, vous pouvez [ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F).

2. **[Microsoft Azure PowerShell](../powershell-install-configure.md)** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement.

3. **Un fichier .VHD** : système d’exploitation Windows pris en charge stocké dans un fichier .vhd et associé à une machine virtuelle.

> [AZURE.IMPORTANT] Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de la [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Pour plus de détail, voir [ce billet de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Étape 1 : Préparer le disque dur virtuel 

Avant de télécharger le disque dur virtuel vers Azure, vous devez le généraliser à l’aide de l’outil Sysprep. Cette opération prépare le disque dur virtuel à utiliser en tant qu’image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

Depuis la machine virtuelle sur laquelle le système d’exploitation a été installé, effectuez la procédure suivante :

1. Connectez-vous au système d’exploitation.

2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

	![Ouvrir une fenêtre d’invite de commandes](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	La boîte de dialogue **Outil de préparation système** apparaît.

	![Démarrer Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  Dans **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case à cocher **Généraliser** est activée.

5.  Dans **Shutdown Options**, sélectionnez **Shutdown**.

6.  Cliquez sur **OK**.

## Étape 2 : Créer un compte de stockage et un conteneur

Vous avez besoin d’un compte de stockage dans Azure afin d’avoir un emplacement pour télécharger le fichier .vhd. Cette étape vous montre comment créer un compte, ou obtenir les informations dont vous avez besoin d’un compte existant. Remplacez les variables entre &lsaquo; crochets &rsaquo; par vos propres informations.

1. Connexion

		Add-AzureAccount

1. Définissez votre abonnement Azure.

    	Select-AzureSubscription -SubscriptionName <SubscriptionName> 

2. Créez un nouveau compte de stockage. Le nom du compte de stockage devrait être unique et composé de 3 à 24 caractères. Vous pouvez utiliser n’importe quelle combinaison de lettres et de chiffres. Vous devez également spécifier un emplacement comme « États-Unis de l'Est »
    	
		New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>

3. Définissez le nouveau compte de stockage comme compte par défaut.
    	
		Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>

4. Créez un conteneur.

    	New-AzureStorageContainer -Name <ContainerName> -Permission Off

 

## Étape 3 : téléchargement du fichier .vhd

Utilisez l’applet de commande [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) pour télécharger le fichier VHD.

Dans la fenêtre Azure PowerShell que vous avez utilisée à l’étape précédente, tapez la commande suivante et remplacez les variables entre &lsaquo; crochets &rsaquo; par vos propres informations.

		Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>


## Étape 4 : ajout de l’image à votre liste d’images personnalisées

Utilisez l’applet de commande [Add-AzureVMImage]) (https://msdn.microsoft.com/library/mt589167.aspx) pour ajouter l’image à la liste de vos images personnalisées.

		Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"


## Étapes suivantes

Vous pouvez à présent [créer une machine virtuelle personnalisée](virtual-machines-windows-classic-createportal.md) à l’aide de l’image que vous avez téléchargée.

<!---HONumber=AcomDC_0727_2016-->