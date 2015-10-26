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
	ms.date="08/11/2015" 
	ms.author="josephd"/>

# Charge de travail des applications métier, phase 4 : configurer des serveurs Web

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique
 

Au cours de cette phase de déploiement d'une application métier à haute disponibilité dans des services d'infrastructure Azure, vous créez les serveurs Web et y chargez votre application métier.

Vous devez procéder à cette opération avant de passer à la [Phase 5](virtual-machines-workload-high-availability-LOB-application-phase5.md). Consultez [Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) pour toutes les phases.

## Création de machines virtuelles de serveur Web dans Azure

Il existe deux serveurs Web virtuels dans lesquels vous pouvez déployer des applications ASP.NET ou des applications plus anciennes pouvant être hébergées par les Internet Information Services (IIS) 8 dans Windows Server 2012 R2.

> [AZURE.NOTE]Cet article contient des commandes pour les versions d’Azure PowerShell *antérieures* à la version 1.0.0. Vous pouvez déterminer votre version d’Azure PowerShell à l’aide de la commande **Get-Module azure | format-table version**. Les blocs de commande Azure PowerShell dans cet article font actuellement l’objet de tests et de mises à jour pour prendre en charge les nouvelles applets de commande dans les versions Azure PowerShell 1.0.0 et ultérieures. Nous vous remercions de votre patience.

Tout d'abord, vous configurez l'équilibrage de charge interne de manière à ce qu'Azure répartisse le trafic client vers l'application métier de manière équitable entre les deux serveurs Web. Pour cela, vous devez spécifier une instance d'équilibrage de charge interne comportant un nom et une adresse IP propre qui sont affectés à partir de l'espace d'adressage du sous-réseau que vous avez affecté à votre réseau virtuel Azure. Pour déterminer si l'adresse IP que vous avez choisie pour l'équilibrage de charge interne est disponible, utilisez les commandes suivantes dans l'invite de commandes Azure PowerShell. Spécifiez les valeurs des variables et supprimez les caractères < and >.

	Switch-AzureMode AzureServiceManagement
	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 2 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Si le champ **IsAvailable** de la commande Test-AzureStaticVNetIP est défini sur **True**, vous pouvez utiliser l’adresse IP.

Revenez au mode Resource Manager de PowerShell avec cette commande.

	Switch-AzureMode AzureResourceManager

Ensuite, renseignez les variables et exécutez l'ensemble des commandes suivant :

	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Ajoutez ensuite un enregistrement d'adresse DNS à l'infrastructure DNS de votre organisation pour résoudre le nom de domaine complet de l'application métier (par exemple, lobapp.corp.contoso.com) au niveau de l'adresse IP affectée à l'équilibrage de charge interne (la valeur de $privIP dans le bloc de commandes Azure PowerShell précédent).

Utilisez ensuite le bloc de commandes PowerShell suivant permet de créer les machines virtuelles pour les deux serveurs Web. Ce jeu de commandes PowerShell utilise les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table ST pour vos comptes de stockage ;
- Table A pour les groupes à haute disponibilité ;

Souvenez-vous que vous avez défini la table M au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md), et les tables V, S, ST et A au cours de la [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant dans l'invite de commandes Azure PowerShell.

	# Set up subscription and key variables
	$subscr="<name of the Azure subscription>"
	Set-AzureSubscription -SubscriptionName $subscr
	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second second SQL Server computer." 
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Étant donné que ces machines virtuelles sont destinées à une application intranet, elles ne reçoivent pas d’adresse IP publique ou d’étiquette de nom de domaine DNS, et ne sont pas exposées à Internet. Toutefois, cela signifie également que vous ne pouvez pas vous y connecter à partir du portail Azure en version préliminaire. Le bouton **Se connecter** n’est pas disponible quand vous affichez les propriétés de la machine virtuelle.

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

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 5 : création du groupe de disponibilité et ajout des bases de données de l’application](virtual-machines-workload-high-availability-LOB-application-phase5.md).

## Ressources supplémentaires

[Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md) (en anglais)

[Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO3-->