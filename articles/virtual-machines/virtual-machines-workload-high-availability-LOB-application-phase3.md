<properties 
	pageTitle="Application métier - Phase 3 | Microsoft Azure" 
	description="Créez les ordinateurs et le cluster SQL Server, et activez les groupes de disponibilité dans la phase 3 de l'application métier dans Azure." 
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

# Charge de travail des applications métier, phase 3 : configurer l'infrastructure SQL Server

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique


Au cours de la phase de déploiement d’une application métier à haute disponibilité dans des services d’infrastructure Azure, vous configurez les deux ordinateurs exécutant SQL Server et l’ordinateur du nœud majoritaire du cluster, puis vous les combinez dans un cluster Windows Server.

Vous devez procéder à cette opération avant de passer à la [Phase 4](virtual-machines-workload-high-availability-LOB-application-phase4.md). Pour découvrir toutes les phases, voir [Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md).

> [AZURE.NOTE]Cet article contient des commandes pour la version préliminaire Azure PowerShell 1.0. Pour exécuter ces commandes dans Azure PowerShell 0.9.8 et versions antérieures, remplacez toutes les instances d’« -AzureRM » par « -Azure » et ajoutez la commande **Switch-AzureMode AzureResourceManager** avant d’exécuter toute commande. Pour plus d’informations, consultez [Version préliminaire Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/).

## Création de machines virtuelles de cluster SQL Server dans Azure

Il existe deux machines virtuelles exécutant SQL Server Une machine virtuelle contient le réplica de la base de données primaire d'un groupe de disponibilité. L'autre contient le réplica secondaire (sauvegarde). La sauvegarde permet de prendre en charge la haute disponibilité. Une machine virtuelle supplémentaire est dédiée au nœud majoritaire du cluster.

Le bloc de commandes PowerShell suivant permet de créer les machines virtuelles correspondant aux trois serveurs. Spécifiez les valeurs des variables en supprimant les caractères < and >. Ce jeu de commandes PowerShell utilise les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table ST pour vos comptes de stockage ;
- Table A pour les groupes à haute disponibilité ;

Souvenez-vous que vous avez défini la table M au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md) et les tables V, S, ST et A au cours de la [Phase 1](virtual-machines-workload-high-availability-LOB-application-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant dans l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<your resource group name>"
	$locName="<Azure location of your resource group>"
	# Change to the premium storage account
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 2 – Availability set name column>"
	
	# Create the first SQL server
	$vmName="<Table M – Item 3 - Virtual machine name column>"
	$vmSize="<Table M – Item 3 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second SQL Server virtual machine
	$vmName="<Table M – Item 4 - Virtual machine name column>"
	$vmSize="<Table M – Item 4 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for SQL data in GB>
	$diskLabel="<the label on the disk>"
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Change to the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"
	
	# Create the cluster majority node server
	$vmName="<Table M – Item 5 - Virtual machine name column>"
	$vmSize="<Table M – Item 5 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Étant donné que ces machines virtuelles sont destinées à une application intranet, elles ne reçoivent pas d’adresse IP publique ou d’étiquette de nom de domaine DNS, et ne sont pas exposées à Internet. Toutefois, cela signifie également que vous ne pouvez pas vous y connecter à partir du portail Azure en version préliminaire. Le bouton **Se connecter** n’est pas disponible quand vous affichez les propriétés de la machine virtuelle. Utilisez l’accessoire Connexion Bureau à distance ou un autre outil Bureau à distance pour vous connecter à la machine virtuelle à l’aide de son adresse IP privée ou de son nom DNS intranet.

## Configurer les ordinateurs exécutant SQL Server

Pour chaque machine virtuelle exécutant SQL Server, utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Associez chaque machine virtuelle exécutant SQL Server au domaine AD DS approprié en utilisant les commandes suivantes dans l'invite de commandes Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Notez que vous devez fournir les informations d'identification de compte de domaine après avoir saisi la commande Add-Computer.

Après le redémarrage, connectez-vous de nouveau à l'aide d'un compte disposant de privilèges d'administrateur local.


Utilisez la procédure suivante à deux reprises, une fois pour chaque machine virtuelle qui exécute SQL Server, pour ajouter le disque de données supplémentaire et créer des dossiers sur le nouveau volume.

### Initialisation d'un disque vide et ajout de dossiers

1. Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2. Dans le volet de contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3. Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4. Dans la page Avant de commencer de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5. Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l’invite, cliquez sur **OK**.
6. Dans la page Spécifier la taille du volume, cliquez sur **Suivant**.
7. À la page Affecter à la lettre d'un lecteur ou à un dossier, cliquez sur **Suivant**.
8. À la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9. À la page Confirmer les sélections, cliquez sur **Créer**.
10. Lorsque vous avez terminé, cliquez sur **Fermer**.
11. Exécutez les commandes suivantes à partir d'une invite de commandes PowerShell Windows :
	- md f:\\Data
	- md f:\\Log
	- md f:\\Backup

Utilisez la procédure suivante à deux reprises, une fois pour chaque machine virtuelle exécutant SQL Server, afin de les configurer de manière à ce qu'elles utilisent le lecteur F: pour les nouvelles bases de données, mais également pour les comptes et les autorisations.

1. Sur l’écran d’accueil, saisissez **SQL Studio**, puis cliquez sur **SQL Server 2014 Management Studio**.
2. Dans **Se connecter au serveur**, cliquez sur **Connecter**.
3. Dans le volet gauche, cliquez avec le bouton droit sur le nœud supérieur, qui constitue l’instance par défaut nommée en fonction de la machine, puis cliquez sur **Propriétés**.
4.	Dans **Propriétés du serveur**, cliquez sur **Paramètres de base de données**.
5.	Dans **Emplacements de la base de données par défaut**, définissez les valeurs suivantes : 
	- Pour **Données**, définissez le chemin d’accès **f:\\Data**.
	- Pour **Journal**, définissez le chemin d’accès **f:\\Log**.
	- Pour **Sauvegarde**, définissez le chemin d’accès **f:\\Backup**.
	- Seules les nouvelles bases de données utilisent ces emplacements.
6.	Cliquez sur **OK** pour fermer la fenêtre.
7.	Dans le volet gauche, développez le **dossier Sécurité**.
8.	Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
9.	Dans **Nom de connexion**, tapez *domain*\\sqladmin (où *domain* désigne le nom du domaine dans lequel le compte sqladmin a été créé au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md)). 
10.	Sous **Sélectionner une page**, cliquez sur **Rôles de serveur**, puis sur **sysadmin** et sur **OK**.
11.	Fermez SQL Server 2014 Management Studio.

