<properties
	pageTitle="Création d’une copie de votre machine virtuelle Linux | Microsoft Azure"
	description="Découvrez comment créer une copie de votre machine virtuelle Azure exécutant Linux dans le modèle de déploiement Resource Manager en créant une *image spécialisée*."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="cynthn"/>

# Créer une copie d’une machine virtuelle Linux dans le modèle de déploiement Azure Resource Manager


Cet article explique comment créer une copie de votre machine virtuelle Azure (VM) exécutée sous Linux. Plus précisément, il décrit comment effectuer cette opération dans le modèle de déploiement Resource Manager, à l’aide de la CLI Azure et du portail Azure. Il décrit comment créer une image *spécialisée* de votre machine virtuelle Azure qui gère les comptes d’utilisateurs et d’autres données d’état à partir de votre machine virtuelle d’origine. Une image spécialisée est utile pour porter votre machine virtuelle Linux d’un modèle de déploiement classique vers le modèle de déploiement Resource Manager, ou pour créer une copie de sauvegarde de votre machine virtuelle Linux créée dans le modèle de déploiement Resource Manager. Vous pouvez copier les disques du système d’exploitation et les disques de données de cette manière, puis configurer les ressources réseau pour créer la nouvelle machine virtuelle.

Si vous avez besoin de créer des déploiements en nombre de machines virtuelles Linux similaires, nous vous recommandons d’utiliser une image *généralisée*. Pour ce faire, consultez la page [Comment capturer une machine virtuelle Linux pour utiliser un modèle de gestionnaire de ressources](virtual-machines-linux-capture-image.md).



## Avant de commencer

Assurez-vous de disposer des composants requis suivants avant de commencer la procédure :

- Vous disposez d’une machine virtuelle Azure exécutant Linux, que vous avez créée à l’aide du modèle de déploiement classique ou du modèle de déploiement Resource Manager. Vous avez configuré le système d’exploitation et les disques de données associés, et effectué d’autres procédures de personnalisation, telles que l’installation des applications requises. Vous utiliserez cette machine virtuelle pour créer la copie. Si vous avez besoin d’aide pour la machine virtuelle source, consultez l’article [Créer une machine virtuelle Linux dans Azure](virtual-machines-linux-quick-create-cli.md).

- Vous avez téléchargé et installé l’interface de ligne de commande Azure sur votre machine et vous êtes connecté à votre abonnement Azure. Pour plus d’informations, voir [Installer l’interface de ligne de commande Microsoft Azure](../xplat-cli-install.md).

- Vous disposez d’un groupe de ressources, d’un compte de stockage et d’un conteneur d’objets blob créés dans ce groupe de ressources pour y copier les disques durs virtuels. Pour en savoir plus sur la création de comptes de stockage et de conteneurs d’objets blob, consultez l’article [Utilisation de l’interface de ligne de commande Microsoft Azure avec Microsoft Azure Storage](../storage/storage-azure-cli.md).

> [AZURE.NOTE] Des étapes similaires s’appliquent pour une machine virtuelle créée en utilisant l’un des deux modèles de déploiement comme image source. Le cas échéant, cet article vous indique les différences mineures.


## Copie de disques durs virtuels sur votre compte de stockage Resource Manager


