<properties 
	pageTitle="Phase 4 de la charge de travail de la batterie de serveurs SharePoint intranet : Configuration de serveurs SharePoint" 
	description="Au cours de cette quatrième phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité SQL Server AlwaysOn dans les services d’infrastructure Azure, vous créez les machines virtuelles du serveur SharePoint ainsi qu’une batterie de serveurs SharePoint." 
	documentationCenter=""
	services="virtual-machines" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="josephd"/>

# Phase 4 de la charge de travail de la batterie de serveurs SharePoint intranet : Configuration de serveurs SharePoint

Au cours de la phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité AlwaysOn SQL Server dans les services d’infrastructure Azure, vous créez les couches Application et Web de la batterie de serveurs SharePoint et vous créez la batterie de serveurs avec l’Assistant Configuration SharePoint.

Vous devez procéder à cette opération avant de passer à la [Phase 5](virtual-machines-workload-intranet-sharepoint-phase5.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

## Création de machines virtuelles de serveur SharePoint dans Azure

Quatre machines virtuelles de serveur SharePoint sont disponibles. Deux machines virtuelles SharePoint Server sont destinées aux serveurs Web frontaux et les deux autres sont réservées à l’administration et à l’hébergement d’applications SharePoint. Deux serveurs SharePoint sur chaque couche fournissent une haute disponibilité.

Le bloc de commandes PowerShell suivant permet de créer les machines virtuelles correspondant aux quatre serveurs SharePoint. Spécifiez les valeurs des variables en supprimant les caractères < and >. Ce jeu de commandes PowerShell utilise les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table A pour les groupes à haute disponibilité ;
- Table C pour les services cloud.

Souvenez-vous que vous avez défini la Table M au cours de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md), et les Tables V, S, A et C au cours de la [Phase 1 : Configuration d’Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l'invite de commandes Azure PowerShell.

	# Create the first SharePoint application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 3 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint application server."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint application server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the first SharePoint web server
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 4 – Availability set name column>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName
	
	# Create the second SharePoint web server
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet
	
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SharePoint web server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS
	
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName
	
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

Consultez [Connexion à une machine virtuelle avec la connexion Bureau à distance](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) quatre fois, une fois pour chaque serveur SharePoint, afin de vous connecter à l’aide des informations d’identification Domain\\sp_farm_db, créées au cours de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md).

Suivez la procédure décrite dans [Test de la connectivité](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) quatre fois, soit une fois pour chaque serveur SharePoint, afin de tester la connectivité à différents emplacements du réseau de votre organisation.

## Configuration de la batterie de serveurs SharePoint

Suivez ces étapes pour configurer le premier serveur SharePoint dans la batterie de serveurs.

1.	À partir du Bureau du premier serveur d’applications SharePoint, double-cliquez sur **Assistant Configuration des produits SharePoint 2013**. Lorsque vous êtes invité à autoriser le programme à apporter des modifications à l’ordinateur, cliquez sur **Oui**.
2.	Sur la page d’accueil des produits SharePoint, cliquez sur **Suivant**.
3.	Une boîte de dialogue **Assistant Configuration des produits SharePoint** s’affiche et indique que les services (tels qu’IIS) seront redémarrés ou réinitialisés. Cliquez sur **Oui**.
4.	Sur la page Se connecter à une batterie de serveurs, sélectionnez sur **Créer une batterie de serveurs**, puis cliquez sur **Suivant**.
5.	Sur la page Spécifier les paramètres de la base de données de configuration :
- Dans **Serveur de base de données**, entrez le nom de la machine SQL Server principale. 
- Dans **Nom d’utilisateur**, entrez Domain**\\sp_farm_db** (créé lors de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md)). N’oubliez pas que le compte sp_farm_db dispose de privilèges sysadmin sur la machine SQL Server. 
- Dans **Mot de passe**, saisissez le mot de passe associé au compte sp_farm_db.
6.	Cliquez sur **Next**.
7.	Sur la page Spécifier les paramètres de sécurité de la batterie de serveurs, entrez votre phrase secrète deux fois. Consignez cette phrase secrète et conservez-la dans un emplacement sécurisé pour vous y reporter ultérieurement. Cliquez sur **Next**.
8.	Sur la page Configurer l’application Web de l’Administration centrale de SharePoint, cliquez sur **Suivant**.
9.	La page Fin de l’Assistant Configuration des produits SharePoint s’affiche. Cliquez sur **Next**.
10.	La page Configuration des produits SharePoint s’affiche. Attendez la fin du processus de configuration. Cette opération prend environ 8 minutes.
11.	Une fois la batterie de serveurs configurée, cliquez sur **Terminer**. Le nouveau site Web d’administration démarre.
12.	Pour configurer la batterie de serveurs SharePoint, cliquez sur **Démarrer l’Assistant**.

Effectuez la procédure suivante sur le deuxième serveur d’applications SharePoint et sur les deux serveurs Web frontaux.

