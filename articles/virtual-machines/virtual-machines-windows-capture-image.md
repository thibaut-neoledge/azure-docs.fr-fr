<properties
	pageTitle="Capturer une machine virtuelle Windows dans Resource Manager | Microsoft Azure"
	description="Apprenez à capturer l’image d’une machine virtuelle Azure sous Windows, créée avec le modèle de déploiement Resource Manager."
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
	ms.date="05/13/2016"
	ms.author="cynthn"/>

# Comment capturer une machine virtuelle Windows dans le modèle de déploiement Resource Manager


Cet article montre comment utiliser Azure PowerShell pour capturer une machine virtuelle Azure exécutant Windows de façon à l’utiliser pour créer d’autres machines virtuelles. Cette image comprend le disque du système d’exploitation, ainsi que les disques de données attachés à la machine virtuelle. Comme il n’inclut pas les ressources de réseau virtuel dont vous aurez besoin pour créer une machine virtuelle Windows, vous devrez les configurer avant de pouvoir créer une autre machine virtuelle qui utilise l’image. Cette image est également préparée comme une [image Windows généralisée](https://technet.microsoft.com/library/hh824938.aspx).



## Configuration requise

Ces étapes supposent que vous avez déjà créé une machine virtuelle Azure dans le modèle de déploiement Resource Manager et que vous avez configuré le système d’exploitation, dont l’attachement des disques de données et l’exécution de personnalisations telles que l’installation d’applications. Si vous n’avez pas encore effectué ces opérations, consultez [Création d’une machine virtuelle Windows à l’aide de Resource Manager et de PowerShell](virtual-machines-windows-ps-create.md). Vous pouvez tout aussi facilement créer une machine virtuelle Windows à l’aide du [portail Azure](https://portal.azure.com). Consultez [Création d’une machine virtuelle Windows dans le portail Azure](virtual-machines-windows-hero-tutorial.md).


## Préparer la machine virtuelle pour la capture d’image

Cette section vous montre comment généraliser votre machine virtuelle Windows. Toutes les informations personnelles de votre compte, entre autres, sont ainsi supprimées. Vous voudrez généralement procéder ainsi quand vous souhaiterez utiliser cette image de machine virtuelle pour déployer rapidement des machines virtuelles similaires.

> [AZURE.WARNING] Veuillez noter que vous ne pouvez pas vous connecter à la machine virtuelle via RDP une fois qu’elle est généralisée, car le processus supprime tous les comptes utilisateur. Il s’agit d’une modification irréversible.

1. Connectez-vous à votre machine virtuelle Windows. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir** > **Machines virtuelles** > Votre machine virtuelle Windows > **Connexion**.

2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.

3. Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.

4. Dans la boîte de dialogue **Outil de préparation système**, procédez comme suit :

	- Dans **Action de nettoyage du système**, sélectionnez **Enter System Out-of-Box Experience (OOBE)** et vérifiez que la case à cocher **Generalize** est activée. Pour plus d’informations sur l’utilisation de Sysprep, voir la page [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx).

	- Dans **Shutdown Options**, sélectionnez **Shutdown**.

	- Cliquez sur **OK**.

	![Exécutez Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   Sysprep arrête la machine virtuelle. Son état devient **Arrêté** dans le portail Azure.

</br>
## Capture de la machine virtuelle

Vous pouvez capturer la machine virtuelle Windows généralisée à l’aide d’Azure PowerShell ou du nouvel outil Explorateur d’Azure Resource Manager. Cette section vous montre les étapes des deux scénarios.

### Utiliser PowerShell

Cet article suppose que vous avez installé Azure PowerShell 1.0.x. Nous vous recommandons d’utiliser cette version, car les nouvelles fonctionnalités Resource Manager ne seront pas ajoutées aux anciennes versions PowerShell. Si vous n’avez pas déjà installé PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

1. Ouvrez Azure PowerShell et connectez-vous à votre compte Azure.

		Login-AzureRmAccount

	Cette commande ouvre une fenêtre contextuelle pour entrer vos informations d’identification Azure.

2. Si l’ID d’abonnement sélectionné par défaut est différent de celui que vous souhaitez utiliser, choisissez l’une des options suivantes pour définir le bon abonnement.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	ou

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Pour trouver les abonnements de votre compte Azure, utilisez la commande `Get-AzureRmSubscription`.

3. Maintenant, vous devez libérer les ressources utilisées par cette machine virtuelle à l’aide de cette commande.

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Vous verrez que l’*état* de la machine virtuelle sur le portail Azure est passé de **Arrêté** à **Arrêté (désalloué)**.

	>[AZURE.TIP] Vous pouvez aussi vérifier l’état de votre machine virtuelle dans PowerShell en utilisant :</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> Le champ **DisplayStatus** correspond à l’**état** affiché dans le portail Azure.

4. Ensuite, vous devez définir l’état de la machine virtuelle sur **Généralisé**. Notez que vous devez procéder ainsi, car l’étape de généralisation ci-dessus (`sysprep`) ne le fait pas d’une façon compréhensible par Azure.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] L’état généralisé, tel que défini ci-dessus, n’apparaît pas sur le portail. Cependant, vous pouvez le vérifier à l’aide de la commande Get-AzureRmVM, comme illustré dans l’info-bulle ci-dessus.

5. Capturez l’image de la machine virtuelle sur un conteneur de stockage de destination à l’aide de cette commande.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	La variable `-Path` est facultative. Vous pouvez l’utiliser pour enregistrer le modèle JSON localement. La variable `-DestinationContainerName` est le nom du conteneur dans lequel vous souhaitez stocker vos images. L’URL de l’image stockée sera similaire à `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Elle sera créée dans le même compte de stockage que celui de la machine virtuelle d’origine.

	>[AZURE.NOTE] Pour trouver l’emplacement de votre image, ouvrez le modèle de fichier JSON local. Accédez à la section **resources** > **storageProfile** > **osDisk** > **image** > **uri** pour obtenir le chemin d’accès complet de votre image. Vous pouvez également vérifier l’URI sur le portail. Il est copié dans un objet blob nommé **system** de votre compte de stockage.


### Utilisation de l’Explorateur de ressources Azure (version préliminaire)

L’[Explorateur de ressources Azure (version préliminaire)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) est un nouvel outil que vous pouvez utiliser pour gérer les ressources Azure créées selon le modèle de déploiement Resource Manager. Avec cet outil, vous pouvez facilement

- Découvrir les API de gestion des ressources Azure.
- Obtenir de la documentation sur les API.
- Effectuer directement les appels d’API dans vos abonnements Azure.

Pour en savoir plus sur ce que ce puissant outil vous permet de faire, regardez la vidéo [Azure Resource Manager Explorer with David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo).

Vous pouvez utiliser l’Explorateur de ressources pour capturer la machine virtuelle, comme alternative à la méthode PowerShell.

1. Ouvrez le [site web de l’Explorateur de ressources](https://resources.azure.com/) et connectez-vous à votre compte Azure.

2. Dans la partie supérieure droite de l’outil, sélectionnez **Lecture/écriture** pour autoriser les opérations _PUT_ et _POST_. La valeur par défaut est **Lecture seule**, ce qui signifie que, par défaut, vous ne pouvez effectuer que des opérations _GET_.

	![Lecture/écriture de l’Explorateur de ressources](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. Ensuite, recherchez votre machine virtuelle Windows. Vous pouvez entrer le nom dans la *zone de recherche* en haut de l’outil ou parcourir le menu de gauche en sélectionnant **Abonnements** > *votre abonnement Azure* > **Groupe de ressources** > *votre groupe de ressources* > **Fournisseurs** > **Microsoft.Compute** > **Machines virtuelles** > *votre machine virtuelle Windows*. Lorsque vous cliquez sur votre machine virtuelle sur le volet de navigation gauche, son modèle apparaît à droite de l’outil.

4. Sur la partie supérieure droite de la page de modèle, vous devez voir les onglets des différentes opérations disponibles pour cette machine virtuelle. Cliquez sur l’onglet **Actions (POST/DELETE)**.

	![Menu Action de l’Explorateur de ressources](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

	- La liste de toutes les actions que vous pouvez effectuer sur la machine virtuelle s’affiche.

		![Éléments d’action de l’Explorateur de ressources](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

5. Libérez la machine virtuelle en cliquant sur le bouton d’action permettant de la **libérer**. L’état de votre machine virtuelle passe de **Arrêté** à **Arrêté (libéré)**.

6. Marquez la machine virtuelle comme généralisée en cliquant sur le bouton d’action permettant de la **généraliser**. Vous pouvez vérifier les changements d’état en cliquant sur le menu **InstanceView** situé sous le nom de votre machine virtuelle sur le côté gauche, et en accédant à la section **États** située à droite.

7. Sous le bouton d’action **capturer**, vous pouvez définir les valeurs de la capture de votre image. Les valeurs une fois remplies peuvent se présenter comme suit.

	![Capture de l’Explorateur de ressources](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	Cliquez sur le bouton d’action **capturer** pour capturer l’image de votre machine virtuelle. Cela crée un nouveau disque dur virtuel pour l’image, ainsi qu’un fichier de modèle JSON.

8. Pour accéder à la nouvelle image de disque dur virtuel et au modèle, téléchargez et installez l’outil Azure de gestion des ressources de stockage : l’[Explorateur Azure Storage](http://storageexplorer.com/). Le programme d’installation va installer l’Explorateur Azure Storage localement sur votre machine.

	- Ouvrez l’Explorateur Azure Storage et connectez-vous à votre abonnement Azure. Il doit afficher tous les comptes de stockage disponibles pour votre abonnement.

	- Sur le côté gauche, vous devez voir le compte de stockage de la machine virtuelle que nous avons capturée dans les étapes ci-dessus. Double-cliquez sur le menu **système** en dessous. Le contenu du dossier **système** doit s’afficher sur le côté droit.

		![Système de l’Explorateur de stockage](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- Double-cliquez sur **Microsoft.Compute** > **Images**. Tous vos dossiers d’image s’affichent. Double-cliquez sur le nom de dossier que vous avez entré pour la variable **destinationContainerName** lors de la capture de l’image dans l’Explorateur de ressources. Le disque dur virtuel et le fichier de modèle JSON s’affichent.

	- À ce stade, vous pouvez trouver l’URL ou télécharger le disque dur virtuel/modèle en cliquant dessus avec le bouton droit.

		![Modèle de l’Explorateur de stockage](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## Déployer une machine virtuelle à partir de l’image capturée

Vous pouvez maintenant utiliser l’image capturée pour créer une machine virtuelle Windows. Ces étapes vous montrent comment utiliser Azure PowerShell et l’image de machine virtuelle capturée dans les étapes ci-dessus pour créer la machine virtuelle dans un nouveau réseau virtuel.

>[AZURE.NOTE] L’image de machine virtuelle doit être présente dans le même compte de stockage que la machine virtuelle qui sera créée.

### Créer des ressources réseau

Utilisez l’exemple de script PowerShell suivant pour configurer un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle. Utilisez les valeurs des variables (représentées par le symbole **$**) adaptées à votre application.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Créer une machine virtuelle

Le script PowerShell suivant montre comment définir les configurations de machine virtuelle et utiliser l’image de machine virtuelle capturée comme source de la nouvelle installation. </br>

	#Enter a new user name and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Vous pouvez afficher la machine virtuelle créée, soit dans le [portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles**, soit en utilisant les commandes PowerShell suivantes :

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Étapes suivantes

Pour gérer votre nouvelle machine virtuelle avec Azure PowerShell, consultez [Gestion des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0525_2016-->