<properties
	pageTitle="Environnement de test de configuration de base avec Azure Resource Manager"
	description="Découvrez comment créer un environnement de développement ou de test simple qui simule un intranet simplifié dans Microsoft Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="josephd"/>

# Environnement de test de la configuration de base

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

Cet article vous présente des instructions détaillées vous permettant de créer l’environnement de test de la configuration de base au sein d’un réseau virtuel Microsoft Azure, à l’aide de machines virtuelles créées dans le Gestionnaire de ressources.

Vous pouvez utiliser l’environnement de test obtenu :

- Pour le développement et le test d’applications.
- En tant que configuration initiale d'un environnement de test étendu personnalisé qui inclut des machines virtuelles et des services Azure supplémentaires.

L’environnement de test de la configuration de base se compose du sous-réseau Corpnet dans un réseau virtuel sur le cloud appelé TestLab, qui simule un intranet privé simplifié connecté à Internet.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph4.png)

Il contient :

- Une machine virtuelle Azure exécutant Windows Server 2012 R2, appelée DC1, configurée en tant que contrôleur de domaine intranet et serveur DNS.
- Une machine virtuelle Azure exécutant Windows Server 2012 R2, appelée APP1, configurée en tant que serveur d’applications et serveur Web.
- Une machine virtuelle Azure exécutant Windows Server 2012 R2 appelée CLIENT1, qui agit en tant que client intranet.

Cette configuration permet aux ordinateurs DC1, APP1, CLIENT1 et autres ordinateurs du sous-réseau Corpnet d’effectuer les opérations suivantes :

- Se connecter à Internet pour installer les mises à jour, accéder aux ressources Internet en temps réel et participer aux technologies de cloud public, comme Microsoft Office 365 et autres services Azure.
- Être gérés à distance à l’aide de connexions Bureau à distance à partir de votre ordinateur connecté à Internet ou au réseau de votre organisation.

La configuration du sous-réseau de réseau d’entreprise de l’environnement de test de la configuration de base de Windows Server 2012 R2 dans Azure comporte quatre phases.

1.	Création du réseau virtuel.
2.	Configuration de DC1
3.	Configuration d’APP1
4.	Configuration de CLIENT1

