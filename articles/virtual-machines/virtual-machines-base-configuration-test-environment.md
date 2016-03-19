<properties
	pageTitle="Environnement de test de la configuration de base"
	description="Découvrez comment créer un environnement de développement ou de test simple qui simule un intranet simplifié dans Microsoft Azure."
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
	ms.date="01/12/2016"
	ms.author="josephd"/>

# Environnement de test de la configuration de base

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-machines-base-configuration-test-environment-resource-manager.md).

Cet article vous présente des instructions détaillées vous permettant de créer l’environnement de test de la configuration de base au sein d’un réseau virtuel Azure.

Vous pouvez utiliser l’environnement de test obtenu :

- Pour le développement et le test d’applications.
- Pour la [simulation d’environnement de cloud hybride](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).
- Vous pouvez l’étendre avec d’autres machines virtuelles et services Azure pour personnaliser votre environnement de test.

L’environnement de test de la configuration de base se compose du sous-réseau Corpnet dans un réseau virtuel sur le cloud appelé TestLab, qui simule un intranet privé simplifié connecté à Internet.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Il contient :

- Une machine virtuelle Azure exécutant Windows Server 2012 R2, appelée DC1, configurée en tant que contrôleur de domaine intranet et serveur DNS.
- Une machine virtuelle Azure exécutant Windows Server 2012 R2, appelée APP1, configurée en tant que serveur d’applications et serveur web.
- Une machine virtuelle Azure exécutant Windows Server 2012 R2 appelée CLIENT1, qui agit en tant que client intranet.

Cette configuration permet aux ordinateurs DC1, APP1, CLIENT1 et autres ordinateurs du sous-réseau Corpnet d’effectuer les opérations suivantes :

- Se connecter à Internet pour installer les mises à jour, accéder aux ressources Internet en temps réel et participer aux technologies de cloud public, comme Microsoft Office 365 et autres services Azure.
- Être gérés à distance à l’aide de connexions Bureau à distance à partir de votre ordinateur connecté à Internet ou au réseau de votre organisation.

La configuration du sous-réseau de réseau d’entreprise de l’environnement de test de la configuration de base de Windows Server 2012 R2 dans Azure comporte quatre phases.

1.	Création du réseau virtuel.
2.	Configuration de DC1
3.	Configuration d’APP1
4.	Configuration de CLIENT1

Si vous ne disposez pas déjà d’un compte Azure, vous pouvez obtenir un essai gratuit dans la page [Évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/). Si vous souscrivez à un abonnement Plateformes MSDN, consultez [Avantage Azure pour les abonnés Plateformes MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/).

