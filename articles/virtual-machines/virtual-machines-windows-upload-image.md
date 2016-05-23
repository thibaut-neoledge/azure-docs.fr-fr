<properties
	pageTitle="Charger un disque dur virtuel (VHD) Windows pour Resource Manager | Microsoft Azure"
	description="Apprenez à télécharger une image de machine virtuelle Windows pour l’utiliser avec le modèle de déploiement Resource Manager."
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
	ms.date="05/06/2016"
	ms.author="dkshir"/>

# Télécharger une image de machine virtuelle Windows dans Azure pour des déploiements Resource Manager


Cet article explique comment télécharger un disque dur virtuel (VHD) avec un système d’exploitation Windows et l’utiliser pour créer des machines virtuelles Windows à l’aide du modèle de déploiement Azure Resource Manager. Pour en savoir plus sur les disques et les disques durs virtuels dans Azure, consultez [À propos des disques et VHD pour machines virtuelles](virtual-machines-linux-about-disks-vhds.md).



## Composants requis

Cet article suppose que vous avez :

- **Un abonnement Azure** : si vous ne disposez pas déjà d’un abonnement Azure, [créez un compte Azure gratuit](/pricing/free-trial/?WT.mc_id=A261C142F) et [activez vos avantages abonnés MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell version 1.0 ou ultérieure** : si vous ne l’avez pas déjà installé, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- **Une machine virtuelle Windows** : il existe de nombreux outils pour créer des machines virtuelles en local. Pour obtenir des exemples, consultez [Installation du rôle Hyper-V et configuration d’une machine virtuelle](http://technet.microsoft.com/library/hh846766.aspx). Pour plus d’informations sur les systèmes d’exploitation Windows pris en charge par Azure, consultez [Prise en charge du logiciel serveur Microsoft pour les machines virtuelles Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Vérifiez que la machine virtuelle utilise le bon format de fichier.

Azure accepte uniquement les images des [machines virtuelles de première génération](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) enregistrées au format de fichier VHD. La taille du disque dur virtuel doit être fixe et correspondre à un nombre entier de mégaoctets, c’est-à-dire un nombre divisible par 8. La taille maximale autorisée pour le disque dur virtuel s’élève à 1 023 Go.

- Si vous avez une image de machine virtuelle Windows au format VHDX, convertissez-la en un disque dur virtuel de l’une des manières suivantes :

	- Hyper-V : ouvrez Hyper-V et sélectionnez votre ordinateur local sur la gauche. Puis, dans le menu du haut, cliquez sur **Action** > **Modifier le disque**. Parcourez les écrans en cliquant sur **Suivant** et en entrant ces options : *Chemin de votre fichier VHDX* > **Convertir** > **VHD** > **Taille fixe** > *Chemin du nouveau fichier VHD*. Cliquez sur **Terminer** pour fermer la fenêtre.

	- [Applet de commande PowerShell convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) : consultez le billet de blog [Conversion des formats de fichiers Hyper-V .vhdx en .vhd](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) pour plus d’informations.

- Si vous disposez d’une image de machine virtuelle Windows au [format de fichier VMDK](https://en.wikipedia.org/wiki/VMDK), convertissez-la au format VHD à l’aide de [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497). Consultez le blog [How to Convert a VMWare VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (en anglais) pour plus d’informations.


## Préparer le disque dur virtuel au chargement

Cette section vous montre comment généraliser votre machine virtuelle Windows. Toutes les informations personnelles de votre compte, entre autres, sont ainsi supprimées. Vous voudrez généralement procéder ainsi quand vous souhaiterez utiliser cette image de machine virtuelle pour déployer rapidement des machines virtuelles similaires. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

1. Connectez-vous à la machine virtuelle Windows

2. Ouvrez la fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\\system32\\sysprep**, puis exécutez `sysprep.exe`.

3. Dans la boîte de dialogue **Outil de préparation système**, procédez comme suit :

	1. Dans **Action de nettoyage du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case à cocher **Généraliser** est activée.

	2. Dans **Shutdown Options**, sélectionnez **Shutdown**.

	3. Cliquez sur **OK**.

	![Démarrer Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Créer ou rechercher un compte de stockage Azure

Vous avez besoin d’un compte de stockage dans Azure pour charger l’image de la machine virtuelle. Vous pouvez utiliser un compte de stockage existant ou en créer un. Pour cela, vous pouvez utiliser le portail Azure ou PowerShell.

### Pour créer ou rechercher un compte de stockage Azure à l’aide du portail Azure

1. Connectez-vous au [portail](https://portal.azure.com).

2. Cliquez sur **Parcourir** > **Comptes de stockage**.

3. Vérifiez si un compte de stockage que vous voulez utiliser existe pour télécharger cette image. Notez le nom de ce compte de stockage. Vous pouvez passer à la section [Charger l’image de la machine virtuelle](#uploadvm) si vous utilisez un compte de stockage existant.

4. Pour créer un compte de stockage, cliquez sur **Ajouter** et entrez les informations suivantes :

	1. Entrez le **nom** du compte de stockage. Celui-ci doit contenir entre 3 et 24 lettres minuscules et chiffres uniquement. Ce nom fait alors partie de l’URL que vous allez utiliser pour accéder aux objets blob, aux fichiers et aux autres ressources du compte de stockage.
	
	2. Sélectionnez *Resource Manager* en tant que **Modèle de déploiement**.

	3. Sélectionnez les valeurs appropriées pour **Type de compte**, **Performances** et **Réplication**. Vous pouvez survoler les icônes d’information pour en savoir plus sur ces valeurs.

	4. Sélectionnez *+ Nouveau* pour le **groupe de ressources** ou un groupe existant. Entrez le nom du nouveau groupe de ressources si vous souhaitez en créer un.

	5. Choisissez l’**emplacement** du compte de stockage et cliquez sur **Créer**. Le compte apparaît à présent dans le panneau **Comptes de stockage**.

		![Entrer les détails du compte de stockage](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Cette étape et les étapes suivantes vous montrent comment créer un conteneur d’objets blob dans ce compte de stockage. Ces étapes sont facultatives, car vous pouvez aussi utiliser la commande PowerShell de téléchargement de l’image pour créer un conteneur d’objets blob associé à votre image. Si vous ne voulez pas le créer vous-même, passez à la section [Charger l’image de la machine virtuelle](#uploadvm). Sinon, cliquez sur **Objets blob** dans la vignette **Services**.

		![Service d'objets blob](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Une fois que le panneau des objets blob s’affiche, cliquez sur **+Conteneur** pour créer un conteneur de stockage d’objets blob. Entrez le nom du conteneur et le type d’accès.

		![Créer un objet blob](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] Par défaut, le conteneur est privé et seul le propriétaire du compte peut y accéder. Pour autoriser l’accès public en lecture aux objets blob du conteneur, mais pas à ses propriétés ni à ses métadonnées, utilisez l’option **Objet blob**. Pour autoriser un accès public total en lecture au conteneur et aux objets blob, utilisez l’option **Conteneur**.

	8. Le panneau **Service BLOB** répertorie le nouveau conteneur d’objets blob. Notez l’URL de ce conteneur. Vous en aurez besoin pour la commande PowerShell de téléchargement de l’image. Selon la longueur de l’URL et la résolution d’écran, l’URL peut être en partie masquée. Dans ce cas, agrandissez le panneau en cliquant sur l’icône **Agrandir** dans le coin supérieur droit.


### Pour créer ou rechercher un compte de stockage Azure à l’aide de PowerShell

1. Ouvrez Azure PowerShell 1.0.x et connectez-vous à votre compte Azure.

		Login-AzureRmAccount

	Cette commande ouvre une fenêtre contextuelle vous permettant d’entrer vos informations d’identification Azure.

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

	2. Si vous voulez créer un groupe de ressources, utilisez cette commande :

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Créez un compte de stockage dans ce groupe de ressources à l’aide de :

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## Charger l’image de la machine virtuelle dans votre compte de stockage

Suivez ces étapes dans Azure PowerShell pour télécharger l’image de la machine virtuelle dans votre compte de stockage. Votre image sera téléchargée vers un conteneur de stockage d’objets blob dans ce compte. Vous pouvez utiliser un conteneur existant ou en créer un.

1. Connectez-vous à Azure PowerShell 1.0.x en utilisant `Login-AzureRmAccount`. Assurez-vous que vous utilisez l’abonnement approprié en vous servant de `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, comme indiqué dans la section précédente.

2. Ajoutez le disque dur virtuel Azure généralisé au compte de stockage à l’aide de l’applet de commande [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) :

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Où :
	- **StorageAccountURL** correspond à l’URL du compte de stockage. Le format est généralement le suivant : `https://YourStorageAccountName.blob.core.windows.net`. Vous devez utiliser le nom du compte de stockage existant ou nouveau à la place de *YourStorageAccountName*.
	- **BlobContainer** correspond au conteneur d’objets blob dans lequel vous voulez stocker vos images. Si l’applet de commande ne trouve pas le conteneur d’objets blob existant portant ce nom, elle le crée pour vous.
	- **TargetVHDName** est le nom que vous souhaitez donner à l’image.
	- **LocalPathOfVHDFile** est le chemin d’accès complet et le nom du fichier .vhd sur votre ordinateur local.

	Le résultat de l’exécution de `Add-AzureRmVhd` sera semblable à ce qui suit :

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

</br>
## Déployer une nouvelle machine virtuelle à partir de l’image chargée

Vous pouvez maintenant utiliser l’image chargée pour créer une machine virtuelle Windows. Ces étapes vous montrent comment utiliser Azure PowerShell et l’image de machine virtuelle téléchargée aux étapes ci-dessus en vue de créer une machine virtuelle dans un nouveau réseau virtuel.

>[AZURE.NOTE] L’image de machine virtuelle doit être présente dans le même compte de stockage que la machine virtuelle qui sera créée.

### Créer des ressources réseau

Utilisez l’exemple de script PowerShell suivant pour configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. Utilisez les valeurs des variables (représentées par le symbole **$**) selon les besoins de votre application.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Créer une machine virtuelle

Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle téléchargée comme source de la nouvelle installation. </br>

	#Enter a new user name and password in the pop-up window for the following
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

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
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

Vous devez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0511_2016-->