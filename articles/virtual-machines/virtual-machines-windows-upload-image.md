<properties
	pageTitle="Charger un disque dur virtuel (VHD) Windows pour Resource Manager | Microsoft Azure"
	description="Apprenez à charger une image de machine virtuelle Windows pour l’utiliser avec le modèle de déploiement Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="dkshir"/>

# Charger une image de machine virtuelle Windows dans Microsoft Azure pour des déploiements Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-createupload-vhd.md).


Cet article montre comment charger un disque dur virtuel (VHD) avec un système d’exploitation Windows et l’utiliser pour créer des machines virtuelles Windows à l’aide du modèle de déploiement Resource Manager. Pour en savoir plus sur les disques et les disques durs virtuels dans Microsoft Azure, consultez la rubrique [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).



## Composants requis

Cet article suppose que vous avez :

1. **Un abonnement Azure** : Si vous n’en avez pas, [ouvrez un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F). Une fois votre compte créé, vous disposez de crédits pour tester les services Azure payant. Une fois qu’ils sont épuisés, vous pouvez conserver le compte et utiliser les services Azure gratuits, notamment Azure Web Sites. Votre carte de crédit n’est pas débitée tant que vous n’avez pas explicitement modifié vos paramètres. Vous pouvez aussi [activer les avantages d’un abonnement MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Un abonnement MSDN vous donne droit chaque mois à des crédits que vous pouvez utiliser pour les services Azure payants.

2. **Microsoft Azure PowerShell 1.0.x** : Assurez-vous d’avoir Microsoft Azure PowerShell version 1.0.x. Si vous ne l’avez pas déjà installé, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md). Nous vous recommandons d’utiliser les versions 1.0 et supérieures, car les nouvelles fonctionnalités Resource Manager ne sont pas ajoutées aux anciennes versions PowerShell. Consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) pour en savoir plus sur les différences de version.

3. **Une machine virtuelle exécutant le système d’exploitation Windows** : Il existe de nombreux outils pour créer des machines virtuelles en local. Par exemple, vous pouvez utiliser le Gestionnaire Hyper-V pour créer une machine virtuelle et installer le système d’exploitation. Pour obtenir des instructions, voir [Installation du rôle Hyper-V et configuration d’une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). Pour plus d’informations sur les systèmes d’exploitation Windows pris en charge, consultez [Prise en charge du logiciel serveur Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Vérifiez que la machine virtuelle utilise le bon format de fichier.

Microsoft Azure accepte uniquement les images des [machines virtuelles de première génération](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) enregistrées au format de fichier VHD. La taille du disque dur virtuel (VHD) doit être fixe et correspondre à un nombre entier de Mo. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.

- Le Gestionnaire Hyper-V enregistre généralement votre image de machine virtuelle au format VHDX, ce qui n’est pas pris en charge dans Microsoft Azure. Vous pouvez la convertir au format VHD à l’aide d’Hyper-V ou de l’[applet de commande Powershell Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Pour obtenir des instructions sur l’utilisation de PowerShell, consultez [Conversion des formats de fichiers Hyper-V .vhdx en .vhd](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/). Ou dans Hyper-V, sélectionnez votre ordinateur local sur la gauche, puis dans le menu juste au-dessus, cliquez sur **Actions** > **Modifier le disque...** Parcourez les écrans en cliquant sur **Suivant** et en entrant ces options : Chemin de votre fichier VHDX > **Convertir** > **VHD** > **Taille fixe** > Chemin du nouveau fichier VHD. Cliquez sur **Terminer** pour fermer la fenêtre.

- Si vous avez une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), vous pouvez la convertir au format VHD à l’aide de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consultez le blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) pour plus d’informations.


## Préparer le disque dur virtuel au chargement

Cette section vous montre comment généraliser votre machine virtuelle Windows. Toutes les informations personnelles de votre compte, entre autres, sont ainsi supprimées. Vous voudrez généralement procéder ainsi quand vous souhaiterez utiliser cette image de machine virtuelle pour déployer rapidement des machines virtuelles similaires. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

1. Connectez-vous à la machine virtuelle Windows.

2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