Si vous ne disposez pas déjà d’un compte Azure, vous pouvez obtenir un essai gratuit sur la page [Essayer Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN ou Visual Studio, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

> [AZURE.NOTE] Les machines virtuelles dans Azure entraînent des frais lors de leur utilisation. Ce coût est facturé sur votre abonnement de version d’évaluation gratuite, votre abonnement MSDN ou votre abonnement payant. Pour plus d’informations sur les coûts des machines virtuelles Azure, consultez les pages [Détails de la tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) et [Calculatrice de tarification Azure](https://azure.microsoft.com/pricing/calculator/). Afin de réduire les coûts, consultez la page [Réduction des coûts des machines virtuelles de l’environnement de test dans Azure](#costs).

## Phase 1 : création du réseau virtuel

Démarrez d'abord une invite de commandes Azure PowerShell.

> [AZURE.NOTE] Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Connectez-vous à votre compte.

	Login-AzureRMAccount

Obtenez votre nom d'abonnement à l'aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Ensuite, créez un nouveau groupe de ressources pour votre laboratoire de test de configuration de base. Pour déterminer un nom de groupe de ressources unique, utilisez cette commande pour répertorier vos groupes de ressources existants.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Créez votre nouveau groupe de ressources avec ces commandes. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Les machines virtuelles basées sur Resource Manager requièrent un compte de stockage basé sur Resource Manager. Vous devez choisir pour votre compte de stockage un nom global unique contenant seulement des chiffres et des lettres minuscules. Vous pouvez utiliser cette commande pour répertorier les comptes de stockage existants.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Créez un compte de stockage pour votre nouvel environnement de test avec ces commandes.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<storage account name>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Puis, créez le réseau virtuel Azure TestLab qui hébergera le sous-réseau Corpnet de la configuration de base et protégez-le avec un groupe de sécurité réseau.

	$rgName="<name of your new resource group>"
	$locName="<Azure location name, such as West US>"
	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$corpnetSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name Corpnet -AddressPrefix 10.0.0.0/24
	New-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/8 -Subnet $corpnetSubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name Corpnet -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	$nsg=Get-AzureRMNetworkSecurityGroup -Name Corpnet -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name Corpnet -AddressPrefix "10.0.0.0/24" -NetworkSecurityGroup $nsg

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph1.png)

## Phase 2 : configuration de DC1

DC1 est un contrôleur de domaine du domaine de Services de domaine Active Directory (AD DS) corp.contoso.com et un serveur DNS pour les machines virtuelles du réseau virtuel TestLab.

Commencez par entrer le nom de votre groupe de ressources, de votre emplacement Azure et de votre compte de stockage et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour DC1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name DC1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 10.0.0.4
	$vm=New-AzureRMVMConfig -VMName DC1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, connectez-vous à la machine virtuelle DC1.

1.	Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sur la machine virtuelle **DC1**.  
2.	Dans le volet **DC1**, cliquez sur **Se connecter**.
3.	Lorsque vous y êtes invité, ouvrez le fichier DC1.rdp téléchargé.
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

Notez que ces commandes peuvent prendre quelques minutes.

Après le redémarrage de DC1, reconnectez-vous à la machine virtuelle DC1.

1.	Dans le portail Azure, cliquez sur **Machines virtuelles**, puis sur la machine virtuelle **DC1**.
2.	Dans le volet **DC1**, cliquez sur **Se connecter**.
3.	Lorsque vous êtes invité à ouvrir DC1.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	À l'invite vous demandant des informations d'identification, utilisez ce qui suit :
- Nom : **CORP\**[Nom de compte d’administrateur local]
- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, créez un compte utilisateur dans Active Directory qui sera utilisé pour la connexion aux ordinateurs membres du domaine CORP. Exécutez cette commande à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADUser -SamAccountName User1 -AccountPassword (read-host "Set user password" -assecurestring) -name "User1" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

Notez que cette commande vous invite à fournir le mot de passe du compte User1. Étant donné que ce compte sera utilisé pour les connexions Bureau à distance pour tous les ordinateurs membres de domaine CORP, *choisissez un mot de passe fort*. Pour en vérifier la force, consultez la page [Password Checker : Utilisation de mots de passe forts](https://www.microsoft.com/security/pc-security/password-checker.aspx). Notez le mot de passe du compte User1 et stockez-le dans un emplacement sécurisé.

Ensuite, configurez le nouveau compte User1 en tant qu'administrateur d'entreprise. Exécutez cette commande à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	Add-ADPrincipalGroupMembership -Identity "CN=User1,CN=Users,DC=corp,DC=contoso,DC=com" -MemberOf "CN=Enterprise Admins,CN=Users,DC=corp,DC=contoso,DC=com","CN=Domain Admins,CN=Users,DC=corp,DC=contoso,DC=com"

Fermez la session Bureau à distance avec DC1 et reconnectez-vous en utilisant le compte CORP\\User1.

Ensuite, pour autoriser le trafic de l’utilitaire Ping, exécutez cette commande à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph2.png)

## Phase 3 : configuration d’APP1

App1 fournit les service web et de partage de fichiers.

Commencez par entrer le nom de votre groupe de ressources, de votre emplacement Azure et de votre compte de stockage et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour APP1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name APP1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName APP1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for APP1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName APP1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/APP1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name APP1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, connectez-vous à la machine virtuelle APP1 en utilisant le nom et le mot de passe de compte d’administrateur local APP1, puis ouvrez une invite de commandes Windows PowerShell de niveau administrateur.

Pour vérifier la résolution des noms et la communication réseau entre APP1 et DC1, exécutez la commande **ping dc1.corp.contoso.com** et vérifiez qu’il y a bien quatre réponses.

Ensuite, joignez la machine virtuelle APP1 au domaine CORP avec les commandes suivantes dans l'invite Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Notez que vous devez fournir vos informations d'identification du compte de domaine CORP\\User1 après avoir saisi la commande Add-Computer.

Après son redémarrage, connectez-vous à la machine virtuelle APP1 en utilisant le nom de compte et le mot de passe de CORP\\User1, puis ouvrez une invite de commandes Windows PowerShell de niveau administrateur.

Ensuite, faites d’APP1 un serveur web avec cette commande à l’invite de commandes Windows PowerShell.

	Install-WindowsFeature Web-WebServer –IncludeManagementTools

Puis créez un dossier partagé et un fichier texte dans ce dossier, sur APP1, avec ces commandes.

	New-Item -path c:\files -type directory
	Write-Output "This is a shared file." | out-file c:\files\example.txt
	New-SmbShare -name files -path c:\files -changeaccess CORP\User1

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph3.png)

## Phase 4 : configuration de CLIENT1

CLIENT1 agit comme un ordinateur portable, une tablette ou un ordinateur de bureau classique sur l’intranet de Contoso.

> [AZURE.NOTE] La commande suivante crée CLIENT1 exécutant Windows Server 2012 R2 Datacenter, opération réalisable pour tous les types d’abonnements Azure. Si vous avez un abonnement Azure MSDN, vous pouvez créer CLIENT1 exécutant Windows 10, Windows 8 ou Windows 7 à l’aide du [portail Azure](virtual-machines-windows-hero-tutorial.md).

