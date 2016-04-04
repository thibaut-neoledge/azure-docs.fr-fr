<properties
	pageTitle="Batterie de serveurs SharePoint Server 2013 - Phase 4 | Microsoft Azure"
	description="Créez les machines virtuelles du serveur SharePoint et une batterie de serveurs SharePoint dans la phase 4 de la batterie de serveurs SharePoint Server 2013 dans Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Phase 4 de la charge de travail de la batterie de serveurs SharePoint intranet : Configuration de serveurs SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Au cours de la phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité AlwaysOn SQL Server dans les services d’infrastructure Azure, vous créez les couches Application et Web de la batterie de serveurs SharePoint et vous créez la batterie de serveurs à l’aide de l’Assistant Configuration SharePoint.

Vous devez procéder à cette opération avant de passer à la [Phase 5](virtual-machines-windows-ps-sp-intranet-ph5.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-windows-sp-intranet-overview.md) pour prendre connaissance de toutes les phases.

## Création de machines virtuelles de serveur SharePoint dans Azure

Quatre machines virtuelles de serveur SharePoint sont disponibles. Deux machines virtuelles de serveur SharePoint sont destinées aux serveurs Web frontaux et les deux autres sont réservées à l’administration et à l’hébergement d’applications SharePoint. Deux serveurs SharePoint sur chaque couche fournissent une haute disponibilité.

Tout d'abord, vous configurez l'équilibrage de charge interne de manière à ce qu'Azure répartisse le trafic client de manière équitable entre les deux serveurs Web frontaux. Pour cela, vous devez spécifier une instance d'équilibrage de charge interne comportant un nom et une adresse IP propre qui sont obtenus à partir de l'espace d'adressage du sous-réseau que vous avez affecté à votre réseau virtuel Azure.

> [AZURE.NOTE] Les jeux de commandes suivants définissent l'utilisation d'Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Spécifiez les valeurs des variables en supprimant les caractères < and >. Les jeux de commandes Azure PowerShell suivants utilisent les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table ST pour vos comptes de stockage ;
- Table A pour les groupes à haute disponibilité ;

