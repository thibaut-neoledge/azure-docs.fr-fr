<properties
	pageTitle="Création et préconfiguration d’une machine virtuelle | Microsoft Azure"
	description="Création et préconfiguration d’une machine virtuelle Azure avec le modèle de déploiement Resource Manager et PowerShell."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/22/2015"
	ms.author="cynthn"/>

# Créer et préconfigurer  une machine virtuelle Windows avec Resource Manager et Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Cet article traite de la création de ressources avec le modèle de déploiement de Resource Manager. Vous pouvez également créer des ressources avec le [modèle de déploiement classique](virtual-machines-ps-create-preconfigure-windows-vms.md).

Ces étapes vous montrent comment construire un jeu de commandes Azure PowerShell qui créent et préconfigurent une machine virtuelle Azure dans le modèle de déploiement Resource Manager. Vous pouvez utiliser ce processus de blocs de construction pour créer un jeu de commandes pour une nouvelle machine virtuelle Windows et pour développer un déploiement existant. Vous pouvez également l’utiliser pour créer plusieurs jeux de commandes qui créent rapidement un environnement de développement/test personnalisé ou un environnement pour professionnels de l’informatique.

Ces étapes utilisent une méthode de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez PowerShell ou simplement si vous souhaitez connaître les valeurs à indiquer pour une configuration réussie. Si vous êtes un utilisateur avancé de PowerShell, vous pouvez utiliser vous-même ces commandes, en y substituant vos propres valeurs aux variables (les lignes commençant par « $ »).

## Étape 1 : installer Azure PowerShell

Vous devez également disposer d’Azure PowerShell version 0.9.0 ou ultérieure. Si vous n’avez pas installé et configuré Azure PowerShell, cliquez [ici](../powershell-install-configure.md) pour obtenir les instructions.

Vous pouvez vérifier la version d’Azure PowerShell que vous avez installée en entrant cette commande à l’invite de commandes d’Azure PowerShell.

	Get-Module azure | format-table version

Voici un exemple.

	Version
	-------
	0.9.0

