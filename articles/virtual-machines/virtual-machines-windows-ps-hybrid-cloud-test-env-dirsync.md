<properties 
	pageTitle="Environnement de test Office 365 DirSync | Microsoft Azure" 
	description="Apprenez à configurer un serveur de synchronisation d'annuaires (DirSync) Office 365 dans un cloud hybride pour les professionnels de l'informatique ou le test des développements." 
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
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configuration de la synchronisation d'annuaires Office 365 (DirSync) dans un cloud hybride pour le test
 
Cette rubrique vous guide lors de la création d'un environnement de cloud hybride pour le test de la synchronisation d'annuaires (DirSync) Office 365 avec la synchronisation de mot de passe hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Cette configuration simule un serveur de synchronisation d'annuaires dans un environnement de production Azure à partir de votre emplacement sur Internet. Elle comprend :

- Un réseau local simplifié (sous-réseau de réseau d'entreprise).
- Un réseau virtuel intersite hébergé dans Azure (TestVNET).
- Une connexion VPN de site à site.
- Un abonnement d'évaluation à Office 365 FastTrack.
- Un serveur de synchronisation d'annuaires exécutant l’outil Azure AD Connect et le contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir duquel vous pouvez :

- Développer et tester des applications pour Office 365 qui reposent sur la synchronisation avec un domaine Active Directory local à l'aide de la synchronisation de mot de passe.
- Effectuer les tests de cette charge de travail informatique basée sur le cloud.

Il existe trois principales étapes pour configurer cet environnement de test de cloud hybride :

1.	Configurer l’environnement de cloud hybride pour le test.
2.	Configurer la version d'évaluation d'Office 365 FastTrack.
3.	Configurer le serveur de synchronisation d'annuaires (DS1).

Si vous n’avez pas encore d’abonnement Azure, vous pouvez obtenir un compte gratuit sur la page [Version d’essai d’Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN ou Visual Studio, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1 : configuration de l’environnement de cloud hybride

Suivez les instructions de la rubrique [Configuration d’un environnement de cloud hybride à des fins de test](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md). Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau de réseau d’entreprise, n’hésitez pas à l’arrêter pour le moment.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph1.png)

> [AZURE.NOTE] Pour la phase 1, vous pouvez également configurer la [simulation d’environnement de test de cloud hybride](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md).

## Phase 2: configuration de la version d’évaluation d’Office 365 FastTrack

Pour démarrer votre version d'évaluation d'Office 365 FastTrack, vous avez besoin d'un nom de société fictif et d'un compte Microsoft. Nous vous recommandons d'utiliser une variante du nom de société Contoso pour le nom de votre société, qui est une société fictive utilisée dans l'exemple de contenu de Microsoft, mais ce n'est pas requis.

Ensuite, inscrivez-vous à un compte Microsoft. Accédez à **http://outlook.com**, puis créez un compte avec une adresse de messagerie telle que user123@outlook.com. Vous vous inscrirez à une version d'évaluation d'Office 365 FastTrack à l'aide de ce compte.

Ensuite, inscrivez-vous à une nouvelle version d'évaluation d'Office 365 Enterprise E3.

1.	Connectez-vous à CLIENT1 avec les informations d'identification du compte CORP\\User1.
2.	Ouvrez Internet Explorer et accédez à **https://go.microsoft.com/fwlink/p/?LinkID=403802**.
3.	Suivez le processus d'inscription à la version d'évaluation d'Office 365 Enterprise E3.

Lorsque vous êtes invité à indiquer une **adresse de messagerie professionnelle**, spécifiez votre nouveau compte Microsoft.

Lorsque vous êtes invité à créer un ID, saisissez le nom d'un compte Office 365 initial, le nom de votre société fictive, puis un mot de passe. Enregistrez l’adresse de messagerie (par exemple, user123@contoso123.onmicrosoft.com) et le mot de passe obtenus dans un emplacement sécurisé. Vous avez besoin de ces informations pour effectuer la configuration d’Azure AD Connect dans la phase 3.

Lorsque vous avez terminé, la page principale du portail Office 365 doit s’afficher. Dans le ruban supérieur, cliquez sur **Admin**, puis sur **Office 365**. La page Centre d'administration Office 365 s'affiche. Gardez cette page ouverte sur CLIENT1.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph2.png)

## Phase 3 : configuration du serveur de synchronisation d’annuaires (DS1).

Dans le portail Azure, démarrez l’ordinateur DC2 si nécessaire.

Créez ensuite une machine virtuelle Azure pour DS1 avec les commandes suivantes à l'invite de commandes Azure PowerShell sur votre ordinateur local. Avant d'exécuter ces commandes, renseignez les valeurs des variables et supprimez les caractères < and >.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DS1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
	$vm=New-AzureRMVMConfig -VMName DS1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DS1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DS1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DS1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DS1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, utilisez le portail Azure pour vous connecter à la machine virtuelle DS1 avec les informations d’identification du compte d’administrateur local.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes de Windows PowerShell de niveau administrateur sur DS1, exécutez les commandes suivantes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc2.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 192.168.0.4.

Ensuite, joignez DS1 au domaine Active Directory CORP avec les commandes suivantes dans l’invite Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Utilisez le compte CORP\\User1 lorsque vous êtes invité à fournir vos informations d’identification du compte de domaine pour la commande **Add-Computer**.

