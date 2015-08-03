<properties
	pageTitle="Phase 3 de la charge de travail de la batterie de serveurs SharePoint intranet : Configuration de l’infrastructure SQL Server"
	description="Au cours de cette troisième phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité SQL Server AlwaysOn dans les services d’infrastructure Azure, vous créez les ordinateurs du cluster SQL Server et le cluster en lui-même."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows-sharepoint"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="josephd"/>

# Phase 3 de la charge de travail de la batterie de serveurs SharePoint intranet : Configuration de l’infrastructure SQL Server

Au cours de la phase de déploiement d’une batterie de serveurs SharePoint 2013 intranet uniquement avec les groupes de disponibilité SQL Server AlwaysOn dans les services d’infrastructure Azure, vous créez et configurez les deux ordinateurs SQL Server et l’ordinateur du nœud majoritaire du cluster dans la gestion des services, puis vous les combinez dans un cluster Windows Server.

Vous devez procéder à cette opération avant de passer à la [Phase 4](virtual-machines-workload-intranet-sharepoint-phase4.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

## Création de machines virtuelles de cluster SQL Server dans Azure

Deux machines virtuelles SQL Server sont disponibles. L’une des machines SQL Server contient le réplica de la base de données primaire d’un groupe de disponibilité. La seconde machine SQL Server contient le réplica de sauvegarde secondaire. La sauvegarde offre une haute disponibilité. Une machine virtuelle supplémentaire est dédiée au nœud majoritaire du cluster.

Le bloc de commandes PowerShell suivant permet de créer les machines virtuelles correspondant aux trois serveurs. Spécifiez les valeurs des variables en supprimant les caractères < and >. Ce jeu de commandes PowerShell utilise les valeurs des tables suivantes :

- table M pour les machines virtuelles ;
- table V pour les paramètres du réseau virtuel ;
- table S pour le sous-réseau ;
- table A pour les groupes à haute disponibilité ;
- table C pour les services cloud.

Souvenez-vous que vous avez défini la Table M au cours de la [Phase 2 : Configuration de contrôleurs de domaine](virtual-machines-workload-intranet-sharepoint-phase2.md), et les Tables V, S, A et C au cours de la [Phase 1 : Configuration d’Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l’invite de commandes Azure PowerShell.

	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 2 – Availability set name column>"

	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the first SQL Server computer."
	$cred2=Get-Credential –Message "Now type the name and password of an account that has permissions to add this virtual machine to the domain."
	$ADDomainName="<name of the AD domain that the server is joining (example CORP)>"
	$domainDNS="<FQDN of the AD domain that the server is joining (example corp.contoso.com)>"
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table 6 – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$serviceName="<Table C – Item 2 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second SQL server
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the second SQL Server computer."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for the cluster majority node server."
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $ADDomainName -DomainUserName $cred2.GetNetworkCredential().Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domainDNS

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configuration des machines SQL Server

Pour chaque machine SQL Server, procédez comme suit :

1. Consultez [Connexion à une machine virtuelle avec la connexion Bureau à distance](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) pour vous connecter à l’aide des informations d’identification du compte de l’administrateur local.

2. Suivez la procédure décrite dans [Initialisation d’un disque vide](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) deux fois, une fois pour chaque machine SQL Server, afin d’ajouter le disque de données supplémentaire.

3. Exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell.

		md f:\Data
		md f:\Log
		md f:\Backup

4. Suivez la procédure décrite dans [Test de la connectivité](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) afin de tester la connectivité à différents emplacements du réseau de votre organisation. Cette procédure garantit que la résolution de noms DNS fonctionne correctement (autrement dit, la machine virtuelle est configurée correctement avec les serveurs DNS sur le réseau virtuel) et que les paquets peuvent être transmis à destination et en provenance du réseau virtuel intersite.

Utilisez la procédure suivante à deux reprises, une fois pour chaque machine SQL Server, afin de configurer la machine SQL Server et d’utiliser le lecteur F: pour les nouvelles bases de données, mais aussi pour les comptes et les autorisations.


1.	Sur l’écran d’accueil, saisissez **SQL Studio**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.	Dans **Se connecter au serveur**, cliquez sur **Connecter**.
3.	Dans le volet gauche, cliquez avec le bouton droit sur le nœud supérieur, qui constitue l’instance par défaut nommée en fonction de la machine, puis cliquez sur **Propriétés**.
4.	Dans **Propriétés du serveur**, cliquez sur **Paramètres de base de données**.
5.	Dans **Emplacements de la base de données par défaut**, définissez les valeurs suivantes :
- Pour **Données**, définissez le chemin d’accès **f:\Data**.
- Pour **Journal**, définissez le chemin d’accès **f:\Log**.
- Pour **Sauvegarde**, définissez le chemin d’accès **f:\Backup**.
- Seules les nouvelles bases de données utilisent ces emplacements.
6.	Cliquez sur **OK** pour fermer la fenêtre.
7.	Dans le volet gauche, développez le **dossier Sécurité**.
8.	Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
9.	Dans **Nom de connexion**, entrez *domain*\sp_farm_db, où *domain* désigne le nom du domaine dans lequel le compte sp_farm_db a été créé.
10.	Sous **Sélectionner une page**, cliquez sur **Rôles de serveur**, puis sur **sysadmin** et sur **OK**.
11.	Fermez SQL Server 2014 Management Studio.

Utilisez la procédure suivante à deux reprises, une fois pour chaque machine SQL Server, afin d’autoriser les connexions Bureau à distance par le biais du compte sp_farm_db.

1.	Sur l’écran d’accueil, cliquez avec le bouton droit sur **Ce PC**, puis cliquez sur **Propriétés**.
2.	Dans la fenêtre **Système**, cliquez sur **Paramètres d’utilisation à distance**.
3.	Dans **Bureau à distance** cliquez sur **Sélectionner les utilisateurs**, puis sur **Ajouter**.
4.	Dans **Entrez les noms des objets à sélectionner**, entrez domain**\sp_farm_db**, puis cliquez sur **OK** trois fois.

SQL Server nécessite un port permettant d’accéder au serveur de base de données. Il requiert également des ports pour la connexion à SQL Server Management Studio et la gestion du groupe à haute disponibilité. Exécutez ensuite la commande ci-après à partir d’une invite de commandes Windows PowerShell au niveau administrateur à deux reprises (une fois pour chaque machine SQL Server) afin d’ajouter une règle de pare-feu autorisant le trafic entrant vers la machine SQL Server.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 4234, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Pour chacune des machines virtuelles SQL Server, déconnectez-vous en tant qu’administrateur local.

Pour plus d’informations sur l’optimisation des performances de SQL Server dans Azure, consultez [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](https://msdn.microsoft.com/library/azure/dn133149.aspx). Vous pouvez également désactiver le stockage géo-redondant (GRS) associé au compte de stockage de la batterie de serveurs SharePoint et utiliser des espaces de stockage pour optimiser les E/S.

## Configuration du serveur de nœud majoritaire du cluster

Consultez [Connexion à une machine virtuelle avec la connexion Bureau à distance](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) pour le nœud majoritaire du cluster afin de vous connecter à l’aide des informations d’identification du compte de domaine.

Sur le nœud majoritaire du cluster, suivez la procédure décrite dans [Test de la connectivité](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) afin de tester la connectivité à différents emplacements du réseau de votre organisation.

## Création du cluster Windows Server

Les groupes de disponibilité SQL Server AlwaysOn s’appuient sur la fonctionnalité de clustering de basculement Windows Server (WSFC) de Windows Server. Cette fonctionnalité permet à plusieurs ordinateurs de faire partie d’un groupe au sein d’un cluster. Si un ordinateur tombe en panne, un deuxième ordinateur est prêt à le remplacer. Il faut tout d’abord activer la fonctionnalité de clustering de basculement sur tous les ordinateurs concernés, ce qui inclue :

- la machine SQL Server principale ;
- la machine SQL Server secondaire ;
- le nœud majoritaire du cluster.

Le cluster de basculement nécessite au moins trois machines virtuelles. Deux machines hébergent SQL Server. La deuxième machine virtuelle SQL Server est un réplica secondaire synchrone qui évite toute perte de données en cas de défaillance de la machine principale. La troisième machine n’a pas besoin d’héberger SQL Server. Le nœud majoritaire du cluster fonctionne comme un témoin du quorum dans le WSFC. Étant donné que le cluster WSFC s’appuie sur un quorum pour surveiller l’intégrité des données, une majorité doit toujours garantir la présence en ligne du cluster WSFC. Si seules deux machines se trouvent dans le cluster, et si l’une d’entre elles tombe en panne, il ne peut y avoir aucune majorité, car seule l’une des deux machines présente une défaillance. Pour plus d’informations, consultez [Modes de quorum WSFC et configuration de vote (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Pour les deux ordinateurs SQL Server et le nœud majoritaire du cluster, exécutez la commande suivante à partir de l’invite de commandes Windows PowerShell au niveau administrateur.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

En raison du comportement actuel de DHCP dans Azure, qui n’est pas conforme au document RFC, il est possible que la création d’un cluster WSFC échoue. Pour plus d’informations, recherchez « Comportement du cluster WSFC sur le réseau Azure » sur la page Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure. Une solution de contournement existe malgré tout. Pour créer le cluster, procédez comme suit :

1.	Connectez-vous à la machine virtuelle SQL Server principale en vous servant du compte **sp_install**.
2.	Sur l’écran d’accueil, entrez **Basculement**, puis cliquez sur **Gestionnaire du cluster de basculement**.
3.	Dans le volet gauche, cliquez sur **Gestionnaire du cluster de basculement**, puis cliquez sur **Créer le cluster**.
4.	Sur la page Avant de commencer, cliquez sur **Suivant**.
5.	Sur la page Sélectionner les serveurs, entrez le nom de la machine SQL Server principale, cliquez sur **Ajouter**, puis cliquez sur **Suivant**.
6.	Sur la page Avertissement de validation, cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster.** Cliquez sur **Suivant**.
7.	Sur la page Point d’accès pour l’administration du cluster, entrez le nom de votre cluster dans la zone de texte **Nom du cluster**, puis cliquez sur **Suivant**.
8.	Sur la page Confirmation, cliquez sur **Suivant** afin de lancer la création du cluster.
9.	Sur la page Résumé, cliquez sur **Terminer**.
10.	Dans le volet gauche, cliquez sur votre nouveau cluster. Dans la section **Principales ressources de cluster** du volet de contenu, ouvrez votre cluster de serveurs. La ressource **Adresse IP** indique l’état **Échec**. La ressource d’adresse IP ne peut pas être mise en ligne, car le cluster a la même adresse IP que la machine elle-même. L’adresse apparaît donc en double.
11.	Cliquez avec le bouton droit sur la ressource **Adresse IP** en échec, puis cliquez sur **Propriétés**.
12.	Dans la boîte de dialogue **Propriétés des adresses IP**, cliquez sur **Adresse IP statique**.
13.	Entrez une adresse IP inutilisée dans la plage d’adresses correspondant au sous-réseau sur lequel se trouve la machine SQL Server, puis cliquez sur **OK**.
14.	Cliquez avec le bouton droit sur la ressource Adresse IP, puis cliquez sur **Mettre en ligne**. Attendez que les deux ressources soient en ligne. Lorsque la ressource du cluster apparaît en ligne, elle met à jour le contrôleur de domaine avec un nouveau compte d’ordinateur Active Directory (AD). Ce compte AD permettra d’exécuter le service en cluster du groupe de disponibilité.
15.	Maintenant que le compte AD existe, mettez le cluster hors connexion. Cliquez avec le bouton droit sur le nom du cluster dans **Principales ressources de cluster**, puis cliquez sur **Mettre hors connexion**.
16.	Pour supprimer l’adresse IP du cluster, cliquez avec le bouton droit sur **Adresse IP**, cliquez sur **Supprimer**, puis cliquez sur **Oui** lorsque vous y êtes invité. La ressource de cluster ne peut plus être mise en ligne, car elle dépend de la ressource d’adresse IP. Toutefois, le bon fonctionnement d’un groupe de disponibilité ne dépend pas du nom du cluster ni de l’adresse IP. Par conséquent, le nom du cluster peut rester hors connexion.
17.	Pour ajouter les nœuds restants au cluster, cliquez avec le bouton droit sur le nom du cluster dans le volet gauche, puis cliquez sur **Ajouter un nœud**.
18.	Sur la page Avant de commencer, cliquez sur **Suivant**.
19.	Sur la page Sélectionner les serveurs, entrez le nom, puis cliquez sur **Ajouter** pour ajouter la machine SQL Server secondaire et le nœud majoritaire du cluster au cluster. Après avoir ajouté les deux ordinateurs, cliquez sur **Suivant**. Si une machine ne peut pas être ajoutée, et si un message d’erreur indique « Le service d’accès distant au Registre n’est pas en cours d’exécution », procédez comme suit : Connectez-vous à la machine, ouvrez le composant logiciel enfichable Services (services.msc) et activez le service d’accès distant au Registre. Pour plus d’informations, consultez [Impossible de se connecter au service Registre distant](http://technet.microsoft.com/library/bb266998.aspx).
20.	Sur la page Avertissement de validation, cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster.** Cliquez sur **Suivant**.
21.	Sur la page Confirmation, cliquez sur **Suivant**.
22.	Sur la page Résumé, cliquez sur **Terminer**.
23.	Dans le volet gauche, cliquez sur **Nœuds**. Les trois ordinateurs doivent s’afficher.

## Activation des groupes à haute disponibilité AlwaysOn

L’étape suivante consiste à activer les groupes de disponibilité AlwaysOn à l’aide du Gestionnaire de configuration SQL Server. Les groupes de disponibilité de SQL Server ne sont pas les mêmes que ceux d’Azure. Un groupe de disponibilité comporte des bases de données hautement disponibles et récupérables. Un groupe à haute disponibilité Azure alloue des machines virtuelles à différents domaines d’erreur. Pour plus d’informations sur les domaines d’erreur, consultez [Gestion de la disponibilité des machines virtuelles](virtual-machines-manage-availability.md).

Suivez cette procédure pour activer les groupes de disponibilité AlwaysOn sur SQL Server.

1.	Connectez-vous à la machine SQL Server principale par le biais du compte **sp_farm_db** ou d’un autre compte disposant d’un rôle de serveur sysadmin sur la machine SQL Server.
2.	Sur l’écran d’accueil, entrez **Configuration de SQL Server**, puis cliquez sur **Gestionnaire de configuration SQL Server**.
3.	Dans le volet gauche, cliquez sur **Services SQL Server**.
4.	Dans le volet de contenu, double-cliquez sur **SQL Server (MSSQLSERVER)**.
5.	Dans **Propriétés de SQL Server (MSSQLSERVER)**, cliquez sur l’onglet **Haute disponibilité AlwaysOn**, sélectionnez **Activer les groupes de disponibilité AlwaysOn**, cliquez sur **Appliquer**, puis cliquez sur **OK** lorsque vous y êtes invité. Ne fermez pas encore la fenêtre Propriétés.
6.	Cliquez sur l’onglet virtual-machines-manage-availability, puis entrez Domain**\sqlservice** dans **Nom du compte**. Saisissez le mot de passe du compte sqlservice dans **Mot de passe** et **Confirmer le mot de passe**, puis cliquez sur **OK**.
7.	Dans la fenêtre du message, cliquez sur **Oui** pour redémarrer le service SQL Server.
8.	Connectez-vous à la machine SQL Server secondaire et répétez la procédure.

Le schéma suivant illustre la configuration résultant de la réussite de cette phase, avec les noms d’ordinateur d’espaces réservés.

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## Étape suivante

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 4 : Configuration de serveurs SharePoint](virtual-machines-workload-intranet-sharepoint-phase4.md).

## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

<!---HONumber=July15_HO4-->