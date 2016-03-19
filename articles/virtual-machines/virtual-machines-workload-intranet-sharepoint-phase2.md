<properties
	pageTitle="Batterie de serveurs SharePoint Server 2013 - Phase 2 | Microsoft Azure"
	description="Créez et configurez les deux contrôleurs de domaine de réplica Active Directory dans la phase 2 de la batterie de serveurs SharePoint Server 2013 dans Azure."
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
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Phase 2 de la charge de travail de la batterie de serveurs SharePoint intranet : configuration de contrôleurs de domaine

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Modèle de déploiement classique

Au cours de cette phase de déploiement d’une batterie SharePoint 2013 intranet uniquement avec des groupes de disponibilité SQL Server AlwaysOn dans des services d’infrastructure Azure, vous configurez deux contrôleurs de domaine dans le réseau virtuel Azure. Les requêtes web des clients pour les ressources de la batterie de serveurs SharePoint peuvent être authentifiées sur le réseau virtuel Azure, au lieu d’envoyer le trafic d’authentification via la connexion VPN de site à site ou Azure ExpressRoute vers votre réseau local.

Vous devez terminer cette opération avant de passer à la [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

## Création de machines virtuelles de contrôleurs de domaine SharePoint dans Azure

Tout d'abord, vous devez remplir la colonne **Nom de la machine virtuelle** de la table M et modifier les tailles de machine virtuelle le cas échéant dans la colonne **Taille minimale**.

Élément | Nom de la machine virtuelle | Image de galerie | Taille minimale
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier contrôleur de domaine, par exemple DC1) | Windows Server 2012 R2 Datacenter | Standard\_A2
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second contrôleur de domaine, par exemple DC2) | Windows Server 2012 R2 Datacenter | Standard\_A2
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier ordinateur SQL Server, par exemple SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second ordinateur SQL Server, par exemple SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard\_A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nœud majoritaire du cluster, par exemple MN1) | Windows Server 2012 R2 Datacenter | Standard\_A1
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier serveur d'applications SharePoint, par exemple APP1) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | Standard\_A4
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second serveur d'applications SharePoint, par exemple APP2) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | Standard\_A4
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier serveur Web SharePoint, par exemple WEB1) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | Standard\_A4
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second serveur Web SharePoint, par exemple WEB2) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | Standard\_A4

**Table M – machines virtuelles pour la batterie de serveurs SharePoint 2013 intranet dans Azure**

Pour obtenir la liste complète des tailles des machines virtuelles, consultez la rubrique [Tailles de machines virtuelles](virtual-machines-size-specs.md).

Utilisez le bloc de commandes Azure PowerShell suivant pour créer les machines virtuelles pour les deux contrôleurs de domaine. Spécifiez les valeurs des variables en supprimant les caractères < and >. Notez que ce jeu de commandes Azure PowerShell utilise les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table ST pour vos comptes de stockage ;
- Table A pour les groupes à haute disponibilité ;

Souvenez-vous que vous avez défini les tables V, S, ST et A au cours de la [Phase 1 : configurer Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

> [AZURE.NOTE]Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant dans l'invite de commandes Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 1 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 1 – Availability set name column>"
	
	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column>"
	$staticIP="<Table V – Item 6 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column>"
	$staticIP="<Table V – Item 7 - Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	
	$diskSize=<size of the extra disk for AD DS data in GB>
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE]Étant donné que ces machines virtuelles sont destinées à une application intranet, elles ne reçoivent pas d’adresse IP publique ou d’étiquette de nom de domaine DNS, et ne sont pas exposées à Internet. Toutefois, cela signifie également que vous ne pouvez pas vous y connecter à partir du portail Azure. Le bouton **Se connecter** n’est pas disponible quand vous affichez les propriétés de la machine virtuelle. Utilisez l’accessoire Connexion Bureau à distance ou un autre outil Bureau à distance pour vous connecter à la machine virtuelle à l’aide de son adresse IP privée ou nom DNS intranet.

