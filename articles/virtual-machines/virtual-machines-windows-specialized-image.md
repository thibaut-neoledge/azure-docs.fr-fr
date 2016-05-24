<properties
	pageTitle="Création d’une copie de votre machine virtuelle Windows | Microsoft Azure"
	description="Découvrez comment créer une copie de votre machine virtuelle Azure exécutant Windows dans le modèle de déploiement Resource Manager en créant une *image spécialisée*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Création d’une copie d’une machine virtuelle Windows dans le modèle de déploiement Resource Manager


Cet article vous explique comment créer une copie de votre machine virtuelle Azure exécutant Windows dans le modèle de déploiement Resource Manager à l'aide d’Azure PowerShell et du portail Azure. Il décrit comment créer une image **_spécialisée_** de votre machine virtuelle Azure qui gère les comptes d'utilisateurs et d'autres données d'état à partir de votre machine virtuelle d'origine. Une image spécialisée est utile par exemple pour transmettre votre machine virtuelle Windows d’un modèle de déploiement classique vers le modèle de déploiement Resource Manager, ou pour créer une copie de sauvegarde de votre machine virtuelle Windows créée dans le modèle de déploiement Resource Manager. Vous pouvez copier les disques du système d'exploitation et les disques de données de cette manière, puis configurer les ressources réseau pour créer la nouvelle machine virtuelle.

Si vous devez déployer un très grand nombre de machines virtuelles Windows similaires, vous aurez besoin d’une image *généralisée*. Pour ce faire, lisez l’article [Comment capturer une machine virtuelle Windows](virtual-machines-windows-capture-image.md).



## Avant de commencer

Cet article suppose que vous disposez de :

1. Une **machine virtuelle Azure exécutant Windows**, dans le modèle de déploiement Classic ou Resource Manager, avec le système d’exploitation configuré, les disques de données attachés et les applications requises installées. Si vous avez besoin d’aide pour créer cette machine virtuelle, voir [Créer une machine virtuelle Windows avec Resource Manager](virtual-machines-windows-ps-create.md). 