1.	À partir du Bureau, double-cliquez sur **Assistant Configuration des produits SharePoint 2013**. Lorsque vous êtes invité à autoriser le programme à apporter des modifications à l’ordinateur, cliquez sur **Oui**.
2.	Sur la page d’accueil des produits SharePoint, cliquez sur **Suivant**.
3.	Une boîte de dialogue Assistant Configuration des produits SharePoint s’affiche et indique que les services (tels qu’IIS) seront redémarrés ou réinitialisés. Cliquez sur **Oui**.
4.	Sur la page Se connecter à une batterie de serveurs, cliquez sur **Se connecter à une batterie de serveurs existante**, puis sur **Suivant**.
5.	Sur la page Spécifier les paramètres de la base de données de configuration, entrez le nom de la machine SQL Server principale dans **Serveur de base de données**, puis cliquez sur **Récupérer les noms de bases de données**. 
6.	Cliquez sur **SharePoint_Config** dans la liste de noms de bases de données, puis cliquez sur **Suivant**. 
7.	Sur la page Spécifier les paramètres de sécurité de la batterie de serveurs, entrez la phrase secrète définie au cours de la procédure précédente. Cliquez sur **Next**.
8.	La page Fin de l’Assistant Configuration des produits SharePoint s’affiche. Cliquez sur **Next**.
9.	Sur la page Configuration réussie, cliquez sur **Terminer**. 

Lorsque SharePoint crée la batterie de serveurs, il configure un ensemble de connexions de serveur sur la machine virtuelle SQL Server principale. SQL Server 2012 introduit le concept d’utilisateurs avec mots de passe pour les bases de données à relation contenant-contenu. La base de données elle-même stocke toutes les métadonnées de la base de données et les informations utilisateur. Les utilisateurs définis dans cette base de données n’ont pas besoin d’une connexion associée. Les informations contenues dans cette base de données sont répliquées par le groupe de disponibilité et sont disponibles après basculement. Pour plus d’informations, consultez [Bases de données à relation contenant-contenu](http://go.microsoft.com/fwlink/p/?LinkId=262794).

Toutefois, les bases de données SharePoint ne sont pas des bases de données à relation contenant-contenu par défaut. Vous devez donc configurer manuellement la machine SQL Server secondaire afin qu’elle dispose du même ensemble de connexions pour les comptes de la batterie de serveurs SharePoint en tant que machine SQL Server principale. Vous pouvez effectuer cette synchronisation à partir de SQL Server Management Studio en vous connectant aux deux serveurs en même temps.

Après cette installation initiale, d’autres options de configuration sont proposées pour les fonctionnalités de la batterie de serveurs SharePoint. Pour plus d’informations, consultez [Planification de SharePoint 2013 dans les services d’infrastructure Azure](http://msdn.microsoft.com/library/dn275958.aspx).

## Configuration de l’équilibrage de charge interne

Vous configurez l’équilibrage de charge interne de sorte que le trafic client à destination de la batterie de serveurs SharePoint soit distribué uniformément sur les deux serveurs Web frontaux. Pour cela, vous devez spécifier une instance d’équilibrage de charge interne comportant un nom et une adresse IP propre qui sont affectés à partir de l’espace d’adressage du sous-réseau. Pour déterminer si l’adresse IP que vous avez choisie peut servir à l’équilibrage de charge interne, utilisez les commandes Azure PowerShell suivantes :

	$vnet="<Table V – Item 1 – Value column>"
	$testIP="<a chosen IP address from the subnet address space, Table S - Item 1 – Subnet address space column>"
	Test-AzureStaticVNetIP –VNetName $vnet –IPAddress $testIP

Si le champ **IsAvailable** de la commande **Test-AzureStaticVNetIP** est défini sur **True**, vous pouvez utiliser l’adresse IP.

Exécutez le jeu de commandes suivant à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local :

	$serviceName="<Table C – Item 3 – Cloud service name column>"
	$ilb="<name of your internal load balancer instance>"
	$subnet="<Table S – Item 1 – Subnet name column>"
	$IP="<an available IP address for your ILB instance>"
	Add-AzureInternalLoadBalancer –ServiceName $serviceName -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
	
	$prot="tcp"
	$locport=80
	$pubport=80
	# This example assumes unsecured HTTP traffic to the SharePoint farm.
	
	$epname="SPWeb1"
	$vmname="<Table M – Item 8 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
	
	$epname="SPWeb2"
	$vmname="<Table M – Item 9 – Virtual machine name column>"
	Get-AzureVM –ServiceName $serviceName –Name $vmname | Add-AzureEndpoint -Name $epname -LBSetName $ilb -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Ajoutez ensuite un enregistrement d’adresse DNS à l’infrastructure DNS de votre organisation pour résoudre le nom de domaine complet de la batterie de serveurs SharePoint (par exemple, sp.corp.contoso.com) au niveau de l’adresse IP affectée à l’instance d’équilibrage de charge interne (valeur égale à **$IP** dans le bloc de commandes Azure PowerShell précédent).

Cela illustre la configuration résultant de la réussite de cette phase.

![](./media/virtual-machines-workload-intranet-sharepoint-phase4/workload-spsqlao_04.png)

## Étape suivante

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 5 : Création du groupe de disponibilité et ajout des bases de données SharePoint](virtual-machines-workload-intranet-sharepoint-phase5.md).

## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=July15_HO2-->