Utilisez la procédure suivante à deux reprises, une fois pour chaque serveur SQL, afin d'autoriser les connexions Bureau à distance par le biais du compte sqladmin.

1.	Sur l’écran d’accueil, cliquez avec le bouton droit sur **Ce PC**, puis cliquez sur **Propriétés**.
2.	Dans la fenêtre **Système**, cliquez sur **Paramètres d’utilisation à distance**.
3.	Dans **Bureau à distance** cliquez sur **Sélectionner les utilisateurs**, puis sur **Ajouter**.
4.	Dans **Entrez les noms des objets à sélectionner**, tapez [domain]**\\sqladmin**, puis cliquez trois fois sur **OK**.

Le service SQL Server requiert un port permettant aux clients d'accéder au serveur de base de données. Il requiert également des ports pour la connexion à SQL Server Management Studio et la gestion du groupe à haute disponibilité. Exécutez ensuite deux fois la commande suivante à partir d'une invite de commandes Windows PowerShell de niveau administrateur (une fois pour chaque machine virtuelle SQL Server) afin d'ajouter une règle de pare-feu autorisant ce type de trafic entrant.

	New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

Pour chacune des machines virtuelles SQL Server, fermez votre session en tant qu'administrateur local.

Pour plus d’informations sur l’optimisation des performances de SQL Server dans Azure, consultez [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-sql-server-performance-best-practices.md). Vous pouvez également désactiver le stockage géo-redondant (GRS) associé au compte de stockage d'applications métier et utiliser des espaces de stockage afin d'optimiser les E/S.

## Configuration du serveur de nœud majoritaire du cluster

Utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance à la machine virtuelle du serveur de nœud majoritaire du cluster. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Associez le serveur de nœud majoritaire du cluster au domaine AD DS approprié en utilisant les commandes suivantes dans l'invite de commandes Windows PowerShell.

	$domName="<AD DS domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Notez que vous devez fournir les informations d’identification de compte de domaine au moment de l’exécution de la commande **Add-Computer**.

Après le redémarrage, connectez-vous de nouveau à l'aide d'un compte disposant de privilèges d'administrateur local.

## Créer le clustering de basculement Windows Server

Les groupes de disponibilité SQL Server AlwaysOn s’appuient sur la fonctionnalité de clustering de basculement Windows Server (WSFC) de Windows Server. Cette fonctionnalité permet à plusieurs ordinateurs de faire partie d’un groupe au sein d’un cluster. Si un ordinateur tombe en panne, un deuxième ordinateur est prêt à le remplacer. Il faut tout d’abord activer la fonctionnalité de clustering de basculement sur tous les ordinateurs concernés, ce qui inclue :