Commencez par entrer le nom de votre groupe de ressources, de votre emplacement Azure et de votre compte de stockage et exécutez les commandes suivantes à partir de l’invite de commandes Azure PowerShell sur votre ordinateur local pour créer une machine virtuelle Azure pour CLIENT1.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$pip = New-AzureRMPublicIpAddress -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRMNetworkInterface -Name CLIENT1-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$vm=New-AzureRMVMConfig -VMName CLIENT1 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for CLIENT1."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName CLIENT1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/CLIENT1-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name CLIENT1-TestLab-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, connectez-vous à la machine virtuelle CLIENT1 en utilisant le nom et le mot de passe de compte d’administrateur local CLIENT1, puis ouvrez une invite de commandes Windows PowerShell de niveau administrateur.

Pour vérifier la résolution des noms et la communication réseau entre CLIENT1 et DC1, exécutez la commande **ping dc1.corp.contoso.com** à partir d’une invite de commandes Windows PowerShell et vérifiez qu’il y a bien quatre réponses.

Ensuite, joignez la machine virtuelle CLIENT1 au domaine CORP avec les commandes suivantes dans l'invite Windows PowerShell.

	Add-Computer -DomainName corp.contoso.com
	Restart-Computer

Notez que vous devez fournir vos informations d'identification du compte de domaine CORP\\User1 après avoir saisi la commande Add-Computer.

Après son redémarrage, connectez-vous à la machine virtuelle CLIENT1 en utilisant le nom de compte et le mot de passe de CORP\\User1, puis ouvrez une invite de commandes Windows PowerShell de niveau administrateur.

Ensuite, vérifiez que vous pouvez accéder au web et aux ressources de partage de fichiers sur APP1 à partir de CLIENT1.

1.	Dans le Gestionnaire de serveur, dans le volet d’arborescence, cliquez sur **Serveur local**.
2.	Dans **Propriétés de CLIENT1**, cliquez sur **Activée** en regard de **Configuration de sécurité renforcée d’Internet Explorer**.
3.	Dans **Configuration de sécurité renforcée d’Internet Explorer**, cliquez sur **Désactivée** pour **Administrateurs** et **Utilisateurs**, puis cliquez sur **OK**.
4.	Dans l’écran d’accueil, cliquez sur **Internet Explorer**, puis cliquez sur **OK**.
5.	Dans la barre d’adresses, tapez **http://app1.corp.contoso.com/**, puis appuyez sur Entrée. Vous devez voir la page web Internet Information Services par défaut pour APP1.
6.	Dans la barre des tâches, cliquez sur l’icône de l’Explorateur de fichiers.
7.	Dans la barre d’adresse, **\\\app1\\Files**, puis appuyez sur Entrée.
8.	Vous devez voir une fenêtre de dossier avec le contenu du dossier partagé Files.
9.	Dans la fenêtre **Files** partagée, double-cliquez sur le fichier **example.txt**. Vous devez voir le contenu du fichier Example.txt.
10.	Fermez **Example.txt - Bloc-notes** et la fenêtre du dossier partagé **Files**.

Ceci est votre configuration finale.

![](./media/virtual-machines-windows-test-config-env/virtual-machines-windows-test-config-env-ph4.png)

Votre configuration de base dans Azure est maintenant prête pour le développement et le test d’applications ou pour d’autres environnements de test.

## Étapes suivantes

- Ajoutez une machine virtuelle à l’aide du [portail Azure](virtual-machines-windows-hero-tutorial.md).
- Concevez la [simulation d’environnement de test de cloud hybride](virtual-machines-setup-simulated-hybrid-cloud-environment-testing.md).


## <a id="costs"></a>Réduction des coûts des machines virtuelles de l’environnement de test dans Azure

Pour réduire le coût des machines virtuelles de l’environnement de test, vous pouvez effectuer une des actions suivantes :

- Créez l’environnement de test et exécutez les tests et démonstrations nécessaires aussi rapidement que possible. Lorsque vous avez terminé, supprimez les machines virtuelles de l’environnement de test.
- Arrêtez les machines virtuelles de l’environnement de test en état désalloué.

Pour arrêter les machines virtuelles avec Azure PowerShell, indiquez le nom du groupe de ressources et exécutez ces commandes.

	$rgName="<your resource group name>"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Stop-AzureRMVM -ResourceGroupName $rgName -Name "DC1" -Force -StayProvisioned

Pour vous assurer que vos machines virtuelles fonctionnent correctement lorsque vous les démarrez toutes à partir de l’état Arrêtée (Désallouée), vous devez les démarrer dans l’ordre suivant :

1.	DC1
2.	APP1
3.	CLIENT1

Pour démarrer les machines virtuelles dans l’ordre avec Azure PowerShell, indiquez le nom du groupe de ressources et exécutez ces commandes.

	$rgName="<your resource group name>"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "DC1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "APP1"
	Start-AzureRMVM -ResourceGroupName $rgName -Name "CLIENT1"

<!---HONumber=AcomDC_0511_2016-->