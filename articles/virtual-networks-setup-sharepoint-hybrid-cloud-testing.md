<properties 
	pageTitle="Configuration d'une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test" 
	description="Découvrez comment créer une batterie de serveurs SharePoint intranet dans un environnement de cloud hybride pour le développement ou le test." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/05/2015" 
	ms.author="josephd"/>


# Configuration d'une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test

Les étapes de cette rubrique vous aident à créer un environnement de cloud hybride pour le test d'une batterie de serveurs SharePoint intranet hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
Cette configuration simule un SharePoint dans l'environnement de production Azure à partir de votre emplacement sur Internet. Elle comprend :

- Un réseau local simplifié (sous-réseau de réseau d'entreprise).
- Un réseau virtuel entre sites hébergé dans Microsoft Azure (TestVNET).
- Une connexion VPN de site à site.
- Une batterie de serveurs SharePoint à deux niveaux et un contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun pour :

- Développer et tester des applications sur une batterie de serveurs SharePoint intranet dans un environnement de cloud hybride.
- Tester cette charge de travail dans un cloud hybride.

La configuration de l'environnement de test de cloud hybride comprend trois grandes étapes :

1.	Configuration de l'environnement de cloud hybride pour le test.
2.	Configuration de l'ordinateur SQL Server (SQL1).
3.	Configuration du serveur SharePoint (SP1).

Si vous n'avez pas encore d'abonnement Azure, vous pouvez obtenir une évaluation gratuite. Pour cela, accédez à la page [Essayer Azure](http://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN, consultez [Avantage Azure pour les abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1 : Configuration de l'environnement de cloud hybride

Suivez les instructions de la rubrique [Configuration d'un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md) . Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau de réseau d'entreprise, n'hésitez pas à l'arrêter pour le moment.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_1.png)
 
## Phase 2 : configuration de l'ordinateur SQL Server (SQL1)

Dans le portail de gestion Azure, démarrez l'ordinateur DC2 si nécessaire.

Tout d'abord, créez une connexion Bureau à distance à DC2 avec les informations d'identification CORP\User1.

Ensuite, créez un compte d'administrateur de batterie de serveurs SharePoint. Ouvrez une invite de commandes Windows PowerShell de niveau administrateur sur DC2 et exécutez cette commande.

	New-ADUser -SamAccountName SPFarmAdmin -AccountPassword (read-host "Set user password" -assecurestring) -name "SPFarmAdmin" -enabled $true -ChangePasswordAtLogon $false

Lorsque vous êtes invité à fournir le mot de passe du compte SPFarmAdmin, tapez un mot de passe fort et notez-le dans un endroit sûr.

Ensuite, créez une machine virtuelle Azure pour SQL1 avec ces commandes à l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la nouvelle machine virtuelle SQL1 *using the local administrator account*.

1.	Dans le volet gauche du portail de gestion Azure, cliquez sur **Machines virtuelles**, puis sur **En cours d'exécution** dans la colonne Statut de SQL1.
2.	Dans la barre des tâches, cliquez sur **Se connecter**. 
3.	Lorsque vous êtes invité à ouvrir SQL1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s'affiche, cliquez sur **Connecter**.
5.	Si des informations d'identification vous sont demandées, utilisez celles-ci :
	- Nom : **SQL1**[Nom du compte de l'administrateur local]
	- Password: [Mot de passe du compte de l'administrateur local]
6.	Lorsqu'une zone de message de connexion Bureau à distance faisant référence aux certificats s'ouvre, cliquez sur **Oui**.

Ensuite, configurez les règles de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base et de SQL Server. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur SQL1, exécutez ces commandes.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit générer quatre réponses de l'adresse IP 10.0.0.1.

Ensuite, ajoutez le disque de données supplémentaire comme nouveau volume avec la lettre de lecteur F:.

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Service de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet de sommaire, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page Avant de commencer de l'Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l'invite, cliquez sur **OK**.
6.	Dans la page Spécifier la taille du volume, cliquez sur **Suivant**.
7.	Dans la page Affecter à la lettre d'un lecteur ou à un dossier page, cliquez sur **Suivant**.
8.	Dans la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9.	Dans la page Confirmer les sélections, cliquez sur **Créer**.
10.	Lorsque vous avez terminé, cliquez sur **Fermer**.

Exécutez les commandes suivantes à l'invite de commandes Windows PowerShell sur SQL1 :

	md f:\Data
	md f:\Log
	md f:\Backup

Ensuite, configurez SQL Server 2014 pour qu'il utilise le lecteur F: pour les nouvelles bases de données et pour les autorisations de compte d'utilisateur.

1.	Dans l'écran de démarrage, tapez **SQL Server Management**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.	Dans **Se connecter au serveur**, cliquez sur **Connecter**.
3.	Dans l'Explorateur d'objets, cliquez avec le bouton droit sur **SQL1**, puis cliquez sur **Propriétés**.
4.	Dans la fenêtre **Propriétés du serveur**, cliquez sur **Paramètres de base de données**.
5.	Recherchez les **Emplacements de la base de données par défaut** et définissez les valeurs suivantes : 
	- Pour **Data**, tapez le chemin **f:\Data**.
	- Pour **Log**, tapez le chemin **f:\Log**.
	- Pour **Backup**, tapez le chemin **f:\Backup**.
	- Notez que seules les nouvelles bases de données utilisent ces emplacements.
6.	Cliquez sur **OK** pour fermer la fenêtre.
7.	Dans l'**Explorateur d'objets**, ouvrez **Sécurité**.
8.	Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
9.	Dans **Nom de connexion**, tapez **CORP\User1**.
10.	Dans la page **Rôles serveur**, cliquez sur **sysadmin**, puis cliquez sur **OK**.
11.	Dans l'**Explorateur d'objets**, cliquez avec le bouton droit sur **Connexions**, puis cliquez sur **Nouvelle connexion**.
12.	Dans la page **Général**, dans **Nom de connexion**, tapez **CORP\SPFarmAdmin**.
13.	Dans la page **Rôles serveur**, sélectionnez **dbcreator**, puis cliquez sur **OK**.
14.	Fermez Microsoft SQL Server Management Studio.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_2.png)

 
## Phase 3 : Configuration du serveur SharePoint (SP1)

Commencez par créer une machine virtuelle Azure pour SP1 avec ces commandes à l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$image= Get-AzureVMImage | where { $_.Label -eq "SharePoint Server 2013 Trial" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$LocalAdminName="<A local administrator account name>" 
	$LocalAdminPW="<The password for the local administrator account>"
	$User1Password="<The password for the CORP\User1 account>"
	$vm1=New-AzureVMConfig -Name SP1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUserName $LocalAdminName -Password $LocalAdminPW -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $User1Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la machine virtuelle SP1 avec les informations d'identification CORP\User1.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur SP1, exécutez ces commandes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit générer quatre réponses de l'adresse IP 10.0.0.1.

Ensuite, configurez le SP1 pour la nouvelle batterie de serveurs SharePoint et un site d'équipe par défaut.

1.	Dans l'écran de démarrage, tapez **produits SharePoint 2013**, puis cliquez sur **Assistant Configuration des produits SharePoint 2013**. Lorsque vous êtes invité à autoriser le programme à apporter des modifications à l'ordinateur, cliquez sur **Oui**.
2.	Dans la page d'accueil des produits SharePoint, cliquez sur **Suivant**. 
3.	Dans la boîte de dialogue qui vous informe que certains services peuvent avoir besoin d'être redémarrés pendant la configuration, cliquez sur **Oui**.
4.	Dans la page Se connecter à une batterie de serveurs, cliquez sur **Créer une batterie de serveurs**, puis sur **Suivant**.
5.	Dans la page Spécifier les paramètres de la base de données de configuration, tapez **sql1.corp.contoso.com** dans **Serveur de base de données**, tapez **CORP\SPFarmAdmin** dans **Nom d'utilisateur**, tapez le mot de passe du compte SPFarmAdmin dans **Mot de passe**, puis cliquez sur **suivant**.
6.	Dans la page Spécifier les paramètres de sécurité de la batterie de serveurs, tapez **P@ssphrase** dans les **Phrase secrète** et **Confirmer la phrase secrète**, puis cliquez sur **suivant**.
7.	Dans la page Configurer l'application Web de l'Administration centrale de SharePoint, cliquez sur **Suivant**.
8.	Dans la page Fin de l'Assistant Configuration des produits SharePoint, cliquez sur **Suivant**. L'exécution de l'Assistant Configuration des produits SharePoint peut prendre quelques minutes.
9.	Dans la page Configuration réussie, cliquez sur **Terminer**. Internet Explorer s'ouvre ensuite avec un onglet appelé Assistant Configuration de batterie de serveurs initiale.
10.	Dans la boîte de dialogue **Contribuer à l'amélioration de SharePoint**, cliquez sur **Non, je ne souhaite pas participer**, puis cliquez sur **OK**.
11.	Dans **Comment voulez-vous configurer votre batterie SharePoint**, cliquez sur **Démarrer l'Assistant**.
12.	Dans la page Configuration de votre batterie SharePoint, dans **Compte de service**, cliquez sur **Utiliser un compte géré existant**.
13.	Dans **Services**, désactivez toutes les cases à cocher, sauf la case **Service d'états**, puis cliquez sur **suivant**. La page Opération en cours peut rester affichée un moment le temps que l'opération se termine.
14.	Dans la page Créer une collection de sites, dans **Titre et description**, tapez **Contoso Corporation** dans **Titre**, spécifiez l'URL **http://sp1**/, puis cliquez sur **OK**. La page Opération en cours peut rester affichée un moment le temps que l'opération se termine. Cette étape crée un site d'équipe dont l'URL est http://sp1.
15.	Dans la page Cette page est la dernière de l'Assistant Configuration de batterie de serveurs, cliquez sur **Terminer**. L'onglet Internet Explorer affiche le site Administration centrale de SharePoint 2013.
16.	Ouvrez une session sur l'ordinateur CLIENT1 avec les informations d'identification du compte CORP\User1, puis démarrez Internet Explorer.
17.	Dans la barre d'adresses, tapez **http://sp1/**, puis appuyez sur Entrée. Vous devriez voir le site d'équipe SharePoint de la société Contoso Corporation. L'affichage du site peut prendre un certain temps.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-set-up-SharePoint-hybrid-cloud-for-testing/CreateSPFarmHybridCloud_3.png)
 
Votre une batterie de serveurs SharePoint intranet dans un cloud hybride est maintenant prêt pour les tests.

## Ressources supplémentaires

[SharePoint sur les services d'infrastructure Azure](http://msdn.microsoft.com/library/azure/dn275955.aspx)

[Batterie de serveurs SharePoint](virtual-machines-sharepoint-farm-azure-preview.md)

[Configuration d'un environnement de cloud hybride pour le test](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configuration d'une application métier web dans un cloud hybride à des fins de test](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configuration de la synchronisation d'annuaires Office 365 (DirSync) dans un cloud hybride à des fins de test](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Configuration d'une simulation d'environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)
<!--HONumber=47-->