- La machine virtuelle principale exécutant SQL Server
- La machine virtuelle secondaire exécutant SQL Server
- le nœud majoritaire du cluster.

Le cluster de basculement nécessite au moins trois machines virtuelles. Deux machines hébergeant SQL Server où la machine virtuelle secondaire est un réplica secondaire synchrone afin d'éviter toute perte de données en cas de défaillance de la machine principale. La troisième machine virtuelle n'a pas besoin d'héberger SQL Server. Les fonctions du nœud majoritaire du cluster fournissent un quorum dans le clustering de basculement Windows Server (WSFC). Étant donné que le cluster WSFC s’appuie sur un quorum pour surveiller l’intégrité des données, une majorité doit toujours garantir la présence en ligne du cluster WSFC. Si seules deux machines se trouvent dans le cluster, et si l’une d’entre elles tombe en panne, il ne peut y avoir aucune majorité, car seule l’une des deux machines présente une défaillance. Pour plus d’informations, consultez [Modes de quorum WSFC et configuration de vote (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx).

Pour les deux machines virtuelles SQL Server et le nœud majoritaire du cluster, exécutez la commande suivante à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	Install-WindowsFeature Failover-Clustering -IncludeManagementTools

En raison du comportement actuel de DHCP dans Azure, qui n’est pas conforme au document RFC, il est possible que la création d’un cluster WSFC échoue. Pour plus d’informations, recherchez « Comportement du cluster WSFC sur le réseau Azure » sur la page Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure. Une solution de contournement existe malgré tout. Pour créer le cluster, procédez comme suit :