## Configuration du premier contrôleur de domaine

Utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance à la première machine virtuelle contrôleur de domaine. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Ensuite, vous devez ajouter le disque de données supplémentaire au premier contrôleur de domaine.

### <a id="datadisk"></a>Initialisation d'un disque vide

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet de contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page Avant de commencer de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l'invite, cliquez sur OK.
6.	À la page Spécifier la taille du volume, cliquez sur **Suivant**.
7.	À la page Affecter à la lettre d'un lecteur ou à un dossier, cliquez sur **Suivant**.
8.	À la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9.	À la page Confirmer les sélections, cliquez sur **Créer**.
10.	Lorsque vous avez terminé, cliquez sur **Fermer**.

Ensuite, testez la connectivité du premier contrôleur de domaine aux emplacements sur le réseau de votre organisation.

### <a id="testconn"></a>Test de la connectivité

1.	Ouvrez une invite de commandes Windows PowerShell à partir du bureau.
2.	Utilisez la commande **ping** pour effectuer un test Ping des noms et des adresses IP des ressources sur le réseau de votre entreprise.

Cette procédure garantit que la résolution de noms DNS fonctionne correctement (autrement dit, que la machine virtuelle est configurée correctement avec des serveurs DNS locaux) et que les paquets peuvent être transmis à destination et en provenance du réseau virtuel intersite. Si ce test de base échoue, contactez votre service informatique pour résoudre les problèmes liés à la résolution du nom DNS et à la remise de paquets.

Ensuite, à partir de l'invite de commandes Windows PowerShell sur le premier contrôleur de domaine, exécutez les commandes suivantes :

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Vous serez invité à fournir les informations d'identification d'un compte d'administrateur de domaine. L'ordinateur redémarre.

## Configuration du second contrôleur de domaine

Utilisez le client Bureau à distance de votre choix et créez une connexion Bureau à distance à la seconde machine virtuelle contrôleur de domaine. Utilisez son nom d'ordinateur ou DNS intranet et les informations d'identification du compte d'administrateur local.

