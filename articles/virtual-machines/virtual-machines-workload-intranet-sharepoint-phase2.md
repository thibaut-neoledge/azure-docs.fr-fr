<properties
	pageTitle="Batterie de serveurs SharePoint Server 2013 - Phase 2 | Microsoft Azure"
	description="Créez et configurez les deux contrôleurs de domaine de réplica Active Directory dans la phase 2 de la batterie de serveurs SharePoint Server 2013 dans Azure."
	documentationCenter=""
	services="virtual-machines"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="josephd"/>

# Phase 2 de la charge de travail de la batterie de serveurs SharePoint intranet : configuration de contrôleurs de domaine

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)]Modèle de déploiement Resource Manager.

Au cours de cette phase de déploiement d'une batterie de serveurs SharePoint 2013 intranet avec des groupes de disponibilité SQL Server AlwaysOn dans des services d'infrastructure Azure, vous configurez deux contrôleurs de domaine dans le réseau virtuel Azure dans la gestion des services. Les requêtes web des clients pour les ressources de la batterie de serveurs SharePoint peuvent être authentifiées sur le réseau virtuel Azure, plutôt que d'envoyer le trafic d'authentification via la connexion VPN ou Azure ExpressRoute vers votre réseau local.

Vous devez terminer cette opération avant de passer à la [Phase 3](virtual-machines-workload-intranet-sharepoint-phase3.md). Consultez [Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md) pour prendre connaissance de toutes les phases.

## Création de machines virtuelles de contrôleurs de domaine SharePoint dans Azure

Tout d'abord, vous devez remplir la colonne **Nom de la machine virtuelle** de la table M et modifier les tailles de machine virtuelle le cas échéant dans la colonne **Taille minimale**.

Élément | Nom de la machine virtuelle | Image de galerie | Taille minimale
--- | --- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier contrôleur de domaine, par exemple DC1) | Windows Server 2012 R2 Datacenter | A2 (Medium)
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second contrôleur de domaine, par exemple DC2) | Windows Server 2012 R2 Datacenter | A2 (Medium)
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier ordinateur SQL Server, par exemple SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
4\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second ordinateur SQL Server, par exemple SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | A5
5\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (nœud majoritaire du cluster, par exemple MN1) | Windows Server 2012 R2 Datacenter | A1 (Small)
6\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier serveur d'applications SharePoint, par exemple APP1) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4 (ExtraLarge)
7\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second serveur d'applications SharePoint, par exemple APP2) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4 (ExtraLarge)
8\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (premier serveur Web SharePoint, par exemple WEB1) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4 (ExtraLarge)
9\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_ (second serveur Web SharePoint, par exemple WEB2) | Microsoft SharePoint Server 2013, version d'évaluation – Windows Server 2012 R2 | A4 (ExtraLarge)

**Table M – machines virtuelles pour la batterie de serveurs SharePoint 2013 intranet dans Azure**

Pour obtenir la liste complète des tailles des machines virtuelles, consultez la rubrique [Tailles de machines virtuelles](virtual-machines-size-specs.md).

Utilisez le bloc de commandes Azure PowerShell suivant pour créer les machines virtuelles pour les deux contrôleurs de domaine. Spécifiez les valeurs des variables en supprimant les caractères < and >. Notez que ce jeu de commandes Azure PowerShell utilise les valeurs des tables suivantes :

- Table M pour les machines virtuelles ;
- Table V pour les paramètres du réseau virtuel ;
- Table S pour le sous-réseau ;
- Table A pour les groupes à haute disponibilité ;
- Table C pour vos services cloud.

