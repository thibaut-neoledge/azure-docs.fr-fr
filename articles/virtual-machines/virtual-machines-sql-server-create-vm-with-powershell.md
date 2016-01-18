<properties
	pageTitle="Création d’une machine virtuelle SQL Server dans PowerShell | Microsoft Azure"
	description="Fournit une procédure et des scripts PowerShell pour la création d’une machine virtuelle Azure à l’aide des images de la galerie de machines virtuelles SQL Server."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/06/2015"
	ms.author="jroth" />

# Créer une machine virtuelle SQL Server dans Azure (PowerShell)

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)


## Vue d’ensemble

Cet article explique comment créer une machine virtuelle SQL Server dans Azure à l'aide des applets de commande PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


## Installation et configuration de PowerShell

1. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).

2. [Installez les applets de commande de la version la plus récente d’Azure PowerShell](../powershell-install-configure.md/#how-to-install-azure-powershell).

3. [Connectez PowerShell à votre abonnement Azure](../powershell-install-configure.md/#how-to-connect-to-your-subscription).

## Déterminer votre région Azure cible

Votre machine virtuelle SQL Server est hébergée dans un service cloud qui se trouve dans une région Azure spécifique. Les étapes suivantes vous aident à déterminer votre région, le compte de stockage et le service cloud qui seront utilisés pour le reste du didacticiel.

1. Déterminez le centre de données que vous souhaitez utiliser pour héberger votre machine virtuelle SQL Server. Les commandes PowerShell suivantes affichent le détail des régions disponibles en terminant par une liste récapitulative.

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  Une fois que vous avez identifié l’emplacement par défaut, définissez une variable nommée **$dcLocation** pour cette région.

		$dcLocation = "<region name>"

## Configuration de votre compte d'abonnement et de stockage

1. Déterminez l’abonnement Azure que vous allez utiliser pour la nouvelle machine virtuelle.

		(Get-AzureSubscription).SubscriptionName

1. Attribuez la variable **$subscr** à votre abonnement Azure cible. Définissez cet abonnement comme votre abonnement Azure actuel.

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. Recherchez ensuite des comptes de stockage. Le script suivant affiche tous les comptes de stockage qui existent dans votre région choisie :

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE]Si vous avez besoin d’un compte de stockage, commencez par utiliser la commande New-AzureStorageAccount pour créer un nom de compte de stockage (intégralement en minuscules), comme dans l’exemple suivant : **New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation**

1. Attribuez le nom de compte de stockage cible à **$staccount**. Utilisez ensuite **Set-AzureSubscription** pour définir l’abonnement et le compte de stockage actif.

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## Sélectionner une image de machine virtuelle SQL Server

1. Consultez la liste des images de machines virtuelles SQL Server disponibles dans la galerie. Ces images ont toutes la propriété **ImageFamily** qui commence par « SQL ». La requête suivante affiche la famille d’images disponible pour vous, pour laquelle SQL Server est pré-installé.

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Il est possible que plusieurs images soient publiées dans la famille d’images de machine virtuelle que vous avez trouvée. Utilisez le script suivant pour rechercher le nom de la dernière image de machine virtuelle publiée pour la famille d’images que vous avez sélectionnée (comme par exemple, **SQL Server Enterprise 2014 SP1 sur Windows Server 2012 R2**) :

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## Créer la machine virtuelle

Pour finir, créez la machine virtuelle avec PowerShell :

1. Créez un service cloud pour héberger la nouvelle machine virtuelle. Notez que vous pouvez également utiliser un service cloud existant. Créez une variable **$svcname** portant le nom abrégé du service cloud.

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Indiquez le nom et la taille de la machine virtuelle. Pour plus d’informations sur les tailles de machines virtuelles, voir [Tailles de machines virtuelles pour Azure](virtual-machines-size-specs.md).

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Indiquez le compte d’administrateur local et son mot de passe.

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Exécutez le script suivant pour créer la machine virtuelle.

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE]Pour consulter d’autres options de configuration ainsi que des explications supplémentaires, lisez la section **Création de votre jeu de commandes** sur la page [Utilisation d’Azure PowerShell pour créer et pré-configurer des machines virtuelles basées sur Windows](virtual-machines-ps-create-preconfigure-windows-vms.md).

## Exemple de script PowerShell

Le script suivant est un exemple de script complet qui crée une machine virtuelle **SQL Server Enterprise 2014 SP1 sur Windows Server 2012 R2**. Si vous utilisez ce script, vous devez personnaliser les variables initiales en vous basant sur les étapes précédentes de cette rubrique.

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5"

	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1


## Connexion avec le Bureau à distance

1. Créez les fichiers .RDP dans le dossier Documents de l’utilisateur actuel pour démarrer ces machines virtuelles afin de terminer l’installation :

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Dans le répertoire Documents, exécutez le fichier RDP. Connectez-vous avec le nom d’utilisateur et le mot de passe du compte d’administrateur fournis précédemment (par exemple, si votre nom d’utilisateur était VMAdmin, entrez « \\VMAdmin » comme nom d’utilisateur, puis entrez le mot de passe).

		.\vm1.rdp

## Terminer la configuration de la machine virtuelle SQL Server pour l’accès distant

Une fois que vous vous êtes connecté à la machine à l’aide du Bureau à distance, configurez SQL Server en vous basant sur les instructions de la [procédure de configuration de la connectivité SQL Server dans une machine virtuelle Azure](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## Étapes suivantes

Vous trouverez des instructions supplémentaires sur l’approvisionnement des machines virtuelles avec PowerShell dans la [documentation sur les machines virtuelles](virtual-machines-ps-create-preconfigure-windows-vms.md). Pour obtenir d’autres scripts relatifs à SQL Server et Premium Storage, consultez [Utilisation du stockage Premium Azure avec SQL Server sur des machines virtuelles](virtual-machines-sql-server-use-premium-storage.md).

Dans de nombreux cas, l’étape suivante consiste à migrer vos bases de données vers cette nouvelle machine virtuelle SQL Server. Pour obtenir de l’aide sur la migration des bases de données, consultez [Migration d’une base de données vers un serveur SQL Server sur une machine virtuelle Azure](virtual-machines-migrate-onpremises-database.md).

Si vous voulez également savoir comment suivre cette procédure en passant par le portail Azure Classic, consultez [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-provision-sql-server.md).

Outre ces ressources, nous vous recommandons de consulter [les autres rubriques liées à l’exécution de SQL Server dans Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_0107_2016-->