1.	Connectez-vous à la machine virtuelle SQL Server principale avec le compte sqladmin créé au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).
2.	Sur l’écran d’accueil, entrez **Basculement**, puis cliquez sur **Gestionnaire du cluster de basculement**.
3.	Dans le volet gauche, cliquez sur **Gestionnaire du cluster de basculement**, puis cliquez sur **Créer le cluster**.
4.	Dans la page **Avant de commencer**, cliquez sur **Suivant**.
5.	Dans la page **Sélectionner les serveurs**, tapez le nom de la machine SQL Server principale, cliquez sur **Ajouter**, puis sur **Suivant**.
6.	Dans la page **Avertissement de validation**, cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster.**. Cliquez sur **Suivant**.
7.	Dans la page **Point d’accès pour l’administration du cluster**, tapez le nom de votre cluster dans la zone de texte **Nom du cluster**, puis cliquez sur **Suivant**.
8.	Dans la page **Confirmation**, cliquez sur **Suivant** pour lancer la création du cluster. 
9.	Dans la page **Résumé**, cliquez sur **Terminer**.
10.	Dans le volet gauche, cliquez sur votre nouveau cluster. Dans la section **Principales ressources de cluster** du volet de contenu, ouvrez votre cluster de serveurs. La ressource **Adresse IP** indique l’état **Échec**. La ressource d’adresse IP ne peut pas être mise en ligne, car le cluster a la même adresse IP que la machine elle-même. L’adresse apparaît donc en double. 
11.	Cliquez avec le bouton droit sur la ressource **Adresse IP** en échec, puis cliquez sur **Propriétés**.
12.	Dans la boîte de dialogue **Propriétés des adresses IP**, cliquez sur **Adresse IP statique**.
13.	Entrez une adresse IP inutilisée dans la plage d’adresses correspondant au sous-réseau sur lequel se trouve la machine SQL Server, puis cliquez sur **OK**.
14.	Cliquez avec le bouton droit sur la ressource Adresse IP, puis cliquez sur **Mettre en ligne**. Attendez que les deux ressources soient en ligne. Lorsque la ressource du cluster apparaît en ligne, elle met à jour le contrôleur de domaine avec un nouveau compte d’ordinateur Active Directory (AD). Ce compte AD permettra d’exécuter le service en cluster du groupe de disponibilité.
15.	Maintenant que le compte AD existe, mettez le cluster hors connexion. Cliquez avec le bouton droit sur le nom du cluster dans **Principales ressources de cluster**, puis cliquez sur **Mettre hors connexion**.
16.	Pour supprimer l’adresse IP du cluster, cliquez avec le bouton droit sur **Adresse IP**, cliquez sur **Supprimer**, puis cliquez sur **Oui** lorsque vous y êtes invité. La ressource de cluster ne peut plus être mise en ligne, car elle dépend de la ressource d’adresse IP. Toutefois, le bon fonctionnement d’un groupe de disponibilité ne dépend pas du nom du cluster ni de l’adresse IP. Par conséquent, le nom du cluster peut rester hors connexion.
17.	Pour ajouter les nœuds restants au cluster, cliquez avec le bouton droit sur le nom de votre cluster dans le volet gauche, puis cliquez sur **Ajouter un nœud**.
18.	Dans la page **Avant de commencer**, cliquez sur **Suivant**. 
19.	Dans la page **Sélectionner les serveurs**, tapez le nom, puis cliquez sur **Ajouter** pour ajouter à la fois la machine SQL Server secondaire et le nœud majoritaire du cluster au cluster. Après avoir ajouté les deux ordinateurs, cliquez sur **Suivant**. Si une machine ne peut pas être ajoutée, et si un message d’erreur indique « Le service d’accès distant au Registre n’est pas en cours d’exécution », procédez comme suit : Connectez-vous à la machine, ouvrez le composant logiciel enfichable Services (services.msc) et activez le service d’accès distant au Registre. Pour plus d’informations, consultez [Impossible de se connecter au service Registre distant](http://technet.microsoft.com/library/bb266998.aspx). 
20.	Dans la page **Avertissement de validation**, cliquez sur **Non. Je n’ai pas besoin de l’assistance de Microsoft pour ce cluster, et par conséquent, je ne souhaite pas exécuter les tests de validation. Lorsque je clique sur Suivant, poursuivre la création du cluster.**. Cliquez sur **Suivant**. 
21.	Dans la page **Confirmation**, cliquez sur **Suivant**.
22.	Dans la page **Résumé**, cliquez sur **Terminer**.
23.	Dans le volet gauche, cliquez sur **Nœuds**. Les trois ordinateurs doivent s’afficher.

## Activation des groupes à haute disponibilité AlwaysOn

L’étape suivante consiste à activer les groupes de disponibilité AlwaysOn à l’aide du Gestionnaire de configuration SQL Server. Les groupes de disponibilité de SQL Server ne sont pas les mêmes que ceux d’Azure. Un groupe de disponibilité comporte des bases de données hautement disponibles et récupérables. Un groupe à haute disponibilité Azure alloue des machines virtuelles à différents domaines d’erreur. Pour plus d’informations sur les domaines d’erreur, consultez [Gestion de la disponibilité des machines virtuelles](virtual-machines-manage-availability.md).

Suivez cette procédure pour activer les groupes de disponibilité AlwaysOn sur SQL Server.

1.	Connectez-vous à la machine virtuelle SQL Server principale avec le compte sqladmin créé au cours de la [Phase 2](virtual-machines-workload-high-availability-LOB-application-phase2.md).
2.	Sur l’écran d’accueil, entrez **Configuration de SQL Server**, puis cliquez sur **Gestionnaire de configuration SQL Server**.
3.	Dans le volet gauche, cliquez sur **Services SQL Server**.
4.	Dans le volet de contenu, double-cliquez sur **SQL Server (MSSQLSERVER)**.
5.	Dans **Propriétés de SQL Server (MSSQLSERVER)**, cliquez sur l’onglet **Haute disponibilité AlwaysOn**, sélectionnez **Activer les groupes de disponibilité AlwaysOn**, cliquez sur **Appliquer**, puis cliquez sur **OK** lorsque vous y êtes invité. Ne fermez pas encore la fenêtre Propriétés. 
6.	Cliquez sur l’onglet virtual-machines-manage-availability, puis tapez [Domain]**\\sqlservice** dans **Nom du compte**. Tapez le mot de passe du compte sqlservice dans **Mot de passe** et **Confirmer le mot de passe**, puis cliquez sur **OK**.
7.	Dans la fenêtre du message, cliquez sur **Oui** pour redémarrer le service SQL Server.
8.	Connectez-vous à la machine virtuelle SQL Server secondaire avec le compte sqladmin créé au cours des étapes 2 à 7. 

Ce schéma illustre la configuration résultant de la réussite de cette phase, avec les noms d’ordinateur d’espaces réservés.

![](./media/virtual-machines-workload-high-availability-LOB-application-phase3/workload-lobapp-phase3.png)

## Étape suivante

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 4 : Configurer les serveurs web](virtual-machines-workload-high-availability-LOB-application-phase4.md).

## Ressources supplémentaires

[Déployer une application métier à haute disponibilité dans Azure](virtual-machines-workload-high-availability-LOB-application-overview.md)

[Plan de l’architecture des applications métier](http://msdn.microsoft.com/dn630664)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](../virtual-network/virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Charge de travail des services d’infrastructure Azure : batterie de serveurs SharePoint Server 2013](virtual-machines-workload-intranet-sharepoint-farm.md)

<!---HONumber=Oct15_HO4-->