Après le redémarrage, utilisez le portail Azure pour vous connecter à DS1 avec le compte et le mot de passe CORP\\User1.

Ensuite, installez .NET 3.5 sur DS1 avec cette commande à l'invite de commandes Windows PowerShell de niveau administrateur.

	Add-WindowsFeature NET-Framework-Core

Ensuite, activez la synchronisation d'annuaires pour votre version d'évaluation d'Office 365 FastTrack.

1.	Dans CLIENT1, dans la page **Centre d’administration Office 365**, dans le volet gauche, cliquez sur **Utilisateurs**, puis sur **Utilisateurs actifs**.
2.	Pour **Synchronisation Active Directory**, cliquez sur **Configurer**.
3.	Dans la page Configurer et gérer la synchronisation Active Directory, à l’étape 3, cliquez sur **Activer**.
4.	À l’invite **Voulez-vous activer la synchronisation Active Directory ?**, cliquez sur **Activer**. Après cela, **La synchronisation Active Directory est activée** s’affiche à l’étape 3.
5.	Laissez la page **Configurer et gérer la synchronisation Active Directory** ouverte sur CLIENT1.

Ensuite, dans l’invite Windows PowerShell sur DC1, exécutez ces commandes **une par une** pour créer une unité d’organisation appelée contoso\_users et ajouter deux nouveaux comptes d’utilisateur pour Camille Brunelle et Denise Bourgeois.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Lorsque vous exécutez chaque commande Windows PowerShell **New-ADUser**, le mot de passe du nouvel utilisateur vous est demandé. Enregistrez ces mots de passe et stockez-les dans un emplacement sécurisé. Vous en aurez besoin ultérieurement.

Ensuite, installez et configurez l'outil Azure AD Connect sur DS1.

1.	Exécutez Internet Explorer, saisissez **https://www.microsoft.com/download/details.aspx?id=47594** dans la barre d’**adresses**, puis appuyez sur Entrée.
2.	Lancez le programme d’installation de Microsoft Azure AD Connect.
3.	À partir du Bureau, double-cliquez sur **Azure AD Connect**.
4.	Dans la page de **bienvenue**, sélectionnez **J'accepte les termes du contrat de licence et la déclaration de confidentialité**, puis cliquez sur **Continuer**.
5.	Sur la page **Paramètres Express**, cliquez sur **Utiliser les paramètres Express**.
6.	Dans la page **Connexion à Azure AD**, saisissez l'adresse de messagerie et le mot de passe du compte initial que vous avez créé lorsque vous avez installé la version d'évaluation d'Office 365 FastTrack à la phase 2. Cliquez sur **Next**.
7.	Dans la page **Connexion à AD DS**, saisissez **CORP\\User1** dans **Nom d’utilisateur** et le mot de passe du compte User1 dans **Mot de passe**. Cliquez sur Suivant.
8.	Sur la page **Prêt à configurer**, passez en revue les paramètres, puis cliquez sur **Installer**.
9.	Sur la page **Configuration terminée**, cliquez sur **Quitter**.

Ensuite, vérifiez que les comptes d'utilisateur dans le domaine CORP sont synchronisés avec Office 365. Notez que la synchronisation peut prendre plusieurs minutes.

Sur CLIENT1, dans la page **Configurer et gérer la synchronisation Active Directory**, cliquez sur le lien **Utilisateurs** à l’étape 6 de cette page. Si la synchronisation d'annuaires s'est déroulée correctement, vous devez voir quelque chose de similaire à ce qui suit.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-example.png)

La colonne **État** indique que le compte a été obtenu via une synchronisation avec un domaine Active Directory.

Montrez ensuite la synchronisation de mot de passe d'Office 365 avec le compte Active Directory de Denise Bourgeois.

1.	Sur CLIENT1, dans la page **Utilisateurs actifs**, sélectionnez le compte **Denise Bourgeois**.
2.	Dans les propriétés du compte Denise Bourgeois, sous **Licence attribuée**, cliquez sur **Modifier**.
3.	Sous l’onglet **Affecter une licence**, dans **Définir l’emplacement de l’utilisateur**, sélectionnez un emplacement (par exemple, les États-Unis).
4.	Sélectionnez **Microsoft Office 365 - Plan E3**, puis cliquez sur **Enregistrer**.
5.	Fermez Internet Explorer.
6.	Exécutez Internet Explorer et accédez à **http://portal.microsoftonline.com**.
7.	Ouvrez une session avec les informations d'identification Office 365 de Denise Bourgeois. Son nom d’utilisateur sera deniseb@<*Votre nom fictif*>.onmicrosoft.com. Le mot de passe est le mot de passe du compte d'utilisateur Active Directory de Denise Bourgeois.
8.	Après l’ouverture de session réussie, vous voyez la page principale du portail d’Office 365 avec la mention **Faisons la différence aujourd’hui**.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync/virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync-ph3.png)
 
Cet environnement est maintenant prêt à exécuter le test des applications Office 365 qui s'appuient sur la fonctionnalité de synchronisation des annuaires d'Office 365 ou à tester la fonctionnalité de synchronisation d'annuaires et les performances de DS1.

## Étape suivante

- Déployez cette charge de travail [en production](http://technet.microsoft.com/library/dn635310.aspx).

<!---HONumber=AcomDC_0601_2016-->