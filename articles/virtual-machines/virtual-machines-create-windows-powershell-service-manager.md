<properties 
	pageTitle="Créer une machine virtuelle Windows avec PowerShell et la gestion des services Azure" 
	description="Utilisez Azure PowerShell pour créer rapidement une machine virtuelle Windows." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/09/2015" 
	ms.author="josephd"/>

# Créer une machine virtuelle Windows avec PowerShell et la gestion des services Azure

Cette rubrique décrit comment créer et gérer une machine virtuelle Azure Windows à l’aide de la gestion des services Azure et de PowerShell.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Déployer et gérer des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Configurer Azure PowerShell

Si vous avez déjà installé Azure PowerShell, vous devez disposer d'Azure PowerShell version 0.8.0 ou version ultérieure. Vous pouvez vérifier la version d'Azure PowerShell que vous avez installée à l'aide de cette commande à l'invite de commandes Azure PowerShell.

	Get-Module azure | format-table version

Si ce n’est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l’aide des instructions décrites dans [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md). Ouvrez ensuite une invite de commande Azure PowerShell.

Tout d'abord, vous devez vous connecter à Azure avec cette commande.

	Add-AzureAccount

Spécifiez l'adresse de messagerie électronique et le mot de passe de votre compte Azure dans la boîte de dialogue de connexion à Microsoft Azure.

Ensuite, si vous avez plusieurs abonnements, vous devez sélectionner l’abonnement Azure à utiliser. Pour afficher une liste de vos abonnements en cours, exécutez la commande suivante.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

À présent, remplacez tous les éléments entre guillemets, y compris les caractères < and >, par le nom de l’abonnement approprié, puis exécutez les commandes suivantes.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Créer une machine virtuelle

Vous avez d’abord besoin d’un compte de stockage Vous pouvez afficher la liste de vos comptes de stockage avec cette commande.

	Get-AzureStorageAccount | sort Label | Select Label

Si vous n'en avez pas déjà, créez un compte de stockage. Vous devez choisir un nom unique qui contient uniquement des chiffres et des lettres en minuscules. Vous pouvez tester l'unicité du nom du compte de stockage avec cette commande.

	Test-AzureName -Storage <Proposed storage account name>

Si cette commande renvoie « False », le nom proposé est unique.

Vous devez spécifier l'emplacement d’un centre de données Azure lors de la création d’un compte de stockage. Pour obtenir une liste des centres de données Azure, exécutez cette commande.

	Get-AzureLocation | sort Name | Select Name

À présent, créez et définissez le compte de stockage avec les commandes suivantes. Entrez les noms du compte de stockage et remplacez tous les éléments entre guillemets, y compris les caractères < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Ensuite, vous avez besoin d’un service cloud. Si vous n'avez pas déjà un service cloud, vous devez en créer un. Vous devez choisir un nom unique qui contient uniquement des lettres, des chiffres et des tirets. Le premier et le dernier caractère du champ doit être une lettre ou un chiffre.

Par exemple, vous pouvez le nommer TestCS-*UniqueSequence*, où est *UniqueSequence* est l’abréviation de votre organisation. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez nommer le service cloud TestCS-Tailspin.

Vous pouvez tester l'unicité du nom avec la commande Azure PowerShell suivante.

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Créez le service cloud avec cette commande.

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Ensuite, copiez le jeu de commandes PowerShell suivant dans un éditeur de texte, comme le Bloc-notes.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Dans votre éditeur de texte, remplissez le nom de la machine virtuelle, le nom du service cloud et l'emplacement.

Enfin, copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Le jeu de commandes est exécuté comme une série de commandes PowerShell. Vous devez entrer le nom et le mot de passe de votre compte administrateur local avant de créer votre machine virtuelle Azure. Voici un exemple de résultat de l’exécution du jeu de commandes.

	PS C:> $vmName="PSTest"
	PS C:> $csName=" TestCS-Tailspin"
	PS C:> $locName="West US"
	PS C:> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
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
	
	PS C:> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
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

## Associer un disque de données
Cette tâche nécessite de réaliser quelques étapes. Vous utilisez d’abord l’applet de commande **Add-AzureDataDisk** pour ajouter le disque à l’objet $vm, puis vous utilisez l’applet de commande Update-AzureVM pour mettre à jour la configuration de la machine virtuelle.

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, cette même commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM <$vm> | Update-AzureVM

Pour associer un disque existant, exécutez cette commande :

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage d’objets blob, exécutez la commande suivante :

    Add-AzureDataDisk -ImportFrom -MediaLocation  "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" -DiskLabel "<main>" -LUN <0> | Update-AzureVM

## Ressources supplémentaires

[Création d’une machine virtuelle Windows avec Azure Resource Manager et PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Création d’une machine virtuelle Windows avec un modèle Resource Manager et PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Documentation sur les machines virtuelles](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

[Utilisation d'Azure PowerShell pour créer et préconfigurer des machines virtuelles basées sur Windows](virtual-machines-ps-create-preconfigure-windows-vms.md)

 

<!---HONumber=62-->