3. Dans la boîte de dialogue **Outil de préparation système**, procédez comme suit :

	1. Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée.

	2. Dans **Shutdown Options**, sélectionnez **Shutdown**.

	3. Cliquez sur **OK**.

	![Démarrer Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>
## Créer ou rechercher un compte de stockage Azure

Vous avez besoin d’un compte de stockage dans Azure pour charger l’image de la machine virtuelle. Vous pouvez utiliser un compte de stockage existant ou en créer un. Pour cela, vous pouvez utiliser le portail ou PowerShell.

### Utiliser le portail

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Cliquez sur **Parcourir** > **Comptes de stockage**.

3. Vérifiez si un compte de stockage que vous voulez utiliser existe pour charger cette image. Notez le nom de ce compte de stockage. Vous pouvez passer à la section [Charger l’image de la machine virtuelle](#uploadvm) si vous utilisez un compte de stockage existant.

4. Si vous voulez créer un compte de stockage, cliquez sur **Ajouter** et entrez les informations suivantes :

	1. Entrez le **nom** du compte de stockage. Celui-ci doit contenir entre 3 et 24 lettres minuscules et chiffres uniquement. Ce nom fait alors partie de l’URL que vous allez utiliser pour accéder aux objets blob, aux fichiers et aux autres ressources du compte de stockage.

	2. Sélectionnez le **type** du compte de stockage que vous voulez créer. Pour plus d’informations, consultez [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

	3. Entrez le nom du **groupe de ressources**. Le portail crée un groupe de ressources s’il n’en trouve pas un existant qui porte ce nom.

	4. Choisissez l’**emplacement** du compte de stockage.

	5. Cliquez sur **Créer**. Le compte apparaît à présent sous le panneau **Comptes de stockage**.

		![Entrer les détails du compte de stockage](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Cette étape et les suivantes vous montrent comment créer un conteneur d’objets blob dans ce compte de stockage. Ces étapes sont facultatives, car la commande PowerShell de chargement de l’image peut également créer un conteneur d’objets blob pour votre image. Si vous ne voulez pas le créer vous-même, passez à la section [Charger l’image de la machine virtuelle](#uploadvm). Sinon, cliquez sur **Objets blob** dans la vignette **Services**.

		![Service d'objets blob](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Une fois que le panneau des objets blob s’affiche, cliquez sur **+Conteneur** pour créer un conteneur de stockage d’objets blob. Entrez le nom du conteneur et le type d’accès.

		![Créer un objet blob](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l’accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés et ses métadonnées, utilisez l’option **Objet blob**. Pour autoriser un accès public total en lecture au conteneur et aux objets blob, utilisez l’option **Conteneur**.

	8. Le panneau **Service BLOB** répertorie le nouveau conteneur d’objets blob. Notez l’URL de ce conteneur ; vous en aurez besoin pour la commande PowerShell de chargement de l’image. Selon la longueur de l’URL et la résolution de l’écran, l’URL risque d’être partiellement masquée. Dans ce cas, agrandissez le panneau en cliquant sur l’icône *Agrandir* en haut à droite.


### Utiliser PowerShell

1. Ouvrez Azure PowerShell 1.0.x et connectez-vous à votre compte Azure.

		Login-AzureRmAccount

	Cette commande ouvre une fenêtre contextuelle pour entrer vos informations d’identification Azure.

2. Si l’ID d’abonnement sélectionné par défaut est différent de celui que vous souhaitez utiliser, choisissez l’une des options suivantes pour définir le bon abonnement.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	ou

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Vous pouvez rechercher les abonnements de votre compte Azure à l’aide de la commande `Get-AzureRmSubscription`.

3. Recherchez les comptes de stockage disponibles sous cet abonnement.

		Get-AzureRmStorageAccount

	Si vous voulez utiliser un compte de stockage existant, passez à la section [Charger l’image de la machine virtuelle](#uploadvm).

4. Si vous voulez créer un compte de stockage pour y placer cette image, procédez comme suit :

	1. Assurez-vous de disposer d’un groupe de ressources pour ce compte de stockage. Recherchez tous les groupes de ressources dans votre abonnement à l’aide de :

			Get-AzureRmResourceGroup

	2. Si vous voulez créer un groupe de ressources, utilisez cette commande :

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Créez un compte de stockage dans ce groupe de ressources à l’aide de :

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>
## Charger l’image de la machine virtuelle dans votre compte de stockage

Suivez ces étapes dans Azure PowerShell pour charger l’image de la machine virtuelle dans votre compte de stockage. Votre image est chargée dans un conteneur de stockage d’objets blob dans ce compte. Vous pouvez utiliser un conteneur existant ou en créer un.

1. Connectez-vous à Azure PowerShell 1.0.x à l’aide de `Login-AzureRmAccount` et assurez-vous d’utiliser le bon abonnement à l’aide de `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, comme indiqué dans la section précédente.

2. Ajoutez le disque dur virtuel Azure généralisé au compte de stockage à l’aide d’[Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) :

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Où :
	- **StorageAccountURL** correspond à l’URL du compte de stockage. Le format est généralement le suivant : `https://YourStorageAccountName.blob.core.windows.net`. Notez que vous devez utiliser le nom du compte de stockage existant ou nouveau à la place de **YourStorageAccountName**.
	- **BlobContainer** correspond au conteneur d’objets blob dans lequel vous voulez stocker vos images. Si l’applet de commande ne trouve pas le conteneur d’objets blob existant portant ce nom, elle le crée pour vous.
	- **TargetVHDName** est le nom que vous souhaitez donner à l’image.
	- **LocalPathOfVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre ordinateur local.

	Une exécution réussie de l’applet de commande `Add-AzureRmVhd` donne ceci :

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file  C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Cette commande peut prendre du temps, selon votre connexion réseau et la taille de votre fichier VHD.

</br>
## Déployer une nouvelle machine virtuelle à partir de l’image chargée

Vous pouvez maintenant utiliser l’image chargée pour créer une machine virtuelle Windows. Ces étapes vous montrent comment utiliser Azure PowerShell et l’image de machine virtuelle chargée dans les étapes ci-dessus, pour créer la machine virtuelle dans un nouveau réseau virtuel.

>[AZURE.NOTE] L’image de machine virtuelle doit être présente dans le même compte de stockage que la machine virtuelle qui sera créée.

### Créer des ressources réseau

Utilisez l’exemple de script PowerShell suivant pour configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. Utilisez les valeurs des variables représentées par le symbole **$** selon ce qui convient à votre application.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Créer une machine virtuelle

Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle chargée comme source de la nouvelle installation. </br>

	#Enter a new username and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Par exemple, votre flux de travail peut ressembler à ceci :

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Vous devez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** OU en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gérer des machines virtuelles à l’aide d’Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0323_2016-->