Souvenez-vous que vous avez défini les tables, V, S, A et C au cours de la [Phase 1 : configuration d'Azure](virtual-machines-workload-intranet-sharepoint-phase1.md).

Une fois que vous avez fourni toutes les valeurs requises, exécutez le bloc résultant à l'invite de commandes Azure PowerShell.

	# Create the first domain controller
	$vmName="<Table M – Item 1 - Virtual machine name column>"
	$vmSize="<Table M – Item 1 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$availSet="<Table A – Item 1 – Availability set name column>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the first domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$subnetName="<Table S – Item 1 – Subnet name column>"
	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 6 – Value column>

	$serviceName="<Table C – Item 1 – Cloud service name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

	# Create the second domain controller
	$vmName="<Table M – Item 2 - Virtual machine name column>"
	$vmSize="<Table M – Item 2 - Minimum size column, specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	$vm1=New-AzureVMConfig -Name $vmName -InstanceSize $vmSize -ImageName $image -AvailabilitySetName $availSet

	$cred=Get-Credential –Message "Type the name and password of the local administrator account for the second domain controller."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	$diskSize=<size of the additional data disk in GB>
	$diskLabel="<the label on the disk>"
	$lun=<Logical Unit Number (LUN) of the disk>
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $diskSize -DiskLabel $diskLabel -LUN $lun -HostCaching None

	$vm1 | Set-AzureSubnet -SubnetNames $subnetName

	$vm1 | Set-AzureStaticVNetIP -IPAddress <Table V – Item 7 – Value column>

	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName $vnetName

## Configuration du premier contrôleur de domaine

Ouvrez une session sur le premier contrôleur de domaine en utilisant les informations d'identification du compte d'administrateur local.

### <a id="logon"></a>Connexion une machine virtuelle à l’aide d’une connexion Bureau à distance

1.	Dans le portail Azure, cliquez sur **Machines virtuelles** dans le volet gauche.
2.	Pour vous connecter à une machine virtuelle, cliquez sur **En cours d'exécution** dans la colonne **État** en regard de son nom.
3.	Dans la barre de commandes en bas de la page, cliquez sur **Connexion**.
4.	Le portail Azure vous informe que le fichier .rdp est en cours de récupération. Cliquez sur **OK**.
5.	La boîte de dialogue de navigateur s'affiche et vous demande « Voulez-vous ouvrir ou enregistrer ComputerName.rdp depuis manage.windowsazure.com ? » Cliquez sur **Ouvrir**.
6.	Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Connecter**.
7.	Dans la boîte de dialogue **Sécurité de Windows**, cliquez sur **Utiliser un autre compte**.
8.	Sous **Nom d'utilisateur**, saisissez le nom de la machine virtuelle et le nom d'utilisateur du compte administrateur local créé avec la machine virtuelle (un compte d'ordinateur local). Utilisez le format suivant : *ComputerName*\*LocalAdministratorAccountName*
9.	Sous **Mot de passe**, tapez le mot de passe pour le compte administrateur local.
10.	Cliquez sur **OK**.
11.	Dans la boîte de dialogue **Connexion Bureau à distance**, cliquez sur **Oui**. Le bureau de la nouvelle machine s'affiche dans une fenêtre de session Bureau à distance.

Ensuite, vous devez ajouter le disque de données supplémentaire au premier contrôleur de domaine.

### <a id="datadisk"></a>Initialisation d'un disque vide

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet de contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page **Avant de commencer** de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page **Sélectionner le serveur et le disque**, cliquez sur **Disque 2**, puis sur **Suivant**. À l’invite, cliquez sur **OK**.
6.	Dans la page **Spécifier la taille du volume**, cliquez sur **Suivant**.
7.	Dans la page **Affecter à la lettre d’un lecteur ou à un dossier**, cliquez sur **Suivant**.
8.	Dans la page **Sélectionner les paramètres du système de fichiers**, cliquez sur **Suivant**.
9.	Dans la page **Confirmer les sélections**, cliquez sur **Créer**.
10.	Quand l’initialisation est terminée, cliquez sur **Fermer**.

Ensuite, testez la connectivité du premier contrôleur de domaine aux emplacements sur le réseau de votre organisation.

### <a id="testconn"></a>Test de la connectivité

1.	Ouvrez une invite de commandes Windows PowerShell à partir du bureau.
2.	Utilisez la commande **ping** pour effectuer un test Ping des noms et des adresses IP des ressources sur le réseau de votre entreprise.

Cette procédure garantit que la résolution de noms DNS fonctionne correctement (autrement dit, que la machine virtuelle est configurée correctement avec des serveurs DNS locaux) et que les paquets peuvent être transmis à destination et en provenance du réseau virtuel intersite.

Ensuite, à partir de l'invite de commandes Windows PowerShell sur le premier contrôleur de domaine, exécutez les commandes suivantes :

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

L'ordinateur redémarre.

## Configuration du second contrôleur de domaine

Ouvrez une session sur le second contrôleur de domaine en utilisant les informations d'identification du compte d'administrateur local. Pour obtenir des instructions, consultez la rubrique [Connexion une machine virtuelle à l'aide d'une connexion Bureau à distance](#logon).

Ensuite, vous devez ajouter le disque de données supplémentaire au second contrôleur de domaine. Consultez la rubrique [Initialisation d'un disque vide](#datadisk).

Ensuite, testez votre connectivité aux emplacements sur le réseau de votre organisation depuis le second contrôleur de domaine. Consultez la rubrique [Test de la connectivité](#testconn). Utilisez cette procédure pour garantir que la résolution de noms DNS fonctionne correctement (autrement dit, que la machine virtuelle est configurée correctement avec des serveurs DNS locaux) et que les paquets peuvent être transmis à destination et en provenance du réseau virtuel intersite.

Ensuite, à partir de l'invite de commandes Windows PowerShell sur le second contrôleur de domaine, exécutez les commandes suivantes :

	$domname="<DNS domain name of the domain for which this computer will be a domain controller>"
	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

L'ordinateur redémarre.

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

Ensuite, mettez à jour les serveurs DNS de votre réseau virtuel afin qu'Azure affecte aux machines virtuelles les adresses IP des deux nouveaux contrôleurs de domaine à utiliser en tant que serveurs DNS. Notez que cette procédure utilise les valeurs de la table V (pour vos paramètres de réseau virtuel).

1.	Dans le volet gauche du portail Azure, cliquez sur **Réseaux**, puis cliquez sur le nom de votre réseau virtuel (Table V – Élément 1 – Colonne Valeur).
2.	Cliquez sur **Configurer**.
3.	Sous **Serveurs DNS**, supprimez les entrées correspondant aux serveurs DNS qui se trouvent sur votre réseau local.
4.	Sous **Serveurs DNS**, ajoutez deux entrées avec des noms conviviaux et les adresses IP des deux éléments de tables suivants :
 - Table V – Élément 6 – Colonne Valeur
 - Table V – Élément 7 – Colonne Valeur
5.	Dans la barre de commandes de la partie inférieure, cliquez sur **Enregistrer**.
6.	Dans le volet gauche du portail Azure, cliquez sur **Machines virtuelles**, puis cliquez sur la colonne **État** située en regard du nom de votre premier contrôleur de domaine.
7.	Dans la barre de commandes, cliquez sur **Redémarrer**.
8.	Lorsque le premier contrôleur de domaine est démarré, cliquez sur la colonne **État** en regard du nom de votre second contrôleur de domaine.
9.	Dans la barre de commandes, cliquez sur **Redémarrer**. Attendez le démarrage du second contrôleur de domaine.

Notez que vous redémarrez les deux contrôleurs de domaine afin qu'ils ne soient pas configurés avec les serveurs DNS locaux en tant que serveurs DNS. Les deux étant eux-mêmes des serveurs DNS, ils sont automatiquement configurés avec les serveurs DNS locaux en tant que redirecteurs DNS lorsqu'ils sont promus contrôleurs de domaine.

Ensuite, vous devez créer un site de réplication Active Directory pour garantir que les serveurs dans le réseau virtuel Azure utilisent les contrôleurs de domaine locaux. Ouvrez une session sur le contrôleur de domaine principal avec le compte sp\_install, puis exécutez les commandes suivantes à partir d'une invite de commandes de niveau administrateur Windows PowerShell :

	$vnet="<Table V – Item 1 – Value column>"
	$vnetSpace="<Table V – Item 5 – Value column>"
	New-ADReplicationSite -Name $vnet
	New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

Ce schéma illustre la configuration résultant de la réussite de cette phase, avec les noms d’ordinateur d’espaces réservés.

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## Étape suivante

Pour poursuivre la configuration de cette charge de travail, passez à la [Phase 3 : configurer l’infrastructure SQL Server](virtual-machines-workload-intranet-sharepoint-phase3.md).

## Ressources supplémentaires

[Déploiement de SharePoint avec des groupes de disponibilité SQL Server AlwaysOn dans Azure](virtual-machines-workload-intranet-sharepoint-overview.md)

[Batteries de serveurs SharePoint hébergés dans des services d’infrastructure Azure](virtual-machines-sharepoint-infrastructure-services.md)

[Infographie SharePoint avec SQL Server AlwaysOn](http://go.microsoft.com/fwlink/?LinkId=394788)

[Architectures Microsoft Azure pour SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx)

[Instructions d’implémentation des services d’infrastructure Azure](virtual-machines-infrastructure-services-implementation-guidelines.md)

[Charge de travail des services d’infrastructure Azure : applications métier à haute disponibilité](virtual-machines-workload-high-availability-lob-application.md)

<!---HONumber=AcomDC_1125_2015-->