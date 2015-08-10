<properties 
	pageTitle="Configuration de la synchronisation d'annuaires Office 365 (DirSync) dans un cloud hybride pour le test" 
	description="Apprenez à configurer un serveur de synchronisation d'annuaires (DirSync) Office 365 dans un cloud hybride pour les professionnels de l'informatique ou le test des développements." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Configuration de la synchronisation d'annuaires Office 365 (DirSync) dans un cloud hybride pour le test

Cette rubrique vous guide lors de la création d'un environnement de cloud hybride pour le test de la synchronisation d'annuaires (DirSync) Office 365 avec la synchronisation de mot de passe hébergée dans Microsoft Azure. Voici la configuration obtenue.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Cette configuration simule un serveur de synchronisation d'annuaires dans un environnement de production Azure à partir de votre emplacement sur Internet. Elle comprend :

- Un réseau local simplifié (sous-réseau de réseau d'entreprise).
- Un réseau virtuel intersite hébergé dans Azure (TestVNET).
- Une connexion VPN de site à site.
- Un abonnement d'évaluation à Office 365 FastTrack.
- Un serveur de synchronisation d'annuaires et le contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir duquel vous pouvez :

- Développer et tester des applications pour Office 365 qui reposent sur la synchronisation avec un domaine Active Directory local à l'aide de la synchronisation de mot de passe.
- Effectuer les tests de cette charge de travail informatique basée sur le cloud.

Il existe trois principales étapes pour configurer cet environnement de test de cloud hybride :

1.	Configurer l’environnement de cloud hybride pour le test.
2.	Configurer la version d'évaluation d'Office 365 FastTrack.
3.	Configurer le serveur de synchronisation d'annuaires (DS1).

Si vous ne disposez pas déjà d'un abonnement Azure, vous pouvez obtenir une évaluation gratuite sur la page [Essai d'Azure](http://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## Phase 1 : configuration de l’environnement de cloud hybride

Suivez les instructions de la rubrique [Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md). Étant donné que cet environnement de test ne nécessite pas la présence du serveur APP1 sur le sous-réseau de réseau d’entreprise, n’hésitez pas à l’arrêter pour le moment.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_1.png)

> [AZURE.NOTE]Pour la phase 1, vous pouvez également configurer la simulation d’environnement de test de cloud hybride. Suivez les instructions de la rubrique [Configuration d’une simulation d’environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

## Phase 2: configuration de la version d’évaluation d’Office 365 FastTrack

Pour démarrer votre version d'évaluation d'Office 365 FastTrack, vous avez besoin d'un nom de société fictif et d'un compte Microsoft. Nous vous recommandons d'utiliser une variante du nom de société Contoso pour le nom de votre société, qui est une société fictive utilisée dans l'exemple de contenu de Microsoft, mais ce n'est pas requis.

Ensuite, inscrivez-vous à un compte Microsoft. Accédez à **http://outlook.com** et créez un compte avec une adresse de messagerie comme user123@outlook.com. Vous vous inscrirez à une version d’évaluation d’Office 365 FastTrack à l’aide de ce compte.

Ensuite, inscrivez-vous à une nouvelle version d'évaluation d'Office 365 FastTrack.

1.	Connectez-vous à CLIENT1 avec les informations d'identification du compte CORP\\User1.
2.	Ouvrez Internet Explorer et accédez à **http://fasttrack.office.com**. 3.	Cliquez sur **Mise en route de FastTrack**.
4.	Sur la page de mise en route de FastTrack, sous **Tout d’abord, inscrivez-vous pour une version d’évaluation d’Office 365**, cliquez sur **Pour les entreprises, inscrivez-vous ici**.
5.	Sur la page de l’étape 1, renseignez la page, en spécifiant votre nouveau compte Microsoft dans **Adresse de messagerie professionnelle**, puis cliquez sur **Suivant**.
6.	Sur la page de l'étape 2, tapez le nom d'un compte Office 365 initial dans le premier champ, le nom de votre société fictive, puis un mot de passe. Enregistrez l’adresse de messagerie obtenue (par exemple, user123@contoso123.onmicrosoft.com) et le mot de passe dans un emplacement sécurisé. Vous aurez besoin de ces informations pour terminer l'Assistant de configuration de l'outil de synchronisation Active Directory à la phase 3. Cliquez sur **Next**.
7.	Dans la page de l’étape 3, tapez le numéro de votre téléphone mobile ou de votre smartphone doté de la fonction de message texte, puis cliquez sur **Me texter**.
8.	Après avoir reçu le message texte sur votre téléphone, tapez le code de vérification, puis cliquez sur **Créer mon compte**. 
9.	Lorsqu’Office 365 a terminé la création de votre compte, cliquez sur **Vous êtes prêt**.
10.	Vous devez maintenant voir la page principale du portail Office 365. Dans le ruban supérieur, cliquez sur **Admin**, puis cliquez sur **Office 365**. La page Centre d'administration Office 365 s'affiche. Gardez cette page ouverte sur CLIENT1.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_2.png)

