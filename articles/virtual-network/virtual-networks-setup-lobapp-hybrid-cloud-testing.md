<properties 
	pageTitle="Environnement de test d'application métier | Microsoft Azure" 
	description="Apprenez à créer une application métier basée sur le web dans un environnement de cloud hybride et destinée aux professionnels de l'informatique ou au test des développements." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/10/2015" 
	ms.author="josephd"/>

# Configuration d’une application métier web dans un cloud hybride pour le test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager


Cette rubrique vous guide lors de la création d'un environnement de cloud hybride pour tester une application métier intranet hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)

Pour obtenir un exemple d’application métier de production hébergée dans Azure, consultez le modèle d’architecture **Applications métier** dans les [Diagrammes et modèles d’architecture logicielle de Microsoft](http://msdn.microsoft.com/dn630664).

Cette configuration simule une application métier dans un environnement de production Azure à partir de votre emplacement sur Internet. Elle comprend :

- Un réseau local simplifié (sous-réseau de réseau d'entreprise).
- Un réseau virtuel intersite hébergé dans Azure (TestVNET).
- Une connexion VPN de site à site.
- Un ensemble de serveur d'entreprise, de serveur SQL et de contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir duquel vous pouvez :

- Développer et tester des applications métier hébergées sur IIS (Internet Information Services) avec un serveur principal de base de données SQL Server 2014 dans Azure.
- Tester cette charge de travail dans un cloud hybride.

Il existe trois principales étapes pour configurer cet environnement de test de cloud hybride :

1.	Configurer l’environnement de cloud hybride pour le test.
2.	Configurer l'ordinateur du serveur SQL (SQL1).
3.	Configurer le serveur métier (LOB1).

Si vous ne disposez pas déjà d'un abonnement Azure, vous pouvez obtenir une évaluation gratuite sur la page [Essai d'Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1 : configuration de l’environnement de cloud hybride

Suivez les instructions de la rubrique [Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md). Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau de réseau d’entreprise, n’hésitez pas à l’arrêter pour le moment.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_1.png)

> [AZURE.NOTE] Pour la phase 1, vous pouvez également configurer la simulation d’environnement de test de cloud hybride. Suivez les instructions de la rubrique [Configuration d’une simulation d’environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
 
## Phase 2 : configurer l’ordinateur du serveur SQL (SQL1)

Dans le portail de gestion Azure, démarrez l'ordinateur DC2 si nécessaire.

Ensuite, créez une machine virtuelle Azure pour SQL1 avec ces commandes dans une invite de commandes Azure PowerShell sur votre ordinateur local. Avant d'exécuter ces commandes, renseignez les valeurs des variables et supprimez les caractères < and >.

	$storageacct="<Name of the storage account for your TestVNET virtual network>"
	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for SQL1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	Set-AzureStorageAccount -StorageAccountName $storageacct
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "SQL Server 2014 RTM Standard on Windows Server 2012 R2" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name SQL1 -InstanceSize Large -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 100 -DiskLabel SQLFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la nouvelle machine virtuelle SQL1 *à l’aide du compte d’administrateur local*.

1.	Dans le volet gauche du portail de gestion Azure, cliquez sur **Machines virtuelles**, puis sur **En cours d’exécution** dans la colonne Statut de SQL1.
2.	Dans la barre des tâches, cliquez sur **Se connecter**. 
3.	Quand vous êtes invité à ouvrir SQL1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	Si des informations d’identification vous sont demandées, utilisez celles-ci :
	- Nom : **SQL1\**[Nom du compte de l’administrateur local]
	- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, configurez les règles de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base et de SQL Server. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur SQL1, exécutez ces commandes.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit obtenir quatre réponses correctes à partir de l’adresse IP 10.0.0.1.

Ensuite, ajoutez le disque de données supplémentaire comme nouveau volume avec la lettre de lecteur F:.

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet Contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page Avant de commencer de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l’invite, cliquez sur **OK**.
6.	Dans la page Spécifier la taille du volume, cliquez sur **Suivant**.
7.	À la page Affecter à la lettre d'un lecteur ou à un dossier, cliquez sur **Suivant**.
8.	À la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9.	À la page Confirmer les sélections, cliquez sur **Créer**.
10.	Lorsque vous avez terminé, cliquez sur **Fermer**.

Exécutez ces commandes à l’invite de commandes Windows PowerShell sur SQL1 :

	md f:\Data
	md f:\Log
	md f:\Backup

Ensuite, configurez SQL Server 2014 pour qu'il utilise le lecteur F: pour les nouvelles bases de données et pour les autorisations de compte d'utilisateur.

1.	Dans l’écran d’accueil, tapez **SQL Server Management**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.	Dans **Se connecter au serveur**, cliquez sur **Connecter**.
3.	Dans le volet d’arborescence de l’Explorateur d’objets, cliquez avec le bouton droit sur **SQL1**, puis cliquez sur **Propriétés**.
4.	Dans la fenêtre **Propriétés du serveur**, cliquez sur **Paramètres de base de données**.
5.	Recherchez les **Emplacements de la base de données par défaut** et définissez les valeurs suivantes : 
	- Pour**Data**, tapez le chemin d’accès **f:\\Data**.
	- Pour **Log**, tapez le chemin d’accès **f:\\Log**.
	- Pour **Backup**, tapez le chemin d’accès **f:\\Backup**.
	- Remarque : seules les nouvelles bases de données utilisent ces emplacements.
6.	Cliquez sur **OK** pour fermer la fenêtre.
7.	Dans le volet d’arborescence de l’**Explorateur d’objets**, ouvrez **Sécurité**.
8.	Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
9.	Dans **Nom de connexion**, tapez **CORP\\User1**.
10.	Dans la page **Rôles de serveur**, cliquez sur **sysadmin**, puis sur **OK**.
11.	Fermez Microsoft SQL Server Management Studio.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_2.png)
 
## Phase 3 : configurer le serveur métier (LOB1).

Commencez par créer une machine virtuelle Azure pour LOB1 avec les commandes suivantes à l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$cred2=Get-Credential -UserName "CORP\User1" -Message "Now type the password for the CORP\User1 account."
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name LOB1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la machine virtuelle LOB1 avec les informations d'identification du compte CORP\\User1.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes de Windows PowerShell de niveau administrateur sur LOB1, exécutez les commandes suivantes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit obtenir quatre réponses correctes à partir de l’adresse IP 10.0.0.1.

Ensuite, configurez LOB1 pour IIS et testez l'accès à partir de CLIENT1.

1.	Exécutez le Gestionnaire de serveur, puis cliquez sur **Ajouter des rôles et fonctionnalités**.
2.	Dans la page Avant de commencer, cliquez sur **Suivant**.
3.	Dans la page Sélectionner le type d’installation, cliquez sur **Suivant**.
4.	Sur la page Sélectionner le serveur de destination, cliquez sur **Suivant**.
5.	Dans la page Rôles de serveur, cliquez sur **Serveur Web (IIS)** dans la liste des **rôles**.
6.	Quand vous y êtes invité, cliquez sur **Ajouter des fonctionnalités**, puis sur **Suivant**.
7.	Dans la page Sélectionner des fonctionnalités, cliquez sur **Suivant**.
8.	Dans la page Serveur Web (IIS), cliquez sur **Suivant**.
9.	Dans la page Sélectionner des services de rôle, cochez ou décochez les cases pour les services requis pour tester votre application métier, puis cliquez sur **Suivant**.
10.	Sur la page Confirmer les sélections pour l’installation, cliquez sur **Installer**.
11.	Attendez la fin de l’installation des composants, puis cliquez sur **Fermer**.
12.	Ouvrez une session sur l'ordinateur CLIENT1 avec les informations d'identification du compte CORP\\User1, puis démarrez Internet Explorer.
13.	Dans la barre d’adresses, tapez ****http://lob1/**, puis appuyez sur Entrée. Vous devez voir la page web IIS 8 par défaut.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-lobapp-hybrid-cloud-testing/CreateLOBAppHybridCloud_3.png)
 
Cet environnement est maintenant prêt pour le déploiement de votre application web sur LOB1 et le test des fonctionnalités et des performances à partir du sous-réseau de réseau d'entreprise.

## Ressources supplémentaires

[Diagrammes et modèles de l’architecture logicielle Microsoft](http://msdn.microsoft.com/dn630664)

[Plateforme de serveur adaptée à l’hébergement Web (IIS)](http://technet.microsoft.com/library/hh831818)

[Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configuration de la synchronisation d’annuaires (DirSync) Office 365 dans un cloud hybride pour le test](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Configuration d’une simulation d’environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Environnements de test de cloud hybride Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Instructions d’implémentation des services d’infrastructure Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)
 

<!---HONumber=AcomDC_0128_2016-->