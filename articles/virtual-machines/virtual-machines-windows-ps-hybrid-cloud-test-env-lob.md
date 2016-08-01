<properties 
	pageTitle="Environnement de test d'application métier | Microsoft Azure" 
	description="Apprenez à créer une application métier basée sur le web dans un environnement de cloud hybride et destinée aux professionnels de l'informatique ou au test des développements." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/19/2016" 
	ms.author="josephd"/>

# Configuration d’une application métier web dans un cloud hybride pour le test

Cette rubrique vous guide lors de la création d’un environnement cloud hybride pour tester une application métier web hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

Pour obtenir un exemple d’application métier de production hébergée dans Azure, consultez le modèle d’architecture **Applications métier** dans les [Diagrammes et modèles d’architecture logicielle de Microsoft](http://msdn.microsoft.com/dn630664).

Cette configuration simule une application métier dans un environnement de production Azure à partir de votre emplacement sur Internet. Elle comprend :

- Un réseau local simplifié (sous-réseau de réseau d'entreprise).
- Un réseau virtuel intersite hébergé dans Azure (TestVNET).
- Une connexion VPN de site à site.
- Un ensemble de serveur d'entreprise, de serveur SQL et de contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir duquel vous pouvez :

- Développer et tester des applications métier hébergées sur IIS (Internet Information Services) avec un serveur principal de base de données SQL Server 2014 dans Azure.
- Tester cette charge de travail dans un cloud hybride.

Il existe trois principales étapes pour configurer cet environnement de test de cloud hybride :

1.	Configurer l’environnement de cloud hybride pour le test.
2.	Configurer l'ordinateur du serveur SQL (SQL1).
3.	Configurer le serveur métier (LOB1).

Cette charge de travail nécessite un abonnement Azure. Si vous avez un abonnement MSDN ou Visual Studio, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1 : configuration de l’environnement de cloud hybride

Suivez les instructions de la rubrique [Configuration d’un environnement de cloud hybride à des fins de test](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau de réseau d’entreprise, n’hésitez pas à l’arrêter pour le moment.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

> [AZURE.NOTE] Pour la phase 1, vous pouvez également configurer la [simulation d’environnement de test de cloud hybride](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).
 
## Phase 2 : configurer l’ordinateur du serveur SQL (SQL1)

Dans le portail Azure, démarrez l’ordinateur DC2 si nécessaire.

Ensuite, créez une machine virtuelle Azure pour SQL1 avec ces commandes dans une invite de commandes Azure PowerShell sur votre ordinateur local. Avant d’exécuter ces commandes, renseignez les valeurs des variables et supprimez les caractères < et >.

	$rgName="<your resource group name>"
	$locName="<the Azure location of your resource group>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty
	
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Utilisez le portail Azure pour vous connecter à SQL1 à l’aide du compte d’administrateur local.

1.	Dans le volet gauche du portail de gestion Azure, cliquez sur **Machines virtuelles**, puis sur **En cours d’exécution** dans la colonne Statut de SQL1.
2.	Dans la barre des tâches, cliquez sur **Se connecter**.
3.	Quand vous êtes invité à ouvrir SQL1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	Si des informations d’identification vous sont demandées, utilisez celles-ci :
	- Nom : **SQL1\**[Nom du compte de l’administrateur local]
	- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, configurez les règles de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base et de SQL Server. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur SQL1, exécutez ces commandes.

	New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 192.168.0.4.

Ensuite, ajoutez le disque de données supplémentaire comme nouveau volume avec la lettre de lecteur F:.

1.	Dans le volet gauche du Gestionnaire de serveur, cliquez sur **Services de fichiers et de stockage**, puis sur **Disques**.
2.	Dans le volet Contenu, dans le groupe **Disques**, cliquez sur **disque 2** (avec la **Partition** définie sur **Inconnue**).
3.	Cliquez sur **Tâches**, puis sur **Nouveau volume**.
4.	Dans la page Avant de commencer de l’Assistant Nouveau volume, cliquez sur **Suivant**.
5.	Dans la page Sélectionner le serveur et le disque, cliquez sur **Disque 2**, puis sur **Suivant**. À l’invite, cliquez sur **OK**.
6.	Dans la page Spécifier la taille du volume, cliquez sur **Suivant**.
7.	À la page Affecter à la lettre d'un lecteur ou à un dossier, cliquez sur **Suivant**.
8.	À la page Sélectionner les paramètres du système de fichiers, cliquez sur **Suivant**.
9.	À la page Confirmer les sélections, cliquez sur **Créer**.
10.	Lorsque vous avez terminé, cliquez sur **Fermer**.

Exécutez ces commandes à l’invite de commandes Windows PowerShell sur SQL1 :

	md f:\Data
	md f:\Log
	md f:\Backup

Ensuite, joignez SQL1 au domaine Active Directory CORP avec les commandes suivantes dans l’invite Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Utilisez le compte CORP\\User1 lorsque vous êtes invité à fournir vos informations d’identification du compte de domaine pour la commande **Add-Computer**.

Après le redémarrage, utilisez le portail Azure pour vous connecter à SQL1 à l’aide *du compte d’administrateur local*.

Ensuite, configurez SQL Server 2014 pour qu'il utilise le lecteur F: pour les nouvelles bases de données et pour les autorisations de compte d'utilisateur.

1.	Dans l’écran d’accueil, tapez **SQL Server Management**, puis cliquez sur **SQL Server 2014 Management Studio**.
2.	Dans **Se connecter au serveur**, cliquez sur **Connecter**.
3.	Dans le volet d’arborescence de l’Explorateur d’objets, cliquez avec le bouton droit sur **SQL1**, puis cliquez sur **Propriétés**.
4.	Dans la fenêtre **Propriétés du serveur**, cliquez sur **Paramètres de base de données**.
5.	Recherchez les **Emplacements de la base de données par défaut** et définissez les valeurs suivantes :
	- Pour**Data**, tapez le chemin d’accès **f:\\Data**.
	- Pour **Log**, tapez le chemin d’accès **f:\\Log**.
	- Pour **Backup**, tapez le chemin d’accès **f:\\Backup**.
	- Remarque : seules les nouvelles bases de données utilisent ces emplacements.
6.	Cliquez sur **OK** pour fermer la fenêtre.
7.	Dans le volet d’arborescence de l’**Explorateur d’objets**, ouvrez **Sécurité**.
8.	Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
9.	Dans **Nom de connexion**, tapez **CORP\\User1**.
10.	Dans la page **Rôles de serveur**, cliquez sur **sysadmin**, puis sur **OK**.
11.	Fermez Microsoft SQL Server Management Studio.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)
 
## Phase 3 : configurer le serveur métier (LOB1).

Commencez par créer une machine virtuelle Azure pour LOB1 avec les commandes suivantes à l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, utilisez le portail Azure pour vous connecter à LOB1 avec les informations d’identification du compte d’administrateur local.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes de Windows PowerShell de niveau administrateur sur LOB1, exécutez les commandes suivantes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 192.168.0.4.

Ensuite, joignez LOB1 au domaine Active Directory CORP avec les commandes suivantes dans l’invite Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Utilisez le compte CORP\\User1 lorsque vous êtes invité à fournir vos informations d’identification du compte de domaine pour la commande **Add-Computer**.

Après le redémarrage, utilisez le portail Azure pour vous connecter à LOB1 avec le compte et le mot de passe CORP\\User1.

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
13.	Dans la barre d’adresses, tapez **http://lob1/**, puis appuyez sur Entrée. Vous devez voir la page web IIS 8 par défaut.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)
 
Cet environnement est maintenant prêt pour le déploiement de votre application web sur LOB1 et le test des fonctionnalités et des performances à partir du sous-réseau de réseau d'entreprise.

## Étape suivante

- Déployer d’autres [charges de travail](virtual-machines-windows-ps-hybrid-cloud-test-envs.md) dans cet environnement.

<!---HONumber=AcomDC_0720_2016-->