1. Libérez les disques durs virtuels utilisés par la machine virtuelle source en exécutant l’une des procédures suivantes :

	- Pour copier votre machine virtuelle source, vous devez l’arrêter, puis la libérer. Dans le portail, cliquez sur **Parcourir** > **Machines virtuelles** ou **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Arrêter**. Pour les machines virtuelles créées à l’aide du modèle de déploiement Resource Manager, vous pouvez également utiliser la commande de l’interface de ligne de commande Azure `azure vm stop <yourResourceGroup> <yourVmName>` suivie de `azure vm deallocate <yourResourceGroup> <yourVmName>`. Notez que l’état de la machine virtuelle dans le portail passe de la valeur **En cours d’exécution** à la valeur **Arrêté (désalloué)**.

	- Si vous souhaitez effectuer la migration de votre machine virtuelle source, supprimez cette machine et utilisez le disque dur virtuel désormais disponible. Accédez à votre machine virtuelle sur le [portail](https://portal.azure.com), puis cliquez sur **Supprimer**.

1. Recherchez la clé d’accès du compte de stockage qui contient votre disque dur virtuel source. Pour plus d’informations sur les clés d’accès, consultez la page [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

	- Si votre machine virtuelle source a été créée à l’aide du modèle de déploiement Classic, cliquez sur **Parcourir** > **Comptes de stockage (classiques)** > *votre compte de stockage* > **Tous les paramètres** > **Clés**. Copiez la clé portant le nom **CLÉ D’ACCÈS PRIMAIRE**. Dans l’interface de ligne de commande Azure, vous pouvez également activer le mode Classic en utilisant la commande `azure config mode asm`, puis utiliser la commande `azure storage account keys list <yourSourceStorageAccountName>`.

	- Si votre machine virtuelle source a été créée à l’aide du modèle de déploiement Resource Manager, cliquez sur **Parcourir** > **Comptes de stockage** > *votre compte de stockage* > **Tous les paramètres** > **Clés d’accès**. Copiez le texte portant le nom **key1**. Dans l’interface de ligne de commande Azure, vous pouvez également vérifier que le mode Resource Manager est activé à l’aide de la commande `azure config mode arm`, puis utiliser la commande `azure storage account keys list -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>`.

1. Copiez les fichiers du disque dur virtuel à l’aide des [commandes de l’interface de ligne de commande Azure pour le stockage](../storage/storage-azure-cli.md), comme décrit dans les étapes suivantes. Si vous préférez passer par l’interface utilisateur, vous pouvez aussi utiliser l’[Explorateur de stockage Microsoft Azure](http://storageexplorer.com/). </br>
	1. Configurez la chaîne de connexion pour le compte de stockage de destination. Cette chaîne de connexion contient la clé d’accès pour ce compte de stockage.

			$azure storage account connectionstring show -g <yourDestinationResourceGroup> <yourDestinationStorageAccount>
			$export AZURE_STORAGE_CONNECTION_STRING=<the_connectionstring_output_from_above_command>

	2. Créez une [signature d’accès partagé](../storage/storage-dotnet-shared-access-signature-part-1.md) pour le fichier VHD dans le compte de stockage source. Notez l’**URL d’accès partagé** obtenue avec la commande suivante.

			$azure storage blob sas create  --account-name <yourSourceStorageAccountName> --account-key <SourceStorageAccessKey> --container <SourceStorageContainerName> --blob <FileNameOfTheVHDtoCopy> --permissions "r" --expiry <mm/dd/yyyy_when_you_want_theSAS_to_expire>

	3. Utilisez la commande suivante pour copier le disque dur virtuel du stockage source vers le stockage de destination.

			$azure storage blob copy start <SharedAccessURL_ofTheSourceVHD> <DestinationContainerName>

	4. Le fichier VHD est copié de manière asynchrone. Pour suivre la progression, utilisez la commande suivante.

			$azure storage blob copy show <DestinationContainerName> <FileNameOfTheVHDtoCopy>

</br>

>[AZURE.NOTE] Vous devez copier séparément les disques de système d’exploitation et de données, comme décrit précédemment.


## Création d’une machine virtuelle à l’aide du disque dur virtuel copié

Vous pouvez maintenant utiliser l’interface de ligne de commande Azure pour créer une machine virtuelle Linux basée sur Resource Manager dans un nouveau réseau virtuel en utilisant le disque dur virtuel copié dans les étapes précédentes. Le disque dur virtuel doit être présent dans le même compte de stockage que la machine virtuelle qui sera créée.


Configurez un réseau virtuel et une carte réseau pour votre nouvelle machine virtuelle en utilisant le script suivant. Remplacez les variables par les valeurs correspondant à votre application.

	$azure network vnet create <yourResourceGroup> <yourVnetName> -l <yourLocation>

	$azure network vnet subnet create <yourResourceGroup> <yourVnetName> <yourSubnetName>

	$azure network public-ip create <yourResourceGroup> <yourIpName> -l <yourLocation>

	$azure network nic create <yourResourceGroup> <yourNicName> -k <yourSubnetName> -m <yourVnetName> -p <yourIpName> -l <yourLocation>


Créez la machine virtuelle à l’aide des disques durs virtuels copiés. Pour ce faire, utilisez la commande suivante. </br>

	$azure vm create -g <yourResourceGroup> -n <yourVmName> -f <yourNicName> -d <UriOfYourOsDisk> -x <UriOfYourDataDisk> -e <DataDiskSizeGB> -Y -l <yourLocation> -y Linux -z "Standard_A1" -o <DestinationStorageAccountName> -R <DestinationStorageAccountBlobContainer>


Les URL des disques de données et des disques du système d’exploitation se présentent comme suit : `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pour obtenir les URL, rendez-vous sur le portail, accédez au conteneur de stockage, cliquez sur le disque dur virtuel du système d’exploitation ou de données qui a été copié, puis copiez le contenu de l’URL.


Si la commande a été exécutée avec succès, vous obtiendrez une sortie similaire à celle-ci :

	$azure vm create -g "testcopyRG" -n "redhatcopy" -f "LinCopyNic" -d https://testcopystore.blob.core.windows.net/testcopyblob/RedHat201631816334.vhd -x https://testcopystore.blob.core.windows.net/testcopyblob/RedHat-data.vhd -e 10 -Y -l "West US" -y Linux -z "Standard_A1" -o "testcopystore" -R "testcopyblob"
	info:    Executing command vm create
	+ Looking up the VM "redhatcopy"
	info:    Using the VM Size "Standard_A1"
	+ Looking up the NIC "LinCopyNic"
	info:    Found an existing NIC "LinCopyNic"
	info:    Found an IP configuration with virtual network subnet id "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourceGroups/testcopyRG/providers/Microsoft.Network/virtualNetworks/LinCopyVnet/subnets/LinCopySub" in the NIC "LinCopyNic"
	info:    This NIC IP configuration has a public ip already configured "/subscriptions/b8e6a92b-d6b7-4dbb-87a8-3c8dfe248156/resourcegroups/testcopyrg/providers/microsoft.network/publicipaddresses/lincopyip", any public ip parameters if provided, will be ignored.
	+ Looking up the storage account testcopystore
	info:    The storage URI 'https://testcopystore.blob.core.windows.net/' will be used for boot diagnostics settings, and it can be overwritten by the parameter input of '--boot-diagnostics-storage-uri'.
	+ Creating VM "redhatcopy"
	info:    vm create command OK

La machine virtuelle que vous venez de créer devrait apparaître dans le [Portail Azure](https://portal.azure.com) sous **Parcourir** > **Machines virtuelles**.

Connectez-vous à votre nouvelle machine virtuelle à l’aide du client SSH de votre choix, puis utilisez les informations d’identification de compte de votre machine virtuelle d’origine (par exemple, `ssh OldAdminUser@<IPaddressOfYourNewVM>`). Pour plus d'informations, consultez [Utilisation de SSH avec Linux dans Azure](virtual-machines-linux-ssh-from-linux.md).


## Étapes suivantes

Pour plus d’informations sur l’utilisation de l’interface de ligne de commande Azure pour gérer votre nouvelle machine virtuelle, voir [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager](azure-cli-arm-commands.md).

<!---HONumber=AcomDC_0525_2016-->