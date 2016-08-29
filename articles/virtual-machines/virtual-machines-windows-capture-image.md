<properties
	pageTitle="Création d’une image de machine virtuelle à partir d’une machine virtuelle Azure | Microsoft Azure"
	description="Apprenez à créer une image de machine virtuelle généralisée à partir d’une machine virtuelle Azure existante créée dans le modèle de déploiement Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# Comment créer une image de machine virtuelle à partir d’une machine virtuelle Azure existante


Cet article vous montre comment utiliser Azure PowerShell pour créer une image généralisée d’une machine virtuelle Azure existante. Vous pouvez ensuite utiliser l’image pour créer une autre machine virtuelle. Cette image comprend le disque du système d’exploitation, ainsi que les disques de données attachés à la machine virtuelle. L’image n’inclut pas les ressources du réseau virtuel. Vous devez donc configurer les ressources lorsque vous créez une machine virtuelle à l’aide de l’image. Ce processus crée une [image généralisée Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Composants requis

- Ces étapes présupposent que vous disposez déjà d’une machine virtuelle Azure dans le modèle de déploiement Resource Manager que vous souhaitez utiliser pour créer l’image. Vous avez besoin du nom de la machine virtuelle et du nom du groupe de ressources. Vous pouvez obtenir une liste des groupes de ressources dans votre abonnement en saisissant l’applet de commande PowerShell `Get-AzureRmResourceGroup`. Vous pouvez obtenir une liste de machines virtuelles dans votre abonnement en saisissant `Get-AzureRMVM`.

- Vous devez disposer d’une installation d'Azure PowerShell version 1.0.x. Si vous n’avez pas déjà installé PowerShell, consultez la rubrique [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- Vérifiez que les rôles serveur exécutés sur la machine sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

## Préparation des données source de machine virtuelle 

Cette section vous montre comment généraliser votre machine virtuelle Windows de manière à pouvoir l’utiliser comme image.

> [AZURE.WARNING] Vous ne peut pas vous connecter à la machine virtuelle par le biais du RDP une fois qu’elle est généralisée, étant donné que le processus supprime tous les comptes d’utilisateur. Les modifications sont irréversibles.

1. Connectez-vous à votre machine virtuelle Windows. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles** > Votre machine virtuelle Windows > **Connecter**.

2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.

3. Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4. Dans la boîte de dialogue **Outil de préparation système**, procédez comme suit :

	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d’informations sur l’utilisation de Sysprep, voir la page [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Exécutez Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep arrête la machine virtuelle. Son état devient **Arrêté** dans le portail Azure.


## Connexion à Azure PowerShell

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure.

		Login-AzureRmAccount

	Une fenêtre contextuelle s’ouvre pour vous permettre d’entrer les informations d’identification de votre compte Azure.

2. Obtenez les ID d’abonnement de vos abonnements disponibles.

		Get-AzureRmSubscription

3. Définissez l’abonnement approprié à l’aide de l’ID d’abonnement.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## Libération de la machine virtuelle et définition de l’état sur généralisé		

1. Libérez les ressources de la machine virtuelle.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	L’*état* de la machine virtuelle dans le portail Azure passe de **Arrêté** à **Arrêté (libéré)**.

2. Définissez l’état de la machine virtuelle sur **Généralisé**.

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. Vérifiez l’état de la machine virtuelle. La section **OSState/généralisé** pour la machine virtuelle doit avoir la valeur de **DisplayStatus** définie sur **Machine virtuelle généralisée**.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## Création de l’image 

1. Copiez l’image de la machine virtuelle dans le conteneur de stockage de destination à l’aide de cette commande. L’image est créée dans le même compte de stockage que la machine virtuelle d’origine. La variable `-Path` enregistre une copie du modèle JSON localement. La variable `-DestinationContainerName` est le nom du conteneur dans lequel vous souhaitez stocker vos images. Si le conteneur n’existe pas, il est créé pour vous.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	Vous pouvez obtenir l’URL de l’image à partir du modèle de fichier JSON. Accédez à la section **resources** > **storageProfile** > **osDisk** > **image** > **uri** pour obtenir le chemin d’accès complet de votre image. L’URL de l’image ressemble a le format suivant : `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
	
	Vous pouvez également vérifier l’URI dans le portail. L’image est copiée dans un objet blob nommé **system** dans votre compte de stockage.

2. Créez une variable pour le chemin d’accès à l’image.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## Créez un réseau virtuel

Créez le réseau virtuel et le sous-réseau du [réseau virtuel](../virtual-network/virtual-networks-overview.md).
		

1. Remplacez la valeur des variables par vos propres informations. Fournissez le préfixe d’adresse du sous-réseau au format CIDR. Créez les variables et le sous-réseau.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Remplacez la valeur de **$vnetName** par le nom du réseau virtuel. Fournissez le préfixe d’adresse du réseau virtuel au format CIDR. Créez la variable et le réseau virtuel avec le sous-réseau.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Création d'une adresse IP publique et une interface réseau

Pour établir la communication avec la machine virtuelle dans le réseau virtuel, vous avez besoin d’une [adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) et d’une interface réseau.

1. Remplacez la valeur de **$ipName** par le nom de l’adresse IP publique. Créez la variable et l’adresse IP publique.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Remplacez la valeur de **$nicName** par le nom de l’interface réseau. Créez la variable et l'interface réseau.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## Création de la machine virtuelle

Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de la machine virtuelle téléchargée comme source de la nouvelle installation.

>[AZURE.NOTE] La machine virtuelle doit se trouver dans le même compte de stockage que le disque dur original.

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

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
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Lorsque vous avez terminé, vous devez voir la machine virtuelle nouvellement créée dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** ou en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0817_2016-->