Ensuite, vous devez ajouter le disque de données supplémentaire au second contrôleur de domaine. Consultez la rubrique [Initialisation d'un disque vide](#datadisk).

Ensuite, exécutez les commandes suivantes à partir de l'invite de commandes Windows PowerShell sur le second contrôleur de domaine :

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Vous serez invité à fournir les informations d'identification d'un compte d'administrateur de domaine. L'ordinateur redémarre.

Vous devez ensuite mettre à jour les serveurs DNS de votre réseau virtuel afin qu'Azure affecte aux machines virtuelles les adresses IP des deux nouveaux contrôleurs de domaine à utiliser en tant que serveurs DNS. Notez que cette procédure utilise les valeurs de la table V (pour vos paramètres de réseau virtuel) et de la table M (pour vos machines virtuelles).

1.	Dans le volet gauche du portail Azure, cliquez sur **Réseaux virtuels**, puis cliquez sur le nom de votre réseau virtuel (Table V – Élément 1 – Colonne Valeur).
2.	Dans le volet **Paramètres**, cliquez sur **Serveurs DNS**.
3.	Dans le volet **Serveurs DNS**, tapez la commande suivante :
	- Pour **Serveur DNS principal** : Table V – Élément 6 – Colonne Valeur
	- Pour **Serveur DNS secondaire** : Table V – Élément 7 – Colonne Valeur
4.	Dans le volet de gauche du portail Azure, cliquez sur **Machines virtuelles**.
5.	Dans le **volet Machines virtuelles**, cliquez sur le nom de votre premier contrôleur de domaine (Table M – Élément 1 - Colonne Nom de machine virtuelle).
6.	Dans le volet de la machine virtuelle, cliquez sur **Redémarrer**.
7.	Une fois le premier contrôleur de domaine démarré, cliquez sur le nom de votre second contrôleur de domaine dans le volet **Machines virtuelles** (Table M – Élément 2 - Colonne Nom de machine virtuelle).
8.	Dans le volet de la machine virtuelle, cliquez sur **Redémarrer**. Attendez le démarrage du second contrôleur de domaine.

Notez que les deux contrôleurs de domaine sont redémarrés afin qu'ils ne soient pas configurés avec les serveurs DNS locaux en tant que serveurs DNS. Les deux étant eux-mêmes des serveurs DNS, ils sont automatiquement configurés avec les serveurs DNS locaux en tant que redirecteurs DNS lorsqu'ils ont été promus contrôleurs de domaine.

L'étape suivante est la création d'un site de réplication Active Directory pour garantir que les serveurs dans le réseau virtuel Azure utilisent les contrôleurs de domaine locaux. Connectez-vous au contrôleur de domaine principal en tant qu'administrateur, puis exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell de niveau administrateur :

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet 
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

## Configuration de comptes et d'autorisations de batterie de serveurs SharePoint

La batterie de serveurs SharePoint aura besoin des comptes d'utilisateur suivants :

- sp\_farm : un compte d'utilisateur pour la gestion de batteries de serveurs SharePoint.
- sp\_farm\_db : un compte d'utilisateur disposant de droits d'administrateur système sur des instances SQL Server.
- sp\_install: un compte d'utilisateur disposant des droits d'administration nécessaires pour installer des rôles et des fonctionnalités.
- sqlservice : un compte d'utilisateur pour l'exécution d'instances SQL Server.

Ensuite, connectez-vous à n'importe quel ordinateur avec un compte d'administrateur de domaine pour le domaine dont les contrôleurs de domaine sont membres, ouvrez une invite de commandes de niveau administrateur Windows PowerShell, puis exécutez les commandes suivantes *l'une après l'autre* :

	New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

	New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Vous devrez entrer un mot de passe pour chaque commande. Enregistrez ces noms de compte et mots de passe et stockez-les dans un emplacement sécurisé.

Ensuite, procédez comme suit pour ajouter des propriétés de compte aux nouveaux comptes d'utilisateur.

1.	À partir de l'écran d'accueil, saisissez **Utilisateurs Active Directory**, puis cliquez sur **Utilisateurs et ordinateurs Active Directory**.
2.	Dans le volet d'arborescence, ouvrez votre domaine, puis cliquez sur **Utilisateurs**.
3.	Dans le volet de contenu, cliquez avec le bouton droit sur **sp\_install**, puis cliquez sur **Ajouter à un groupe**.
4.	Dans la boîte de dialogue **Sélectionner des groupes**, saisissez **Admins du domaine**, puis cliquez deux fois sur **OK**.
5.	Dans la boîte de dialogue, cliquez sur **Affichage et sur Fonctionnalités avancées**. Cette option vous permet de voir tous les conteneurs et les onglets masqués dans les fenêtres de propriétés des objets Active Directory.
6.	Cliquez avec le bouton droit sur votre nom de domaine, puis cliquez sur **Propriétés**.
7.	Dans la boîte de dialogue **Propriétés**, cliquez sur l'onglet **Sécurité**, puis cliquez sur le bouton **Avancé**.
8.	Dans la fenêtre **Paramètres de sécurité avancés pour <YourDomain>**, cliquez sur **Ajouter**.
9.	Dans la fenêtre **Entrée d’autorisation pour <YourDomain>**, cliquez sur **Sélectionnez un principal**.
10.	Dans la zone de texte, saisissez **<YourDomain>\\sp\_install**, puis cliquez sur **OK**.
11.	Sélectionner **Autoriser** pour la **création des objets ordinateur**, puis cliquez trois fois sur **OK**.

Cela illustre la configuration résultant de la réussite de cette phase, avec les noms d’ordinateur d’espaces réservés.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Étape suivante

- Pour poursuivre la configuration de cette charge de travail, utilisez la [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md).

<!---HONumber=AcomDC_1217_2015-->