## Phase 3 : configuration du serveur de synchronisation d’annuaires (DS1).

Commencez par créer une machine virtuelle Azure pour DS1 avec les commandes suivantes à l'invite de commandes Azure PowerShell sur votre ordinateur local. Avant d'exécuter ces commandes, renseignez les valeurs des variables et supprimez les caractères < and >.

	$ServiceName="<The cloud service name for your TestVNET virtual network>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for DS1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DS1 -InstanceSize Medium -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET

Ensuite, connectez-vous à la machine virtuelle DS1.

1.	Sur la page des machines virtuelles du portail de gestion Azure, cliquez sur **En cours d’exécution** dans la colonne État de la machine virtuelle DS1.
2.	Dans la barre des tâches, cliquez sur **Se connecter**. 
3.	Lorsque vous êtes invité à ouvrir DS1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	Si des informations d’identification vous sont demandées, utilisez celles-ci :
	- Nom : **CORP\\User1**
	- Mot de passe : [mot de passe du compte User1]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes de Windows PowerShell de niveau administrateur sur DS1, exécutez les commandes suivantes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit obtenir quatre réponses correctes à partir de l’adresse IP 10.0.0.1.

Ensuite, installez .NET 3.5 sur DS1 avec cette commande à l'invite de commandes Windows PowerShell.

	Add-WindowsFeature NET-Framework-Core

Ensuite, installez la synchronisation d'annuaires sur DS1.

1.	Exécutez Internet Explorer, tapez **http://go.microsoft.com/fwlink/?LinkID=278924** dans la barre d’adresses, puis appuyez sur ENTRÉE. Lorsque vous êtes invité à exécuter dirsync.exe, cliquez sur la flèche en regard de **Enregistrer**, cliquez sur **Enregistrer sous**, puis cliquez sur **Enregistrer** pour enregistrer le fichier dans le dossier Téléchargements. Pour plus d’informations sur l’installation de l’outil, consultez la page [Installation ou mise à niveau de l’outil de synchronisation d’annuaires](http://technet.microsoft.com/library/jj151800). 2.	Ouvrez le dossier **Téléchargements**, cliquez avec le bouton droit sur le fichier **dirsync**, puis cliquez sur **Exécuter en tant qu’administrateur**.
3.	Dans la page Bienvenue de l’Assistant Installation de la synchronisation Active Directory, cliquez sur **Suivant**. 
4.	Sur la page Termes du contrat de licence, cliquez sur **J’accepte**, puis cliquez sur **Suivant**.
5.	Dans la page Sélectionner une installation de dossier, cliquez sur **Suivant**. Cela peut prendre plusieurs minutes pour que l'installation se termine.
6.	Dans la page Terminé, désactivez **Démarrer l’Assistant Configuration maintenant**, puis cliquez sur **Terminer**.
7.	Dans l’écran d’accueil, cliquez sur **user1**, puis cliquez sur **Se déconnecter**.

Ensuite, activez la synchronisation d'annuaires pour votre version d'évaluation d'Office 365 FastTrack.

1.	Sur CLIENT1, sur la page **Centre d’administration Office 365**, dans le volet gauche, cliquez sur **Utilisateurs**, puis cliquez sur **Utilisateurs actifs**.
2.	Pour **Synchronisation Active Directory**, cliquez sur **Configurer**.
3.	Sur la page Configurer et gérer la synchronisation Active Directory, à l’étape 3, cliquez sur **Activer**.
4.	Lorsque l’invite **Voulez-vous activer la synchronisation Active Directory ?** s’ouvre, cliquez sur **Activer**. Après cela, **La synchronisation Active Directory est activée** s’affiche à l’étape 3.
5.	Laissez la page **Configurer et gérer la synchronisation Active Directory** ouverte sur CLIENT1.

Ensuite, ouvrez une session DC1 avec le compte CORP\\User1 et ouvrez une invite de commandes Windows PowerShell au niveau administrateur. Exécutez ces commandes une par une pour créer une nouvelle unité d’organisation appelée contoso\_users et ajouter deux nouveaux comptes d’utilisateur pour Camille Brunelle et Denise Bourgeois.

	New-ADOrganizationalUnit -Name contoso_users -Path "DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName marcik -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Marci Kaufman" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"
	New-ADUser -SamAccountName lyndam -AccountPassword (Read-Host "Set user password" -AsSecureString) -name "Lynda Meyer" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Path "OU=contoso_users,DC=corp,DC=contoso,DC=com"

