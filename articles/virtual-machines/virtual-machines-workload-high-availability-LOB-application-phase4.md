<properties 
	pageTitle="Application métier - Phase 4 | Microsoft Azure" 
	description="Créez les serveurs web et chargez votre application métier dessus dans la phase 4 de l'application métier dans Azure." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/20/2015" 
	ms.author="josephd"/>

# Charge de travail des applications métier, phase 4 : configurer des serveurs Web

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Au cours de cette phase de déploiement d'une application métier à haute disponibilité dans des services d'infrastructure Azure, vous créez les serveurs Web et y chargez votre application métier.

Vous devez procéder à cette opération avant de passer à la [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Consultez [Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) pour toutes les phases.

## Création de machines virtuelles de serveur Web dans Azure

Il existe deux serveurs Web virtuels dans lesquels vous pouvez déployer des applications ASP.NET ou des applications plus anciennes pouvant être hébergées par les Internet Information Services (IIS) 8 dans Windows Server 2012 R2.

Tout d'abord, vous configurez l'équilibrage de charge interne de manière à ce qu'Azure répartisse le trafic client vers l'application métier de manière équitable entre les deux serveurs Web. Pour cela, vous devez spécifier une instance d'équilibrage de charge interne comportant un nom et une adresse IP propre qui sont affectés à partir de l'espace d'adressage du sous-réseau que vous avez affecté à votre réseau virtuel Azure.

> [AZURE.NOTE]Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Spécifiez les valeurs des variables en supprimant les caractères < and >. Notez que les jeux de commandes PowerShell suivants utilisent les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table ST pour vos comptes de stockage ;
- Table A pour les groupes à haute disponibilité ;

Souvenez-vous que vous avez défini la table M au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) et les tables V, S, ST et A au cours de la [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Ajoutez ensuite un enregistrement d'adresse DNS à l'infrastructure DNS de votre organisation pour résoudre le nom de domaine complet de l'application métier (par exemple, lobapp.corp.contoso.com) au niveau de l'adresse IP affectée à l'équilibrage de charge interne (la valeur de $privIP dans le bloc de commandes Azure PowerShell précédent).

Utilisez ensuite le bloc de commandes PowerShell suivant permet de créer les machines virtuelles pour les deux serveurs Web.

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant dans l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Étant donné que ces machines virtuelles sont destinées à une application intranet, elles ne reçoivent pas d’adresse IP publique ou d’étiquette de nom de domaine DNS, et ne sont pas exposées à Internet. Toutefois, cela signifie également que vous ne pouvez pas vous y connecter à partir du portail Azure. Le bouton **Se connecter** n’est pas disponible quand vous affichez les propriétés de la machine virtuelle.

Utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance à chaque machine virtuelle de serveur Web. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Associez chaque machine virtuelle de serveur Web au domaine Active Directory approprié en utilisant les commandes suivantes dans l'invite de commandes Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Notez que vous devez fournir les informations d’identification de compte de domaine après avoir entré la commande **Add-Computer**.

Après le redémarrage, connectez-vous de nouveau à l'aide d'un compte disposant de privilèges d'administrateur local.

Ensuite, installez et configurez IIS pour chaque serveur Web.

1. Exécutez le Gestionnaire de serveur, puis cliquez sur **Ajouter des rôles et fonctionnalités**.
2. Dans la page Avant de commencer, cliquez sur **Suivant**.
3. Dans la page Sélectionner le type d’installation, cliquez sur **Suivant**.
4. Dans la page Sélectionner le serveur de destination, cliquez sur **Suivant**.
5. Dans la page Rôles de serveur, cliquez sur **Serveur Web (IIS)** dans la liste des **rôles**.
6. Quand vous y êtes invité, cliquez sur **Ajouter des fonctionnalités**, puis sur **Suivant**.
7. Dans la page Sélectionner des fonctionnalités, cliquez sur **Suivant**.
8. Dans la page Serveur Web (IIS), cliquez sur **Suivant**.
9. Dans la page Sélectionner des services de rôle, cochez ou décochez les cases pour les services requis pour votre application métier, puis cliquez sur **Suivant**. 10. Dans la page Confirmer les sélections pour l’installation, cliquez sur **Installer**.

## Déployer votre application métier sur les machines virtuelles du serveur web

À partir d'un ordinateur sur votre réseau local :

1.	Ajoutez les fichiers de votre application métier dans les deux serveurs Web.
2.	Créez des bases de données pour votre application métier dans le cluster SQL Server.
3.	Testez l'accès à votre application métier et à ses fonctionnalités.

Ce diagramme représente la configuration résultant de l'exécution de cette phase.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase4/workload-lobapp-phase4.png)

## Étape suivante

- Pour terminer la configuration de cette charge de travail, utilisez la [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md).

<!---HONumber=AcomDC_1217_2015-->