> [AZURE.NOTE] Les machines virtuelles dans Azure entraînent des frais lors de leur utilisation. Ce coût est facturé dans le cadre de votre essai gratuit, de votre abonnement Plateformes MSDN ou de votre abonnement payant. Pour plus d’informations sur les coûts des machines virtuelles Azure, consultez les pages [Détails de la tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) et [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/). Afin de réduire les coûts, consultez la page [Réduction des coûts des machines virtuelles de l’environnement de test dans Azure](#costs).

## Phase 1 : création du réseau virtuel

Tout d’abord, vous créez le réseau virtuel TestLab qui hébergera le sous-réseau Corpnet de la configuration de base.

1.	Dans la barre des tâches du [portail Azure Classic](https://manage.windowsazure.com), cliquez sur **Nouveau > Services réseau > Réseau virtuel > Création personnalisée**.
2.	Dans la page Détails du réseau virtuel, tapez **TestLab** dans **Nom**.
3.	Dans **Emplacement**, sélectionnez la région souhaitée.
4.	Cliquez sur la flèche Suivant.
5.	Dans la page Serveurs DNS et connectivité VPN, dans **Serveurs DNS**, tapez **DC1** dans **Sélectionner ou entrer le nom**, tapez **10.0.0.4** dans **Adresse IP**, puis cliquez sur la flèche Suivant.
6.	Dans la page Espaces d’adressage de réseau virtuel, dans **Sous-réseaux**, cliquez sur **Subnet-1** et remplacez le nom par **Corpnet**.
7.	Dans la colonne **CIDR (nombre d’adresses)** pour Corpnet, cliquez sur **/24 (256)**.
8.	Cliquez sur l’icône Terminer. Attendez que le réseau virtuel soit créé avant de continuer.

Ensuite, suivez les instructions de la page [Installation et configuration d’Azure PowerShell](../install-configure-powershell.md) pour installer Azure PowerShell sur votre ordinateur local. Ouvrez une invite de commandes Azure PowerShell.

Tout d’abord, sélectionnez l’abonnement Azure approprié avec ces commandes. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<Subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Le nom de l’abonnement apparaît dans la propriété **SubscriptionName** du résultat de la commande **Get-AzureSubscription**.

Ensuite, vous créez un service cloud Azure. Le service cloud agit comme une limite de sécurité et un conteneur logique pour les machines virtuelles placées dans le réseau virtuel. Il permet également de vous connecter à distance et de gérer les machines virtuelles sur le sous-réseau Corpnet.

Vous devez choisir un nom unique pour votre service cloud. *Le nom de service cloud ne peut contenir que des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère du champ doivent être une lettre ou un chiffre.*

Par exemple, vous pouvez nommer votre service cloud TestLab-*UniqueSequence*, où *UniqueSequence* est l’abréviation de votre entreprise. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez appeler le service cloud TestLab-Tailspin.

Vous pouvez vérifier si le nom est unique avec cette commande Azure PowerShell.

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Ensuite, créez le service cloud avec ces commandes.

	$loc="<the same location (region) as your TestLab virtual network>"
	New-AzureService -Service <Unique cloud service name> -Location $loc

Notez le nom de votre service cloud.

Ensuite, vous configurez un compte de stockage contenant les disques de vos machines virtuelles et les disques de données supplémentaires. *Vous devez choisir un nom unique qui contient uniquement des chiffres et des lettres en minuscules.* Vous pouvez vérifier si le nom du compte de stockage est unique avec cette commande Azure PowerShell.

	Test-AzureName -Storage <Proposed storage account name>

Si cette commande renvoie « False », le nom proposé est unique. Ensuite, créez le compte de stockage et définissez l’abonnement à utiliser avec ces commandes.

	$stAccount="<your storage account name>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $loc
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $stAccount

Ceci est votre configuration actuelle.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG01.png)

## Phase 2 : configuration de DC1

DC1 est un contrôleur de domaine du domaine de Services de domaine Active Directory (AD DS) corp.contoso.com et un serveur DNS pour les machines virtuelles du réseau virtuel TestLab.

Commencez par entrer le nom de votre service cloud et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour DC1.

	$serviceName="<your cloud service name>"
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC1."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel "AD" -LUN 0 -HostCaching None
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Ensuite, connectez-vous à la machine virtuelle DC1.

1.	Dans le portail Azure Classic, cliquez sur **Machines virtuelles** dans le volet de gauche, puis cliquez sur **Démarrée** dans la colonne **État** de la machine virtuelle DC1.  
2.	Dans la barre des tâches, cliquez sur **Se connecter**.
3.	Lorsque vous êtes invité à ouvrir DC1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	À l'invite vous demandant des informations d'identification, utilisez ce qui suit :
- Nom : **DC1\**[Nom de compte d’administrateur local]
- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, ajoutez le disque de données supplémentaire en tant que nouveau volume avec la lettre de lecteur F:.

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

Ensuite, configurez DC1 comme contrôleur de domaine et serveur DNS pour le domaine corp.contoso.com. Exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSForest -DomainName corp.contoso.com -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

Après le redémarrage de DC1, reconnectez-vous à la machine virtuelle DC1.

1.	Sur la page des machines virtuelles du portail Azure Classic, cliquez sur **En cours d'exécution** dans la colonne **État** de la machine virtuelle DC1.
2.	Dans la barre des tâches, cliquez sur **Se connecter**.
3.	Lorsque vous êtes invité à ouvrir DC1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	À l'invite vous demandant des informations d'identification, utilisez ce qui suit :
- Nom : **CORP\**[Nom de compte d’administrateur local]
- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, créez un compte utilisateur dans Active Directory qui sera utilisé pour la connexion aux ordinateurs membres du domaine CORP. Exécutez les commandes suivantes une par une à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false
	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Notez que les premiers résultats de la commande vous invitent à fournir le mot de passe du compte utilisateur1. Étant donné que ce compte sera utilisé pour les connexions Bureau à distance pour tous les ordinateurs membres de domaine CORP, choisissez un mot de passe fort. Pour en vérifier la force, consultez la page [Password Checker : Utilisation de mots de passe forts](https://www.microsoft.com/security/pc-security/password-checker.aspx). Notez le mot de passe du compte User1 et stockez-le dans un emplacement sécurisé.

Reconnectez-vous à la machine virtuelle DC1 à l’aide du compte CORP\\User1.

Ensuite, pour autoriser le trafic de l’utilitaire Ping, exécutez cette commande à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Ceci est votre configuration actuelle.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG02.png)

## Phase 3 : configuration d’APP1

App1 fournit les service web et de partage de fichiers.

Commencez par entrer le nom de votre service cloud et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour APP1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for APP1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name APP1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Ensuite, connectez-vous à la machine virtuelle APP1 en utilisant les informations d’identification CORP\\User1 et ouvrez une invite de commandes Windows PowerShell de niveau administrateur.

Pour vérifier la résolution des noms et la communication réseau entre APP1 et DC1, exécutez la commande **ping dc1.corp.contoso.com** et vérifiez qu’il y a bien quatre réponses.

Ensuite, faites d’APP1 un serveur web avec cette commande à l’invite de commandes Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Puis créez un dossier partagé et un fichier texte dans ce dossier, sur APP1, avec ces commandes.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Ceci est votre configuration actuelle.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG03.png)

