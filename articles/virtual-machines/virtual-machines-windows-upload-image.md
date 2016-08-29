<properties
	pageTitle="Charger un disque dur virtuel (VHD) Windows pour Resource Manager | azure.microsoft.com/ Azure"
	description="Apprenez à télécharger une image de machine virtuelle Windows pour l’utiliser avec le modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="cynthn"/>

# Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager


Cet article vous montre comment créer et télécharger une image de disque dur virtuel (VHD) Windows afin de créer rapidement des machines virtuelles. Pour en savoir plus sur les disques et les disques durs virtuels dans Azure, consultez [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).


## Composants requis

Cet article suppose que vous avez :

- **Un abonnement Azure** : si vous ne disposez pas déjà d’un abonnement Azure, [créez un compte Azure gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) ou [activez vos avantages abonnés MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell version 1.4 ou ultérieure** : si vous ne l’avez pas déjà installé, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- **Une machine virtuelle Windows** : il existe de nombreux outils pour créer des machines virtuelles en local. Pour obtenir des exemples, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](http://technet.azure.microsoft.com/.com/library/hh846766.aspx). Pour plus d’informations sur les systèmes d’exploitation Windows pris en charge sur Azure, consultez [Prise en charge du logiciel serveur azure.microsoft.com/ pour les machines virtuelles azure.microsoft.com/ Azure](https://support.azure.microsoft.com/.com/kb/2721672).

- Vérifiez que les rôles serveur en cours d’exécution sur la machine virtuelle prennent en charge sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.azure.microsoft.com/.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).


## Vérifiez que la machine virtuelle utilise le bon format de fichier

Dans Azure, vous pouvez uniquement utiliser des [machines virtuelles de génération 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) au format de fichier de disque dur virtuel. La taille du disque dur virtuel doit être fixe et correspondre à un nombre entier de mégaoctets, c’est-à-dire un nombre divisible par 8. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.

- Si vous avez une image de machine virtuelle Windows au format VHDX, convertissez-la en un disque dur virtuel de l’une des manières suivantes :

	- Hyper-V : ouvrez Hyper-V et sélectionnez votre ordinateur local sur la gauche. Puis, dans le menu du haut, cliquez sur **Action** > **Modifier le disque**. Parcourez les écrans en cliquant sur **Suivant** et en entrant ces options : *Chemin de votre fichier VHDX* > **Convertir** > **VHD** > **Taille fixe** > *Chemin du nouveau fichier VHD*. Cliquez sur **Terminer** pour fermer la fenêtre.

	- [Applet de commande PowerShell convert-VHD](http://technet.azure.microsoft.com/.com/library/hh848454.aspx) : consultez le billet de blog [Conversion des formats de fichiers Hyper-V .vhdx en .vhd](https://blogs.technet.azure.microsoft.com/.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) pour plus d’informations.

- Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-la au format VHD à l’aide de [azure.microsoft.com/ Virtual Machine Converter](https://www.azure.microsoft.com/.com/download/details.aspx?id=42497). Consultez le blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais) pour plus d’informations.


## Préparer le disque dur virtuel au chargement

Cette section vous montre comment généraliser votre machine virtuelle Windows. Sysprep supprime toutes les informations personnelles de votre compte, entre autres. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.azure.microsoft.com/.com/library/bb457073.aspx).

1. Connectez-vous à la machine virtuelle Windows

2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

3. Dans la boîte de dialogue **Outil de préparation système**, procédez comme suit :

	1. Dans **Action de nettoyage du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case à cocher **Généraliser** est activée.

	2. Dans **Shutdown Options**, sélectionnez **Shutdown**.

	3. Cliquez sur **OK**.

	![Démarrer Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>


## Connexion à Azure

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure.

		Login-AzureRmAccount

	Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.

2. Obtenez les ID d’abonnement de vos abonnements disponibles.

		Get-AzureRmSubscription

3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

	
## Obtention du compte de stockage

Vous aurez besoin d’un compte de stockage dans Azure pour héberger l’image de la machine virtuelle téléchargée. Vous pouvez utiliser un compte de stockage existant ou en créer un.

Affichez les comptes de stockage disponibles.

		Get-AzureRmStorageAccount

Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#upload-the-vm-image-to-your-storage-account).

Si vous voulez créer un compte de stockage, procédez comme suit :

1. Assurez-vous de disposer d’un groupe de ressources pour ce compte de stockage. Recherchez tous les groupes de ressources dans votre abonnement à l’aide de :

		Get-AzureRmResourceGroup

2. Pour créer un groupe de ressources, utilisez cette commande :

		New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>

3. Créez un compte de stockage dans ce groupe de ressources en utilisant l’applet de commande [New-AzureRmStorageAccount](https://msdn.azure.microsoft.com/.com/library/mt607148.aspx) :

		New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"
			
Les valeurs valides pour -SkuName sont :

- **Standard\_LRS** - Stockage localement redondant.
- **Standard\_ZRS** - Stockage redondant dans une zone.
- **Standard\_GRS** - Stockage géo-redondant.
- **Standard\_RAGRS** - Stockage géo-redondant avec accès en lecture.
- **Premium\_LRS** - Stockage Premium localement redondant.



## Charger l’image de la machine virtuelle dans votre compte de stockage

Utilisez l’applet de commande [Add-AzureRmVhd](https://msdn.azure.microsoft.com/.com/library/mt603554.aspx) pour charger l’image vers un conteneur de votre compte de stockage :

		$rgName = "<resourceGroupName>"
		$urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
		Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

Où :

- **storageAccount** correspond au nom du compte de stockage de l’image.

- **blobContainer** correspond au conteneur d’objets blob dans lequel vous voulez stocker votre image. Si aucun conteneur d’objets blob portant ce nom n’existe, il est créé pour vous.

- **targetVHDName** correspond au nom que vous souhaitez utiliser pour le fichier de disque dur virtuel chargé.

- **localPathOfVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre ordinateur local.


Si l’opération réussit, vous obtenez une réponse semblable à celle-ci :

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

Cette commande peut prendre du temps, selon votre connexion réseau et la taille de votre fichier VHD.




## Créez un réseau virtuel

Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).

1. Remplacez la valeur des variables par vos propres informations. Fournissez le préfixe d’adresse du sous-réseau au format CIDR. Créez les variables et le sous-réseau.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Remplacez la valeur de **$vnetName** par le nom du réseau virtuel. Fournissez le préfixe d’adresse du réseau virtuel au format CIDR. Créez la variable et le réseau virtuel avec le sous-réseau.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Remplacez la valeur de **$ipName** par le nom de l’adresse IP publique. Créez la variable et l’adresse IP publique.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
        
2. Remplacez la valeur de **$nicName** par le nom de l’interface réseau. Créez la variable et l'interface réseau.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

		

## Création de la machine virtuelle

Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle téléchargée comme source de la nouvelle installation.

>[AZURE.NOTE] La machine virtuelle doit se trouver dans le même compte de stockage que le fichier de disque dur virtuel chargé.

</br>

	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account where the VHD file is and where the OS disk will be created
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.azure.microsoft.com/.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0817_2016-->