Souvenez-vous que vous avez défini la Table M au cours de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-windows-ps-sp-intranet-ph2.md) et les Tables V, S, ST et A au cours de la [Phase 1 : Configuration d'Azure](virtual-machines-windows-ps-sp-intranet-ph1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Ajoutez ensuite un enregistrement d'adresse DNS à l'infrastructure DNS interne de votre organisation pour résoudre le nom de domaine complet de la batterie de serveurs SharePoint (par exemple, spfarm.corp.contoso.com) au niveau de l'adresse IP affectée à l'équilibrage de charge interne (valeur égale à $privIP dans le bloc de commandes Azure PowerShell précédent).

Le bloc de commandes PowerShell suivant permet de créer les machines virtuelles correspondant aux quatre serveurs SharePoint. Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Étant donné que ces machines virtuelles sont destinées à une application intranet, elles ne reçoivent pas d’adresse IP publique ou d’étiquette de nom de domaine DNS, et ne sont pas exposées à Internet. Toutefois, cela signifie également que vous ne pouvez pas vous y connecter à partir du portail Azure. Le bouton **Se connecter** n’est pas disponible quand vous affichez les propriétés de la machine virtuelle.

Utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance à chaque machine virtuelle. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Associez chaque machine virtuelle au domaine Active Directory approprié en utilisant les commandes suivantes dans l'invite de commandes Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Notez que vous devez fournir les informations d’identification de compte de domaine après avoir entré la commande **Add-Computer**.

Après le redémarrage, utilisez [Connexion à une machine virtuelle avec la connexion Bureau à distance](virtual-machines-windows-ps-sp-intranet-ph2.md#logon) quatre fois, une fois pour chaque serveur SharePoint, afin de vous connecter à l'aide des informations d'identification de compte [Domain]\\sp\_farm\_db. Vous avez créé ces informations d'identification au cours de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-windows-ps-sp-intranet-ph2.md).

Suivez la procédure décrite dans [Test de la connectivité](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn) quatre fois, soit une fois pour chaque serveur SharePoint, afin de tester la connectivité à différents emplacements du réseau de votre organisation.

> [AZURE.NOTE] Les serveurs SharePoint sont créés à partir de l’image de la version d’évaluation de SharePoint Server 2013. Vous devez convertir l’installation afin qu’elle utilise une clé de licence vendue au détail ou une clé de produit de licence en volume pour les éditions Standard ou Enterprise de SharePoint Server 2013.

## Configuration de la batterie de serveurs SharePoint

Suivez ces étapes pour configurer le premier serveur SharePoint dans la batterie de serveurs :

1.	À partir du Bureau du premier serveur d’applications SharePoint, double-cliquez sur **Assistant Configuration des produits SharePoint 2013**. Quand vous êtes invité à autoriser le programme à apporter des modifications à l'ordinateur, cliquez sur **Oui**.
2.	Dans la page **Bienvenue dans les produits SharePoint**, cliquez sur **Suivant**.
3.	Une boîte de dialogue **Assistant Configuration des produits SharePoint** s’affiche et indique que les services (tels qu’IIS) seront redémarrés ou réinitialisés. Cliquez sur **Oui**.
4.	Dans la page **Se connecter à une batterie de serveurs**, sélectionnez **Créer une batterie de serveurs**, puis cliquez sur **Suivant**.
5.	Dans la page **Spécifier les paramètres de la base de données de configuration** :
 - Dans **Serveur de base de données**, tapez le nom du serveur de base de données primaire.
 - Dans **Nom d'utilisateur**, tapez [Domain]**\\sp\_farm\_db** (créé pendant la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-windows-ps-sp-intranet-ph2.md)). N’oubliez pas que le compte sp\_farm\_db dispose de privilèges sysadmin sur le serveur de base de données.
 - Dans **Mot de passe**, saisissez le mot de passe associé au compte sp\_farm\_db.
6.	Cliquez sur **Next**.
7.	Dans la page **Spécifier les paramètres de sécurité de la batterie de serveurs**, tapez une phrase secrète deux fois. Consignez cette phrase secrète et conservez-la dans un emplacement sécurisé pour vous y reporter ultérieurement. Cliquez sur **Next**.
8.	Dans la page **Configurer l'application Web de l'Administration centrale de SharePoint**, cliquez sur **Suivant**.
9.	La page **Fin de l’Assistant Configuration des produits SharePoint** s’affiche. Cliquez sur **Next**.
10.	La page **Configuration des produits SharePoint** s'affiche. Attendez la fin du processus de configuration. Cette opération prend environ 8 minutes.
11.	Une fois la batterie de serveurs configurée, cliquez sur **Terminer**. Le nouveau site Web d’administration démarre.
12.	Pour configurer la batterie de serveurs SharePoint, cliquez sur **Démarrer l’Assistant**.

Effectuez la procédure suivante sur le deuxième serveur d’applications SharePoint et sur les deux serveurs Web frontaux :

1.	À partir du Bureau, double-cliquez sur **Assistant Configuration des produits SharePoint 2013**. Quand vous êtes invité à autoriser le programme à apporter des modifications à l'ordinateur, cliquez sur **Oui**.
2.	Dans la page **Bienvenue dans les produits SharePoint**, cliquez sur **Suivant**.
3.	Une boîte de dialogue **Assistant Configuration des produits SharePoint** s’affiche et indique que les services (tels qu’IIS) seront redémarrés ou réinitialisés. Cliquez sur **Oui**.
4.	Dans la page **Se connecter à une batterie de serveurs**, cliquez sur **Se connecter à une batterie de serveurs existante**, puis sur **Suivant**.
5.	Dans la page **Spécifier les paramètres de la base de données de configuration**, tapez le nom du serveur de base de données primaire dans **Serveur de base de données**, puis cliquez sur **Récupérer les noms de bases de données**.
6.	Cliquez sur **SharePoint\_Config** dans la liste de noms de bases de données, puis sur **Suivant**.
7.	Dans la page **Spécifier les paramètres de sécurité de la batterie de serveurs**, tapez la phrase secrète définie au cours de la procédure précédente. Cliquez sur **Next**.
8.	La page **Fin de l'Assistant Configuration des produits SharePoint** s'affiche. Cliquez sur **Next**.
9.	Sur la page **Configuration réussie**, cliquez sur **Terminer**.

Lorsque SharePoint crée la batterie de serveurs, il configure un ensemble de connexions de serveur sur la machine virtuelle SQL Server principale. SQL Server 2012 introduit le concept d’utilisateurs avec mots de passe pour les bases de données à relation contenant-contenu. La base de données elle-même stocke toutes les métadonnées de la base de données et les informations utilisateur. Les utilisateurs définis dans cette base de données n’ont pas besoin d’une connexion associée. Les informations contenues dans cette base de données sont répliquées par le groupe de disponibilité et sont disponibles après basculement. Pour plus d'informations, consultez [Bases de données à relation contenant-contenu](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Toutefois, les bases de données SharePoint ne sont pas des bases de données à relation contenant-contenu par défaut. Vous devez donc configurer manuellement le serveur de base de données secondaire afin qu’il dispose du même ensemble de connexions pour les comptes de la batterie de serveurs SharePoint que le serveur de base de données primaire. Vous pouvez effectuer cette synchronisation à partir de SQL Server Management Studio en vous connectant aux deux serveurs en même temps.

Après cette configuration initiale, d’autres options de configuration sont proposées pour les fonctionnalités de la batterie de serveurs SharePoint. Pour plus d'informations, consultez [Planification de SharePoint 2013 dans les services d'infrastructure Azure](http://msdn.microsoft.com/library/dn275958.aspx).

Voici la configuration résultant de la réussite de cette phase.

![](./media/virtual-machines-windows-ps-sp-intranet-ph4/workload-spsqlao_04.png)

## Étape suivante

- Pour poursuivre la configuration de cette charge de travail, utilisez la [Phase 5](virtual-machines-windows-ps-sp-intranet-ph5.md).

<!---HONumber=AcomDC_0323_2016-->