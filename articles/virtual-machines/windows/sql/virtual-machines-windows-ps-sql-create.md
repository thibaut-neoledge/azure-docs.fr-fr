---
title: "Créer une machine virtuelle SQL Server dans Azure PowerShell (Resource Manager) | Microsoft Docs"
description: "Fournit une procédure et des scripts PowerShell pour la création d’une machine virtuelle Azure à l’aide des images de la galerie de machines virtuelles SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/17/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 7402249aa87ffe985ae13f28a701e22af3afd450
ms.openlocfilehash: 7706fd453ef8be4ebe65b65da643ec38b7c18433


---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Approvisionner une machine virtuelle SQL Server à l’aide d’Azure PowerShell (Resource Manager)
> [!div class="op_single_selector"]
> * [Portail](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous montre comment créer une machine virtuelle Azure à l’aide du modèle de déploiement **Azure Resource Manager** et d’applets de commande Azure PowerShell. Dans ce didacticiel, nous allons créer une machine virtuelle à l’aide d’un lecteur de disque à partir d’une image dans la galerie SQL. Nous allons créer des fournisseurs pour les ressources de stockage, de réseau et de calcul qui seront utilisées par la machine virtuelle. Si vous avez déjà des fournisseurs pour ces ressources, vous pouvez les utiliser.

Si vous avez besoin de la version classique de cette rubrique, consultez la page [Approvisionner une machine virtuelle SQL Server à l’aide d’Azure PowerShell (Classic)](../sqlclassic/virtual-machines-windows-classic-ps-sql-create.md).

## <a name="prerequisites"></a>Configuration requise
Pour ce didacticiel, vous devez disposer des éléments suivants :

* Un compte Azure et un abonnement, avant de commencer. Si vous n’en avez pas, inscrivez-vous pour un [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
* [Azure PowerShell](/powershell/azureps-cmdlets-docs)1.4.0 ou version ultérieure (ce didacticiel a été écrit avec la version 1.5.0).
  * Pour récupérer votre version, tapez **Get-Module Azure -ListAvailable**.

## <a name="configure-your-subscription"></a>Configurer votre abonnement
Ouvrez Windows PowerShell et accédez à votre compte Azure en exécutant l’applet de commande suivante. Un écran de connexion vous invite à entrer vos informations d’identification. Utilisez l'adresse électronique et le mot de passe que vous utilisez pour vous connecter au portail Azure.

    Add-AzureRmAccount

Une fois que vous êtes connecté, l’écran affiche plusieurs informations, dont l’ID d’abonnement avec lequel vous vous êtes identifié. Il s’agit de l’abonnement dans lequel les ressources de ce didacticiel vont être créées, sauf si vous en changez. Si vous avez plusieurs ID d’abonnement, exécutez l’applet de commande suivante pour renvoyer la liste de tous vos ID d’abonnement :

    Get-AzureRmSubscription

Pour basculer vers un autre ID d’abonnement, exécutez l’applet de commande avec l’ID d’abonnement souhaité.

    Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## <a name="define-image-variables"></a>Définir des variables d’image
Pour simplifier l’utilisation et la compréhension du script complet de ce didacticiel, nous allons commencer par définir plusieurs variables. Modifiez les valeurs des paramètres comme vous le souhaitez, mais sachez que des restrictions s’appliquent à la longueur des noms et aux caractères spéciaux en cas de modification des valeurs fournies.

### <a name="location-and-resource-group"></a>Emplacement et groupe de ressources
Utilisez deux variables pour définir la région des données et le groupe de ressources dans lequel vous allez créer les autres ressources de la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez les applets de commande suivantes pour initialiser ces variables.

    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### <a name="storage-properties"></a>Propriétés de stockage
Utilisez les variables suivantes pour définir le compte de stockage et le type de stockage à utiliser par la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables. Notez que, dans cet exemple, nous utilisons [Premium Storage](../../../storage/storage-premium-storage.md), qui est recommandé pour les charges de travail de production. Pour plus d’informations et d’autres recommandations, consultez [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

### <a name="network-properties"></a>Propriétés du réseau
Utilisez les variables suivantes pour définir l’interface réseau, la méthode d’allocation TCP/IP, le nom du réseau virtuel, le nom du sous-réseau virtuel, la plage d’adresses IP du réseau virtuel, la plage d’adresses IP du sous-réseau et le libellé du nom de domaine public à utiliser par le réseau dans la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"

### <a name="virtual-machine-properties"></a>Propriétés de machine virtuelle
Utilisez les variables suivantes pour définir le nom de la machine virtuelle, le nom de l’ordinateur, la taille de la machine virtuelle et le nom du disque du système d’exploitation de la machine virtuelle.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### <a name="image-properties"></a>Propriétés de l’image
Utilisez les variables suivantes pour définir l’image à utiliser pour la machine virtuelle. Dans cet exemple, l’image d’évaluation SQL Server 2016 Enterprise est utilisée.

Apportez les modifications souhaitées, puis exécutez l’applet de commande suivante pour initialiser ces variables.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

Notez que vous pouvez obtenir la liste complète des images SQL Server avec la commande Get-AzureRmVMImageOffer :

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

Et vous pouvez voir les références SKU disponibles pour une offre avec la commande Get-AzureRmVMImageSku. La commande suivante affiche toutes les références disponibles pour l’offre **SQL2014SP1-WS2012R2** .

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Avec le modèle de déploiement Resource Manager, le premier objet que vous créez est le groupe de ressources. Nous allons utiliser l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt759837.aspx) pour créer un groupe de ressources Azure et ses ressources avec le nom et l’emplacement du groupe de ressources définis par les variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer votre groupe de ressources.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## <a name="create-a-storage-account"></a>Créer un compte de stockage
La machine virtuelle nécessite des ressources de stockage pour le disque du système d’exploitation ainsi que pour les données et fichiers journaux de SQL Server. Pour plus de simplicité, nous allons créer un seul disque pour les deux. Vous pouvez attacher des disques supplémentaires ultérieurement, à l’aide de l’applet de commande [Add-Azure Disk](https://msdn.microsoft.com/library/azure/dn495252.aspx) , pour placer vos données et vos fichiers journaux SQL Server sur des disques dédiés. Nous allons utiliser l’applet de commande [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) pour créer un compte de stockage standard dans votre nouveau groupe de ressources, avec le nom du compte de stockage, le nom de référence du stockage et l’emplacement définis à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer votre compte de stockage.

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

## <a name="create-network-resources"></a>Créer des ressources réseau
La machine virtuelle requiert plusieurs ressources réseau pour la connectivité réseau.

* Chaque machine virtuelle requiert un réseau virtuel.
* Un réseau virtuel doit avoir au moins un sous-réseau.
* Une interface réseau doit être définie avec une adresse IP privée ou publique.

### <a name="create-a-virtual-network-subnet-configuration"></a>Créer une configuration de sous-réseau de réseau virtuel
Nous allons commencer par créer une configuration de sous-réseau pour notre réseau virtuel. Dans notre didacticiel, nous allons créer un sous-réseau par défaut à l’aide de l’applet de commande [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) . Nous allons créer notre configuration de sous-réseau de réseau virtuel avec le nom et le préfixe d’adresse définis à l’aide des variables déjà initialisées.

> [!NOTE]
> Vous pouvez définir des propriétés supplémentaires dans la configuration de sous-réseau de réseau virtuel à l’aide de cette applet de commande, mais cela sort du cadre de ce didacticiel.
>
>

Exécutez l’applet de commande suivante pour créer la configuration de votre sous-réseau virtuel.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### <a name="create-a-virtual-network"></a>Créez un réseau virtuel
Ensuite, nous allons créer notre réseau virtuel à l’aide de l’applet de commande [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) . Nous allons créer notre réseau virtuel dans votre nouveau groupe de ressources, avec le nom, l’emplacement et le préfixe d’adresse définis avec les variables que vous avez déjà initialisées, et à l’aide de la configuration de sous-réseau définie à l’étape précédente.

Exécutez l’applet de commande suivante pour créer votre réseau virtuel.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### <a name="create-the-public-ip-address"></a>Créer une adresse IP publique
Maintenant que nous avons défini notre réseau virtuel, nous devons configurer une adresse IP pour la connectivité à la machine virtuelle. Dans ce didacticiel, nous allons créer une adresse IP publique à l’aide de l’adressage IP dynamique pour la connectivité Internet. Nous allons utiliser l’applet de commande [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) pour créer l’adresse IP publique dans le groupe de ressources déjà créé, avec le nom, l’emplacement, la méthode d’allocation et le libellé du nom de domaine DNS définis à l’aide des variables que vous avez déjà initialisées.

> [!NOTE]
> Vous pouvez définir des propriétés supplémentaires de l’adresse IP publique à l’aide de cette applet de commande, mais cela sort du cadre de ce didacticiel. Vous pouvez également créer une adresse privée ou une adresse avec une adresse statique, mais cela sort du cadre de ce didacticiel.
>
>

Exécutez l’applet de commande suivante pour créer votre adresse IP publique.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### <a name="create-the-network-interface"></a>Créer l’interface réseau
Nous sommes maintenant prêts à créer l’interface réseau que notre machine virtuelle utilisera. Nous allons utiliser l’applet de commande [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) pour créer notre interface réseau dans le groupe de ressources déjà créé, et avec le nom, l’emplacement, le sous-réseau et l’adresse IP publique définis auparavant.

Exécutez l’applet de commande suivante pour créer votre interface réseau.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## <a name="configure-a-vm-object"></a>Configurer un objet de machine virtuelle
Maintenant que nous avons défini les ressources réseau et de stockage, nous pouvons définir les ressources de calcul de la machine virtuelle. Dans notre didacticiel, nous allons configurer la taille de la machine virtuelle et plusieurs propriétés du système d’exploitation, spécifier l’interface réseau que nous avons créée auparavant, définir le Blob Storage et identifier le disque du système d’exploitation.

### <a name="create-the-vm-object"></a>Créer l’objet de machine virtuelle
Nous allons commencer par spécifier la taille de la machine virtuelle. Dans ce didacticiel, nous spécifions une DS13. Nous allons utiliser l’applet de commande [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) pour créer une machine virtuelle configurable, avec le nom et la taille définis à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour créer l’objet de machine virtuelle.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Créer un objet d’informations d’identification pour stocker le nom et le mot de passe de l’administrateur local
Avant de définir les propriétés du système d’exploitation de la machine virtuelle, nous devons indiquer les informations d’identification du compte d’administrateur local sous la forme d’une chaîne de caractères sécurisée. Pour ce faire, nous allons utiliser l’applet de commande [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Exécutez l’applet de commande suivante puis, dans la fenêtre de demande d’informations d’identification Windows PowerShell, tapez le nom et le mot de passe à utiliser pour le compte d’administrateur local sur la machine virtuelle Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Configurer les propriétés du système d’exploitation de la machine virtuelle
Nous pouvons maintenant configurer les propriétés du système d’exploitation de la machine virtuelle. Nous allons utiliser l’applet de commande [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) pour configurer le système d’exploitation Windows, exiger l’installation de [l’agent de machine virtuelle](../../virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json), spécifier que l’applet de commande active la mise à jour automatique et définir le nom de la machine virtuelle, le nom de l’ordinateur et les informations d’identification à l’aide des variables que vous avez déjà initialisées.

Exécutez l’applet de commande suivante pour définir les propriétés du système d’exploitation de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Ajouter l’interface réseau à la machine virtuelle
Ensuite, nous allons ajouter l’interface réseau précédemment créée à la machine virtuelle. Nous allons utiliser l’applet de commande [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) pour ajouter l’interface réseau à l’aide de la variable d’interface réseau définie auparavant.

Exécutez l’applet de commande suivante pour définir l’interface réseau de votre machine virtuelle.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Définir l’emplacement de Blob Storage du disque à utiliser par la machine virtuelle
Ensuite, nous allons définir l’emplacement de Blob Storage du disque à utiliser par la machine virtuelle à l’aide des variables définies auparavant.

Exécutez l’applet de commande suivante pour définir l’emplacement de Blob Storage.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Configurer les propriétés du disque du système d’exploitation de la machine virtuelle
Ensuite, nous allons configurer les propriétés du disque du système d’exploitation de la machine virtuelle. Nous allons utiliser l’applet de commande [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) pour spécifier que le système d’exploitation de la machine virtuelle proviendra d’une image, pour définir la mise en cache en lecture seule (car SQL Server est installé sur le même disque) et spécifier le nom de la machine virtuelle ainsi que le disque du système d’exploitation définis à l’aide des variables configurées auparavant.

Exécutez l’applet de commande suivante pour configurer les propriétés du disque du système d’exploitation de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Spécifier l’image de plateforme de la machine virtuelle
Notre dernière étape de configuration consiste à spécifier l’image de plateforme de notre machine virtuelle. Dans notre didacticiel, nous utilisons la dernière image en date de SQL Server 2016 CTP. Nous allons utiliser l’applet de commande [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) pour utiliser cette image définie par les variables définies auparavant.

Exécutez l’applet de commande suivante pour définir l’image de plateforme de votre machine virtuelle.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## <a name="create-the-sql-vm"></a>Créer la machine virtuelle SQL
Une fois les étapes de configuration terminées, vous pouvez créer la machine virtuelle. Nous allons utiliser l’applet de commande [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) pour créer la machine virtuelle à l’aide des variables que nous avons définies.

Exécutez l’applet de commande suivante pour créer votre machine virtuelle.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

La machine virtuelle est créée. Remarquez qu’un compte de stockage standard est créé pour les diagnostics de démarrage, car le compte de stockage spécifié pour le disque de la machine virtuelle est un compte Premium Storage.

Vous pouvez maintenant afficher cette machine dans le portail Azure pour [voir son adresse IP publique et son nom de domaine complet](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Exemple de script
Le script suivant contient le script PowerShell complet pour ce didacticiel. Nous considérons que vous avez déjà configuré l’abonnement Azure à utiliser avec les commandes **Add-AzureRmAccount** et **Select-AzureRmSubscription**.

    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageSku = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016-WS2016"
    $Sku = "Enterprise"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## <a name="next-steps"></a>Étapes suivantes
Une fois la machine virtuelle créée, vous pouvez vous y connecter à l’aide du protocole RDP et en configurant la connectivité. Pour plus d’informations, consultez [Se connecter à une machine virtuelle SQL Server sur Azure (Resource Manager)](virtual-machines-windows-sql-connect.md).




<!--HONumber=Jan17_HO2-->


