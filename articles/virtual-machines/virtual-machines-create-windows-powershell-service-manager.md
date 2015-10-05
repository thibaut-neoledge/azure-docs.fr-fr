<properties
	pageTitle="Création et gestion d’une machine virtuelle Windows à l’aide d’Azure PowerShell | Microsoft Azure"
	description="Créez et gérez une machine virtuelle Windows avec Azure PowerShell à l’aide du modèle de déploiement classique."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="cynthn"/>

# Création et gestion d’une machine virtuelle Windows à l’aide d’Azure PowerShell

Cet article explique comment créer et gérer les machines virtuelles Windows à l’aide d’Azure PowerShell.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création d’une machine virtuelle Windows avec le modèle de déploiement classique. Vous pouvez également créer une machine virtuelle Windows avec le [modèle de déploiement Resource Manager](virtual-machines-deploy-rmtemplates-powershell.md).



## Configurer Azure PowerShell

Si vous avez déjà installé Azure PowerShell, vous devez disposer d'Azure PowerShell version 0.8.0 ou version ultérieure. Vous pouvez vérifier la version d'Azure PowerShell que vous avez installée en utilisant cette commande à l'invite de commandes Azure PowerShell :

	Get-Module azure | format-table version

Si ce n’est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l’aide des instructions décrites dans [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Ouvrez ensuite une invite de commande Azure PowerShell.

Pour commencer, vous devez vous connecter à Azure en utilisant cette commande :

	Add-AzureAccount

Spécifiez l'adresse de messagerie électronique et le mot de passe de votre compte Azure dans la boîte de dialogue de connexion à Microsoft Azure.

Ensuite, si vous avez plusieurs abonnements, vous devez sélectionner l’abonnement Azure à utiliser. Pour afficher la liste de vos abonnements actuels, exécutez cette commande :

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

À présent, remplacez tous les éléments entre guillemets, y compris les caractères < and >, par le nom de l’abonnement approprié, puis exécutez ces commandes :

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Créer une machine virtuelle

Dans un premier temps, vous avez besoin d’un compte de stockage. Vous pouvez afficher la liste de vos comptes de stockage actuels en utilisant cette commande :

	Get-AzureStorageAccount | sort Label | Select Label

Si vous n'avez pas déjà un compte de stockage, créez-en un. Vous devez choisir un nom unique qui contient uniquement des chiffres et des lettres en minuscules. Pour vérifier si le nom du compte de stockage est unique, utilisez cette commande :

	Test-AzureName -Storage <Proposed Storage account name>

Si cette commande renvoie « False », le nom proposé est unique.

Vous devrez spécifier l'emplacement d’un centre de données Azure au moment de créer un compte de stockage. Pour obtenir la liste des centres de données Azure, exécutez cette commande :

	Get-AzureLocation | sort Name | Select Name

À présent, créez et définissez le compte de stockage en utilisant les commandes suivantes. Entrez les noms du compte de stockage et remplacez tous les éléments entre guillemets, y compris les caractères < and >.

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Ensuite, vous avez besoin d’un service cloud. Si vous n'avez pas déjà un service cloud, vous devez en créer un. Vous devez choisir un nom unique qui contient uniquement des lettres, des chiffres et des tirets. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.

Par exemple, vous pouvez le nommer TestCS-*UniqueSequence*, où *UniqueSequence* est l’abréviation de votre entreprise. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez nommer le service cloud TestCS-Tailspin.

Vous pouvez vérifier si le nom est unique en utilisant cette commande Azure PowerShell :

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Créez le service cloud en utilisant cette commande :

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Ensuite, copiez ce jeu de commandes Azure PowerShell dans un éditeur de texte, tel que le Bloc-notes :

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Dans votre éditeur de texte, remplissez le nom de la machine virtuelle, le nom du service cloud et l'emplacement.

Enfin, copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit dans votre invite de commandes Azure PowerShell ouverte. Le jeu de commandes est exécuté comme une série de commandes Azure PowerShell. Vous êtes invité à entrer le nom et le mot de passe du compte administrateur local avant de créer votre machine virtuelle Azure. Voici un exemple de résultat de l’exécution du jeu de commandes :

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password


	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :

	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest

	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded

## Affichage des informations relatives à une machine virtuelle
Il s’agit d’une tâche de base que vous utiliserez souvent. Utilisez-la pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur cette dernière ou pour obtenir un résultat à stocker dans une variable.

Pour obtenir des informations sur la machine virtuelle, exécutez cette commande en remplaçant tous les éléments entre guillemets notamment les caractères < and > :

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pour stocker le résultat dans une variable $vm, exécutez :

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Connectez-vous à une machine virtuelle Windows

Exécutez ces commandes :

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]Vous pouvez obtenir le nom du service cloud et de la machine virtuelle à partir de l’affichage de la commande **Get-AzureVM**.

## Arrêter une machine virtuelle

Exécutez cette commande :

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilisez le paramètre **StayProvisioned** pour conserver l’adresse IP virtuelle du service cloud s’il s’agit de la dernière machine virtuelle de ce service. Si vous utilisez ce paramètre, vous êtes toujours facturé pour cette machine virtuelle.

## Démarrer une machine virtuelle

Exécutez cette commande :

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Association d’un disque de données
Cette tâche nécessite de réaliser quelques étapes. Tout d’abord, utilisez l’applet de commande **Add-AzureDataDisk** pour ajouter le disque à l’objet $vm. Utilisez ensuite l’applet de commande Update-AzureVM pour mettre à jour la configuration de la machine virtuelle.

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, cette même commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

Pour associer un disque existant, exécutez cette commande :

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage d’objets blob, exécutez la commande suivante :

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## Ressources supplémentaires

[Création d’une machine virtuelle Windows avec Resource Manager et Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Création d’une machine virtuelle Windows avec un modèle Resource Manager et Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

[Utiliser Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=Sept15_HO4-->