## Phase 4 : configuration de CLIENT1

CLIENT1 agit comme un ordinateur portable, une tablette ou un ordinateur de bureau classique sur l’intranet de Contoso.

Commencez par entrer le nom de votre service cloud et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour CLIENT1.

	$serviceName="<your cloud service name>"
	$cred1=Get-Credential –Message "Type the name and password of the local administrator account for CLIENT1."
	$cred2=Get-Credential –UserName "CORP\User1" –Message "Now type the password for the CORP\User1 account."
	$image= Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name CLIENT1 -InstanceSize Small -ImageName $image
	$vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.GetNetworkCredential().Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain "CORP" -DomainUserName "User1" -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain "corp.contoso.com"
	$vm1 | Set-AzureSubnet -SubnetNames Corpnet
	New-AzureVM –ServiceName $serviceName -VMs $vm1 -VNetName TestLab

Ensuite, connectez-vous à la machine virtuelle CLIENT1 avec les informations d’identification CORP\\User1.

Pour vérifier la résolution des noms et la communication réseau entre CLIENT1 et DC1, exécutez la commande **ping dc1.corp.contoso.com** à partir d’une invite de commandes Windows PowerShell et vérifiez qu’il y a bien quatre réponses.

Ensuite, vérifiez que vous pouvez accéder au web et aux ressources de partage de fichiers sur APP1 à partir de CLIENT1.

1.	Dans le Gestionnaire de serveur, dans le volet d’arborescence, cliquez sur **Serveur local**.
2.	Dans **Propriétés de CLIENT1**, cliquez sur **Activée** en regard de **Configuration de sécurité renforcée d’Internet Explorer**.
3.	Dans **Configuration de sécurité renforcée d’Internet Explorer**, cliquez sur **Désactivée** pour **Administrateurs** et **Utilisateurs**, puis cliquez sur **OK**.
4.	Dans l’écran d’accueil, cliquez sur **Internet Explorer**, puis cliquez sur **OK**.
5.	Dans la barre d’adresses, tapez ****http://app1.corp.contoso.com/**, puis appuyez sur Entrée. Vous devez voir la page web Internet Information Services par défaut pour APP1.
6.	Dans la barre des tâches, cliquez sur l’icône de l’Explorateur de fichiers.
7.	Dans la barre d’adresse, **\\\app1\\Files**, puis appuyez sur Entrée.
8.	Vous devez voir une fenêtre de dossier avec le contenu du dossier partagé Files.
9.	Dans la fenêtre **Files** partagée, double-cliquez sur le fichier **example.txt**. Vous devez voir le contenu du fichier Example.txt.
10.	Fermez **Example.txt - Bloc-notes** et la fenêtre du dossier partagé **Files**.

Ceci est votre configuration finale.

![](./media/virtual-machines-base-configuration-test-environment/BC_TLG04.png)

Votre configuration de base dans Azure est maintenant prête pour le développement et le test d’applications ou pour d’autres environnements de test.

## Étape suivante

- Configurez l’[environnement cloud hybride simulé](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md) pour tester les configurations hybrides.

## <a id="costs"></a>Réduction des coûts des machines virtuelles de l’environnement de test dans Azure

Pour réduire le coût des machines virtuelles de l’environnement de test, vous pouvez effectuer une des actions suivantes :

- Créez l’environnement de test et exécutez les tests et démonstrations nécessaires aussi rapidement que possible. Lorsque vous avez terminé, supprimez les machines virtuelles de l’environnement de test.
- Arrêtez les machines virtuelles de l’environnement de test en état désalloué.

Pour arrêter les machines virtuelles avec Azure PowerShell, indiquez le nom du service cloud et exécutez ces commandes.

	$serviceName="<your cloud service name>"
	Stop-AzureVM -ServiceName $serviceName -Name "CLIENT1"
	Stop-AzureVM -ServiceName $serviceName -Name "APP1"
	Stop-AzureVM -ServiceName $serviceName -Name "DC1" -Force -StayProvisioned


Pour vous assurer que vos machines virtuelles fonctionnent correctement lorsque vous les démarrez toutes à partir de l’état Arrêtée (Désallouée), vous devez les démarrer dans l’ordre suivant :

1.	DC1
2.	APP1
3.	CLIENT1

Pour démarrer les machines virtuelles dans l’ordre avec Azure PowerShell, indiquez le nom du service cloud et exécutez ces commandes.

	$serviceName="<your cloud service name>"
	Start-AzureVM -ServiceName $serviceName -Name "DC1"
	Start-AzureVM -ServiceName $serviceName -Name "APP1"
	Start-AzureVM -ServiceName $serviceName -Name "CLIENT1"

<!---HONumber=AcomDC_0128_2016-->