Lorsque vous exécutez chaque commande Windows PowerShell, le nouveau mot de passe vous est demandé. Enregistrez ces mots de passe et stockez-les dans un emplacement sécurisé. Vous en aurez besoin ultérieurement.

Ensuite, installez la synchronisation d'annuaires sur DS1.

1.	Connectez-vous à DS1 avec le compte CORP\\User1.
2.	Sur l’écran d’**accueil**, tapez **Synchronisation d’annuaires**.
3.	Cliquez avec le bouton droit sur **Configuration de la synchronisation d’annuaires**, puis cliquez sur **Exécuter en tant qu’administrateur**. Ainsi, l'Assistant de configuration démarre.
4.	Sur la page d'accueil, cliquez sur **Suivant**.
5.	Dans la page Informations d’identification Microsoft Azure Active Directory, tapez l’adresse de messagerie et le mot de passe du compte initial que vous avez créé lorsque vous avez installé la version d’évaluation d’Office 365 FastTrack à la phase 2. Cliquez sur Suivant. 
6.	Dans la page Informations d’identification Active Directory, tapez **CORP\\User1** dans **Nom d’utilisateur** et le mot de passe du compte User1 dans **Mot de passe**. Cliquez sur **Next**.
7.	Sur la page Déploiement hybride, sélectionnez **Activer le déploiement hybride**, puis cliquez sur **Suivant**.
8.	Sur la page Synchronisation de mot de passe, sélectionnez **Activer la synchronisation des mots de passe**, puis cliquez sur **Suivant**.
9.	La page Configuration s'affiche. Lorsque la configuration est terminée, cliquez sur **Suivant**.
10.	Dans la page Terminé, cliquez sur **Terminer**. À l’invite, cliquez sur **OK**.

Ensuite, vérifiez que les comptes d'utilisateur dans le domaine CORP sont synchronisés avec Office 365. Notez que la synchronisation peut prendre plusieurs heures.

Sur CLIENT1, sur la page **Configurer et gérer la synchronisation Active Directory**, cliquez sur le lien **utilisateurs** à l’étape 6 de cette page. Si la synchronisation d'annuaires s'est déroulée correctement, vous devez voir quelque chose de similaire à ce qui suit.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_4.png)

La colonne **État** indique que le compte a été obtenu via la synchronisation avec un domaine Active Directory.

Montrez ensuite la synchronisation de mot de passe d'Office 365 avec le compte Active Directory de Denise Bourgeois.

1.	Sur CLIENT1, sur la page **Utilisateurs actifs**, sélectionnez le compte **Denise Bourgeois**.
2.	Dans les propriétés du compte Denise Bourgeois, sous **Licence attribuée**, cliquez sur **Modifier**.
3.	Sous l’onglet **Affecter une licence**, sélectionnez un emplacement dans **Définir l’emplacement de l’utilisateur** (tel que les États-Unis).
4.	Sélectionnez **Microsoft Office 365 - Plan E3**, puis cliquez sur **Enregistrer**.
5.	Fermez Internet Explorer.
6.	Exécutez Internet Explorer et accédez à **http://portal.microsoftonline.com**. 7.	Ouvrez une session avec les informations d'identification Office 365 de Denise Bourgeois. Son nom d’utilisateur sera denise@<\*Votre nom fictif\*>.onmicrosoft.com. Le mot de passe est le mot de passe du compte d'utilisateur Active Directory de Denise Bourgeois.
8.	Après l’ouverture de session réussie, vous voyez la page principale du portail d’Office 365 avec la mention **Faisons la différence aujourd’hui**.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-dirsync-hybrid-cloud-testing/CreateDirSyncHybridCloud_3.png)
 
Cet environnement est maintenant prêt à exécuter le test des applications Office 365 qui s'appuient sur la fonctionnalité de synchronisation des annuaires d'Office 365 ou à tester la fonctionnalité de synchronisation d'annuaires et les performances de DS1.

## Ressources supplémentaires

[Déploiement de la synchronisation d’annuaires (DirSync) d’Office 365 dans Microsoft Azure](http://technet.microsoft.com/library/dn635310.aspx)

[Solutions utilisant Office Server et le cloud](http://technet.microsoft.com/library/dn262744.aspx)

[Configuration d’un environnement de cloud hybride à des fins de test](virtual-networks-setup-hybrid-cloud-environment-testing.md)

[Configuration d’une batterie de serveurs SharePoint intranet dans un cloud hybride à des fins de test](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configuration d’une application métier web dans un cloud hybride à des fins de test](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configuration d’une simulation d’environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Environnements de test de cloud hybride Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Instructions d’implémentation des services d’infrastructure Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)


 

<!---HONumber=July15_HO5-->