1. **Azure PowerShell 1.0 ou une version ultérieure** installé sur votre machine et connecté à votre abonnement Azure. Pour plus d’informations, voir [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

1. L’**outil AzCopy** installé sur votre machine. Pour plus d’informations sur cet utilitaire, voir [Transfert de données avec l’utilitaire de ligne de commande AzCopy](../storage/storage-use-azcopy.md).

1. Un **groupe de ressources** avec un **compte de stockage** et un **conteneur d’objets blob** créés dans ce groupe pour y copier les disques durs virtuels. Pour plus d’informations sur l’utilisation d’un compte de stockage existant ou sur la création de ce type de compte, voir la section [Créer ou rechercher un compte de stockage Azure](virtual-machines-windows-upload-image.md#createstorage).



> [AZURE.NOTE] Des étapes similaires s’appliquent pour une machine virtuelle créée en utilisant l’un des deux modèles de déploiement comme image source. Le cas échéant, nous détaillerons les légères différences possibles.


## Copie de disques durs virtuels sur votre compte de stockage Resource Manager


1. Commencez par libérer les disques durs virtuels utilisés par la machine virtuelle source en exécutant l’une des deux procédures suivantes :

	- Pour **_copier_** votre machine virtuelle source, vous devez l’**arrêter**, puis la **libérer**.
	
		- Dans le cas d’une machine virtuelle créée à l’aide du modèle de déploiement Classic, vous pouvez soit vous connecter au [Portail](https://portal.azure.com) et cliquer sur **Parcourir** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Arrêter**, soit utiliser la commande PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`. 
		
		- Dans le cas d’une machine virtuelle dans le modèle de déploiement Resource Manager, vous pouvez soit vous connecter au portail et cliquer sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**, soit utiliser la commande PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Notez que l’*État* de la machine virtuelle dans le portail passe de la valeur **En cours d’exécution** à la valeur **Arrêté (désalloué)**.

	
	- Si vous souhaitez **_effectuer la migration_** de votre machine virtuelle source, **supprimez** cette machine et utilisez le disque dur virtuel désormais disponible. **Accédez** à votre machine virtuelle dans le [portail](https://portal.azure.com), puis cliquez sur **Supprimer**.
	
1. Recherchez les clés d’accès pour le compte de stockage qui contient votre disque dur virtuel source, ainsi que le compte de stockage dans lequel vous allez copier votre disque dur virtuel pour créer la machine virtuelle. La clé du compte à partir duquel nous copions le disque dur virtuel est appelée *Clé source*, et la clé du compte dans lequel il sera copié est appelée *Clé de destination*. Pour plus d’informations sur les clés d’accès, voir [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

	- Si votre machine virtuelle source a été créée à l’aide du modèle de déploiement Classic, cliquez sur **Parcourir** > **Comptes de stockage (classiques)** > *votre compte de stockage* > **Tous les paramètres** > **Clés** et copiez la clé portant le nom **CLÉ D’ACCÈS PRIMAIRE**. 
	
	- Pour une machine virtuelle créée à l’aide du modèle de déploiement Resource Manager, ou pour le compte de stockage que vous utiliserez avec votre nouvelle machine virtuelle, cliquez sur **Parcourir** > **Comptes de stockage** > *votre compte de stockage* > **Tous les paramètres** > **Clés d’accès** et copiez la clé portant la mention **key1**.

1. Identifiez les URL d’accès à vos comptes de stockage source et de destination. Dans le portail, **accédez** à votre compte de stockage, puis cliquez sur **Objets blob**. Cliquez ensuite sur le conteneur qui héberge votre disque dur virtuel source (par exemple, *vhds* pour le modèle de déploiement classique) ou sur le conteneur dans lequel souhaitez copier le disque dur virtuel. Cliquez sur **Propriétés** pour le conteneur et copiez le texte intitulé **URL**. Nous aurons besoin des URL des conteneurs source et de destination. Les URL ressembleront à ceci : `https://myaccount.blob.core.windows.net/mycontainer`.

1. Sur votre ordinateur local, ouvrez une fenêtre de commande et accédez au dossier dans lequel AzCopy est installé. Ce dossier peut être semblable à *C:\\Program Files (x86)\\Microsoft SDKs\\Azure\\AzCopy*. À partir de ce dossier, exécutez la commande suivante : </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] Vous devrez copier les disques du système d'exploitation et les disques de données séparément à l’aide d’AzCopy comme décrit ci-dessus.


## Création d’une machine virtuelle à l'aide du disque dur virtuel copié

Ces étapes vous montrent comment utiliser Azure PowerShell pour créer une machine virtuelle Windows basée sur Resource Manager dans un nouveau réseau virtuel à l’aide du disque dur virtuel copié dans les étapes précédentes. Le disque dur virtuel doit être présent dans le même compte de stockage que la machine virtuelle qui sera créée.


Commencez par configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle en utilisant le script suivant. Utilisez les valeurs des variables (représentées par le symbole **$**) selon les besoins de votre application.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Ensuite, définissez les configurations de machines virtuelles et utilisez les disques durs virtuels copiés pour créer une machine virtuelle comme illustré ci-dessous. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
Les URL des disques de données et des disques du système d’exploitation se présentent comme suit : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pour obtenir les URL, rendez-vous sur le portail, accédez au conteneur de stockage cible, cliquez sur le disque dur virtuel du système d’exploitation ou de données qui a été copié, puis copiez le contenu de l’**URL**.
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


Vous devez voir la machine virtuelle nouvellement créée dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles** OU en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Pour vous connecter à votre nouvelle machine virtuelle, **accédez** à la machine virtuelle dans le [portal](https://portal.azure.com), cliquez sur **Se connecter**, puis ouvrez le fichier RDP *Bureau à distance*. Utilisez les informations d’identification de compte de votre machine virtuelle d’origine pour vous connecter à votre nouvelle machine virtuelle.


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, voir [Gestion des machines virtuelles Azure à l’aide de modèles Azure Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0511_2016-->