Si vous n’avez pas la version 0.9.0 ou ultérieure, vous devez supprimer Azure PowerShell à l’aide de Programmes et fonctionnalités du Panneau de configuration, puis installer la version la plus récente. Pour plus d’informations, consultez la rubrique [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

## Étape 2 : Définir votre abonnement

Ouvrez d’abord une invite de commandes Azure PowerShell.

Ensuite, définissez votre abonnement Azure en exécutant ces commandes sur l’invite de commandes Azure PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Vous pouvez obtenir le nom correct de l’abonnement dans le résultat affiché par cette commande.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Ensuite, passez Azure PowerShell en mode Resource Manager.

	Switch-AzureMode AzureResourceManager

## Étape 3 : créer les ressources requises

Les machines virtuelles basées sur Resource Manager requièrent un groupe de ressources. Si nécessaire, créez un groupe de ressources pour votre nouvelle machine virtuelle avec ces commandes. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Pour déterminer un nom de groupe de ressources unique, utilisez cette commande pour répertorier vos groupes de ressources existants.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Pour répertorier les emplacements Azure où vous pouvez créer des machines virtuelles basées sur Resource Manager, utilisez ces commandes.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Les machines virtuelles basées sur Resource Manager requièrent un compte de stockage basé sur Resource Manager. Si nécessaire, créez un compte de stockage pour votre nouvelle machine virtuelle avec ces commandes.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Vous devez choisir pour votre compte de stockage un nom global unique contenant seulement des chiffres et des lettres minuscules. Vous pouvez utiliser cette commande pour répertorier les comptes de stockage existants.

	Get-AzureStorageAccount | Sort Name | Select Name

Pour vérifier si un nom de compte de stockage choisi est globalement unique, vous devez exécuter la commande **Test-AzureName** dans le mode Gestion des services Azure de PowerShell. Utilisez ces commandes.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Si la commande Test-AzureName affiche « False », c’est que le nom proposé est unique. Quand vous avez déterminé un nom unique, rebasculez Azure PowerShell en mode Resource Manager avec cette commande.

	Switch-AzureMode AzureResourceManager

Les machines virtuelles basées sur Resource Manager peuvent utiliser une étiquette de nom de domaine public, qui peut contenir seulement des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.

Pour tester si une étiquette de nom de domaine choisie est globalement unique, utilisez ces commandes.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureDnsAvailability -DomainQualifiedName $domName -Location $loc

Si DNSNameAvailability a la valeur « True », c’est que le nom proposé est globalement unique.

>[AZURE.NOTE]L’applet de commande Test-AzureDnsAvailability était nommée Get-AzureCheckDnsAvailability dans les versions d’Azure PowerShell antérieures à la version 0.9.5. Si vous utilisez la version 0.9.4 ou antérieure, remplacez Test-AzureDnsAvailability par Get-AzureCheckDnsAvailability dans la commande illustrée ci-dessus.

Les machines virtuelles basées sur Resource Manager peuvent être placées dans un groupe à haute disponibilité basé sur Resource Manager. Si nécessaire, créez un groupe à haute disponibilité pour votre nouvelle machine virtuelle avec ces commandes.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Utilisez cette commande pour répertorier les groupes à haute disponibilité existants.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

Les machines virtuelles basées sur Resource Manager peuvent être configurées avec des règles NAT de trafic entrant pour autoriser le trafic entrant provenant d’Internet et pour être placées dans un ensemble à charge équilibrée. Dans les deux cas, vous devez spécifier une instance d’équilibreur de charge et d’autres paramètres. Pour plus d’informations, consultez [Création d’un équilibreur de charge à l’aide d’Azure Resource Manager](../load-balancer/load-balancer-arm-powershell.md)

Les machines virtuelles basées sur Resource Manager requièrent un réseau virtuel basé sur Resource Manager. Si nécessaire, créez un réseau virtuel basé sur Resource Manager avec au moins un sous-réseau pour la nouvelle machine virtuelle. Voici un exemple d’un nouveau réseau virtuel avec deux sous-réseaux nommés frontendSubnet et backendSubnet.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Utilisez ces commandes pour répertorier les réseaux virtuels existants.

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Étape 4 : générer votre jeu de commandes

Ouvrez une nouvelle instance de l’éditeur de texte de votre choix ou l’environnement d’écriture de scripts intégré de PowerShell, et copiez les lignes suivantes au début de votre jeu de commandes. Spécifiez le nom du groupe de ressources, l’emplacement Azure et le compte de stockage pour cette nouvelle machine virtuelle. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Vous devez spécifier le nom d’un réseau virtuel basé sur Resource Manager et le numéro d’index d’un sous-réseau du réseau virtuel. Utilisez ces commandes pour répertorier les sous-réseaux d’un réseau virtuel.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

L’index du sous-réseau est le numéro du sous-réseau dans le résultat affiché par cette commande, avec une numérotation consécutive de gauche à droite et commençant à 0.

Pour cet exemple :

	PS C:\> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

L’index de sous-réseau pour frontendSubnet est 0 et l’index de sous-réseau pour backendSubnet est 1.

Copiez ces lignes dans votre jeu de commandes et spécifiez un nom de réseau virtuel existant, ainsi que l’index de sous-réseau pour la machine virtuelle.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Ensuite, vous créez une carte d’interface réseau. Copiez une des deux options suivantes dans votre jeu de commandes et spécifiez les informations nécessaires.

### Option 1 : spécifier un nom de carte d’interface réseau et affecter une adresse IP publique

Copiez les lignes suivantes dans votre jeu de commandes et spécifiez le nom de la carte d’interface réseau.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Option 2 : spécifier un nom de carte d’interface réseau et une étiquette de nom de domaine DNS

Copiez les lignes suivantes dans votre jeu de commandes, et spécifiez le nom de la carte d’interface réseau et l’étiquette du nom de domaine global unique. Quand vous créez des machines virtuelles dans le mode Gestion des services d’Azure PowerShell, Azure effectue ces étapes automatiquement.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Option 3 : spécifier un nom de carte d’interface réseau et affecter une adresse IP privée statique

Copiez les lignes suivantes dans votre jeu de commandes et spécifiez le nom de la carte d’interface réseau.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Option 4 : spécifier un nom de la carte d’interface réseau et une instance d’équilibreur de charge pour une règle NAT de trafic entrant

Pour créer une carte d’interface réseau et l’ajouter à une instance d’équilibreur de charge pour une règle NAT de trafic entrant, vous avez besoin des éléments suivants :

- le nom d’une instance d’équilibrage de charge précédemment créée qui a une règle NAT de trafic entrant pour le trafic qui est transféré vers la machine virtuelle ;
- le numéro d’index du pool d’adresses principal de l’instance d’équilibreur de charge à affecter à la carte d’interface réseau ;
- le numéro d’index de la règle NAT de trafic entrant à affecter à la carte d’interface réseau.

Pour plus d’informations sur la création d’une instance d’équilibreur de charge avec des règles NAT de trafic entrant, consultez [Créer un équilibreur de charge à l’aide d’Azure Resource Manager](../load-balancer/load-balancer-arm-powershell.md).

Copiez ces lignes dans votre jeu de commandes et spécifiez les noms et numéros d’index nécessaires.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

La chaîne $nicName doit être unique pour le groupe de ressources. Une meilleure pratique consiste à incorporer le nom de la machine virtuelle dans la chaîne, comme « LOB07-NIC ».

### Option 5 : spécifier un nom de carte d’interface réseau et une instance d’équilibreur de charge pour un ensemble à charge équilibrée

Pour créer une carte d’interface réseau et l’ajouter à une instance d’équilibreur de charge pour un ensemble à charge équilibrée, vous avez besoin des éléments suivants :

- le nom d’une instance d’équilibreur de charge précédemment créée ayant une règle pour le trafic à charge équilibrée ;
- le numéro d’index du pool d’adresses principal de l’instance d’équilibreur de charge à affecter à la carte d’interface réseau.

Pour plus d’informations sur la création d’une instance d’équilibreur de charge avec des règles pour le trafic à charge équilibrée, consultez [Créer un équilibreur de charge à l’aide d’Azure Resource Manager](../load-balancer/load-balancer-arm-powershell.md).

Copiez ces lignes dans votre jeu de commandes et spécifiez les noms et numéros d’index nécessaires.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

Ensuite, créez un objet de machine virtuelle locale et éventuellement, ajoutez-le à un groupe à haute disponibilité. Copiez une des deux options suivantes dans votre jeu de commandes, et spécifiez le nom, la taille et le nom du groupe à haute disponibilité.

Option 1 : spécifier un nom et une taille de machine virtuelle.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

Pour déterminer les valeurs possibles de la chaîne de taille de machine virtuelle pour l’option 1, utilisez ces commandes.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

Option 2 : spécifier un nom et une taille de machine virtuelle et les ajouter à un groupe à haute disponibilité.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Pour déterminer les valeurs possibles de la chaîne de taille de machine virtuelle pour l’option 2, utilisez ces commandes.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Actuellement, avec Resource Manager, vous pouvez ajouter une machine virtuelle à un groupe à haute disponibilité seulement lors de sa création.

Pour ajouter un disque de données supplémentaire à la machine virtuelle, copiez ces lignes dans votre jeu de commandes et spécifiez les paramètres du disque.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

Ensuite, vous devez déterminer le serveur de publication, l’offre et la référence (SKU) de l’image pour votre machine virtuelle. Voici un tableau d’images basées sur Windows couramment utilisées.

|Nom du serveur de publication | Nom de l’offre | Nom de la référence (SKU)
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour l’entrepôt de données |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Entreprise, optimisé pour le traitement transactionnel en ligne |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Si l’image de machine virtuelle dont vous avez besoin n’est pas répertoriée, suivez les instructions données [ici](resource-groups-vm-searching.md#powershell) pour déterminer les noms du serveur de publication, de l’offre et de la référence (SKU).

Copiez ces commandes dans votre jeu de commandes et spécifiez les noms du serveur de publication, de l’offre et de la référence (SKU).

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

Enfin, copiez ces commandes dans votre jeu de commandes et spécifiez l’identificateur du nom pour le disque du système d’exploitation de la machine virtuelle.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Étape 5 : exécuter votre jeu de commandes

Passez en revue le jeu de commandes Azure PowerShell que vous avez créé dans votre éditeur de texte ou dans l’environnement d’écriture de scripts intégré de PowerShell, constitué de plusieurs blocs de commandes de l’étape 4. Vérifiez que vous avez spécifié toutes les variables nécessaires et qu’elles ont les valeurs correctes. Vérifiez également que vous avez supprimé tous les caractères < and >.

Si vos commandes sont dans un éditeur de texte, copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Azure PowerShell ouverte. Vous émettez ainsi le jeu de commandes en tant que série de commandes PowerShell et créez votre machine virtuelle Azure. Vous pouvez aussi exécuter le jeu de commandes depuis l’environnement d’écriture de scripts intégré d’Azure PowerShell.

Si vous devez créer à nouveau cette machine virtuelle ou une machine virtuelle similaire, vous pouvez enregistrer ce jeu de commandes en tant que fichier de script PowerShell (*.ps1).

## Exemple

J’ai besoin d’un jeu de commandes PowerShell pour créer une machine virtuelle supplémentaire pour une charge de travail métier web qui :

- est placée dans le groupe de ressources existant LOBServers ;
- utilise l’image Windows Server 2012 R2 Datacenter ;
- porte le nom LOB07 et est dans le groupe à haute disponibilité WEB\_AS existant ;
- a une carte d’interface réseau avec une adresse IP publique dans le sous-réseau FrontEnd (index de sous-réseau 0) du réseau virtuel AZDatacenter existant ;
- a un disque de données supplémentaire de 200 Go.

Voici le jeu de commandes Azure PowerShell correspondant pour créer cette machine virtuelle, basé sur le processus décrit à l’étape 4.

	# Switch to the Resource Manager mode
	Switch-AzureMode AzureResourceManager

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Ressources supplémentaires

[Fournisseurs de calcul, de réseau et de stockage Azure dans Azure Resource Manager](virtual-machines-azurerm-versus-azuresm.md)

[Présentation d’Azure Resource Manager](../resource-group-overview.md)

[Déployer et gérer des machines virtuelles Azure à l’aide de modèles Resource Manager et de PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Création d’une machine virtuelle Windows avec un modèle Resource Manager et PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Installation et configuration d’Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Sept15_HO4-->