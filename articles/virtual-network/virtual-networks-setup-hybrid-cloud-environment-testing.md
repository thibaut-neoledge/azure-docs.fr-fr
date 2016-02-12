<properties 
	pageTitle="Environnement de test de cloud hybride | Microsoft Azure" 
	description="Apprenez à créer un environnement de cloud hybride complet avec un réseau local simplifié pour les professionnels de l'informatique ou le test des développements." 
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
	ms.date="01/28/2016" 
	ms.author="josephd"/>

# Configuration d’un environnement de cloud hybride pour le test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modèle Resource Manager
 

Cette rubrique vous guide lors de la création d'un environnement de cloud hybride avec Microsoft Azure pour le test. Voici la configuration obtenue.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

Cette configuration simule un environnement réel de production hybride à partir de votre emplacement sur Internet. Elle comprend :

-  Un réseau local simplifié (sous-réseau de réseau d'entreprise).
-  Un réseau virtuel intersite hébergé dans Azure (TestVNET).
-  Une connexion VPN de site à site.
-  Un contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Cette configuration fournit une base et un point de départ commun à partir duquel vous pouvez :

-  Développer et tester des applications dans un environnement de cloud hybride.
-  Créer des configurations de test des ordinateurs, certains sur le sous-réseau de réseau d'entreprise et d'autres dans le réseau virtuel TestVNET, pour les charges de travail informatiques de cloud hybride.

Il existe cinq phases principales de configuration de cet environnement de test de cloud hybride :

1.	Configurer les ordinateurs sur le sous-réseau de réseau d'entreprise.
2.	Configurer RRAS1.
3.	Créer le réseau virtuel Azure intersite.
4.	Créer la connexion VPN de site à site
5.	Configurer DC2. 

Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour une version d’évaluation gratuite à partir de la page permettant d’[essayer Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN, consultez la page [Avantage Azure pour les abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Les machines virtuelles et les passerelles de réseau virtuel dans Azure entraînent des frais lors de leur utilisation. Ce coût est facturé sur votre abonnement de version d’évaluation gratuite, votre abonnement MSDN ou votre abonnement payant. Afin de réduire les coûts d’exécution de cet environnement de test quand vous ne l’utilisez pas, consultez la section [Réduction des coûts récurrents de cet environnement](#costs) plus loin dans cet article.

Cette configuration nécessite un sous-réseau de test comportant jusqu’à quatre ordinateurs connectés directement à Internet à l’aide d’une adresse IP publique. Si vous ne disposez pas de ces ressources, vous pouvez également [configurer une simulation d’environnement de cloud hybride à des fins de test](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md). L’environnement de test de cloud hybride simulé nécessite uniquement un abonnement Azure.

## Phase 1 : configurer les ordinateurs sur le sous-réseau de réseau d’entreprise

Suivez les instructions de la section décrivant la procédure de configuration du sous-réseau d’entreprise dans le [Guide de laboratoire de test : configuration de base pour Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) (en anglais) pour configurer les ordinateurs DC1, APP1 et CLIENT1 sur un sous-réseau nommé Corpnet. **Ce sous-réseau doit être isolé de votre réseau d’entreprise, car il est connecté directement à Internet par le biais de l’ordinateur RRAS1.**

Ensuite, connectez-vous à DC1 avec les informations d'identification CORP\\User1. Pour configurer le domaine CORP afin que les utilisateurs et les ordinateurs utilisent leur contrôleur de domaine local pour l’authentification, exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## Phase 2 : configurer RRAS1

RRAS1 fournit le routage du trafic et les services de périphérique VPN entre les ordinateurs sur le sous-réseau de réseau d'entreprise et le réseau virtuel TestVNET. RRAS1 doit avoir deux cartes réseau installées.

Tout d'abord, installez le système d'exploitation sur RRAS1.

1.	Démarrez l'installation de Windows Server 2012 R2.
2.	Suivez les instructions pour terminer l'installation, en spécifiant un mot de passe fort pour le compte d'administrateur local. Ouvrez une session en utilisant le compte d'administrateur local.
3.	Connectez RRAS1 à un réseau qui a accès à Internet et exécutez Windows Update pour installer les dernières mises à jour pour Windows Server 2012 R2.
4.	Connectez une carte réseau au sous-réseau de réseau d'entreprise et l'autre directement à Internet. RRAS1 peut se trouver derrière un pare-feu Internet mais ne doit pas être derrière un traducteur d'adresses réseau (NAT).

Ensuite, configurez les propriétés TCP/IP de RRAS1. Vous aurez besoin d'une configuration d'adresse IP publique, y compris une adresse et un masque de sous-réseau (ou une longueur de préfixe) ainsi que la passerelle par défaut et les serveurs DNS de votre fournisseur de services Internet (ISP).

Utilisez les commandes suivantes à partir d'une invite de commandes Windows PowerShell de niveau administrateur sur RRAS1. Avant d'exécuter ces commandes, renseignez les valeurs des variables et supprimez les caractères < and >. Vous pouvez obtenir les noms actuels des cartes réseau à partir de l’affichage de la commande **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Pour la dernière commande, vérifiez qu'il y a quatre réponses à partir de l'adresse IP 10.0.0.1.

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## Phase 3 : créer le réseau virtuel Azure entre différents locaux

Pour commencer, ouvrez une session sur le [Portail de gestion Azure](https://manage.windowsazure.com/) avec les informations d’identification de votre abonnement Azure et créez un réseau virtuel nommé TestVNET.

1.	Dans la barre des tâches du portail de gestion Azure, cliquez sur **Nouveau > Services réseau > Réseau virtuel > Création personnalisée**.
2.	Sur la page Détails du réseau virtuel, tapez **TestVNET** dans le champ **Nom**.
3.	Dans le champ **Emplacement**, sélectionnez le centre de données approprié à votre emplacement.
4.	Cliquez sur la flèche Suivant.
5.	Sur la page Serveurs DNS et connectivité VPN, dans la zone **Serveurs DNS**, tapez **DC1** dans le champ **Sélectionner ou entrer le nom**, tapez **10.0.0.1** dans le champ **Adresse IP**, puis sélectionnez **Configurer un réseau VPN de site à site**.
6.	Dans **Réseau Local**, sélectionnez **Spécifier un nouveau réseau local**. 
7.	Cliquez sur la flèche Suivant.
8.	Dans la page Connectivité de site à site :
	- Dans le champ **Nom**, tapez **Corpnet**. 
	- Dans le champ **Adresse IP du périphérique VPN**, tapez l’adresse IP publique attribuée à l’interface Internet de RRAS1.
	- Dans la zone **Espace d’adressage**, au niveau de la colonne **Adresse IP de départ**, tapez **10.0.0.0**. Dans le champ **CIDR (nombre d’adresses)**, sélectionnez **/8**, puis cliquez sur **Ajouter un espace d’adressage**.
9.	Cliquez sur la flèche Suivant.
10.	Sur la page Espace d’adresses du réseau virtuel :
	- Dans la zone **Espace d’adressage**, au niveau du champ **Adresse IP de départ**, sélectionnez **192.168.0.0**.
	- Dans **Sous-réseaux**, cliquez sur **Subnet-1** et remplacez le nom par **TestSubnet**. 
	- Dans la colonne **CIDR (nombre d’adresses)** pour TestSubnet, cliquez sur **/24 (256)**.
	- Cliquez sur **Ajouter un sous-réseau de passerelle**.
11.	Cliquez sur l’icône Terminer. Attendez que le réseau virtuel soit créé avant de continuer.

Ensuite, suivez les instructions de la page [Installation et configuration d'Azure PowerShell](../install-configure-powershell.md) pour installer Azure PowerShell sur votre ordinateur local.

Ensuite, créez un service cloud pour le réseau virtuel TestVNET. Vous devez choisir un nom unique. Par exemple, vous pouvez le nommer TestVNET-*UniqueSequence*, où *UniqueSequence* est l’abréviation de votre entreprise. Par exemple, si le nom de votre organisation est Tailspin Toys, vous pouvez nommer le service cloud TestVNET-Tailspin.

Vous pouvez tester l'unicité du nom avec la commande Azure PowerShell suivante sur votre ordinateur local.

	Test-AzureName -Service <Proposed cloud service name>

Si cette commande renvoie « False », le nom proposé est unique. Créez le service cloud avec cette commande.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Ensuite, créez un nouveau compte de stockage pour le réseau virtuel TestVNET. Vous devez choisir un nom unique. Vous pouvez tester l'unicité du nom du compte de stockage avec cette commande.

	Test-AzureName -Storage <Proposed storage account name>

Si cette commande renvoie « False », le nom proposé est unique. Créez et définissez le compte de stockage avec les commandes suivantes.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## Phase 4 : créer la connexion VPN de site à site

Commencez par créer une passerelle de réseau virtuel.

1.	Dans le Portail de gestion Azure de votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis vérifiez que la colonne **État** concernant TestVNET est définie sur **Créé**.
2.	Cliquez sur **TestVNET**. Sur la page Tableau de bord, vous devez voir l’état **Passerelle non créée**.
3.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et qu’elle prenne l’état **Connexion en cours**. Cette opération peut prendre quelques minutes.
4.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de l’adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestVNET. Vous avez besoin de cette adresse IP pour configurer RRAS1.
5.	Dans la barre des tâches, cliquez sur **Gérer la clé**, puis cliquez sur l’icône de copie en regard de la clé pour la copier dans le Presse-papiers. Collez cette clé dans un document que vous enregistrez. Vous avez besoin de cette valeur de clé pour configurer RRAS1. 

Ensuite, configurez RRAS1 avec le service Routage et accès distant en tant que périphérique VPN pour le sous-réseau Corpnet. Connectez-vous à RRAS1 en tant qu'administrateur local et exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Ensuite, configurez RRAS1 pour recevoir la connexion VPN de site à site à partir de la passerelle VPN Azure. Redémarrez RRAS1, puis connectez-vous en tant qu'administrateur local et exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell. Vous devez fournir l'adresse IP de la passerelle VPN Azure et la valeur de clé.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Ensuite, accédez au Portail de gestion Azure sur votre ordinateur local et attendez que le réseau virtuel TestVNET indique l’état **Connecté**.

Puis, configurez RRAS1 pour prendre en charge le trafic traduit vers des emplacements Internet. Sur RRAS1 :

1.	Sur l’écran d’accueil, tapez **rras**, puis cliquez sur **Routage et accès distant**. 
2.	Dans l’arborescence de la console, ouvrez le nom du serveur, puis cliquez sur **IPv4**.
3.	Cliquez avec le bouton droit sur **Général**, puis cliquez sur **Nouveau protocole de routage**.
4.	Cliquez sur **NAT**, puis sur **OK**.
5.	Dans l’arborescence de la console, cliquez avec le bouton droit sur **NAT**, cliquez sur **Nouvelle interface**, puis sur **Corpnet**, et enfin deux fois sur **OK**.
6.	Cliquez avec le bouton droit sur **NAT**, cliquez sur **Nouvelle Interface**, puis sur **Internet**, et enfin sur **OK**.
7.	Dans l’onglet **NAT**, cliquez sur **Interface publique connectée à Internet**, sélectionnez **Activer NAT sur cette interface**, puis cliquez sur **OK**.


Ensuite, configurez DC1, APP1 et CLIENT1 pour utiliser RRAS1 comme passerelle par défaut.
 
Sur DC1, exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Si le nom de l’interface n’est pas Ethernet, utilisez la commande **Get-NetAdapter** pour déterminer le nom de l’interface.

Sur APP1, exécutez cette commande à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

Sur CLIENT1, exécutez cette commande à partir d'une invite de commandes Windows PowerShell de niveau administrateur.

	ipconfig /renew

Ceci est votre configuration actuelle.
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## Phase 5 : configurer DC2

Commencez par créer une machine virtuelle Azure pour DC2 avec les commandes suivantes à partir de l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles -LUN 0 -HostCaching None
	New-AzureVM -ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


Ensuite, connectez-vous à la machine virtuelle DC2.

1.	Dans le volet gauche du Portail de gestion Azure, cliquez sur **Machines virtuelles**, puis cliquez sur **En cours d’exécution** dans la colonne **État** pour DC2.
2.	Dans la barre des tâches, cliquez sur **Se connecter**. 
3.	Lorsque vous êtes invité à ouvrir DC2.rdp, cliquez sur **Ouvrir**.
4.	Lorsque le message Connexion Bureau à distance s’affiche, cliquez sur **Connecter**.
5.	À l'invite vous demandant des informations d'identification, utilisez ce qui suit :
	- Nom : **DC2\**[Nom de compte d’administrateur local]
	- Mot de passe : [Mot de passe de compte d’administrateur local]
6.	Lorsqu’une zone de message de connexion Bureau à distance faisant référence aux certificats s’ouvre, cliquez sur **Oui**.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur DC2, exécutez :

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit obtenir quatre réponses correctes à partir de l’adresse IP 10.0.0.1. Il s'agit d'un test de trafic sur la connexion VPN de site à site.

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

Ensuite, configurez DC2 comme contrôleur de domaine réplica pour le domaine corp.contoso.com. Exécutez ces commandes dans l’invite de commandes Windows PowerShell sur DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Notez que vous serez invité à fournir le mot de passe CORP\\User1 et un mot de passe du Mode restauration des services d'annuaire (DSRM) et à redémarrer DC2.

Maintenant que le réseau virtuel TestVNET possède son propre serveur DNS (DC2), vous devez configurer le réseau virtuel TestVNET pour utiliser ce serveur DNS.

1.	Dans le volet gauche du Portail de gestion Azure, cliquez sur **Réseaux**, puis sur **TestVNET**.
2.	Cliquez sur **Configurer**.
3.	Dans **Serveurs DNS**, supprimez l’entrée **10.0.0.1**.
4.	Dans **Serveurs DNS**, ajoutez une entrée avec le nom **DC2** et l’adresse IP **192.168.0.4**. 
5.	Dans la barre de commandes de la partie inférieure, cliquez sur **Enregistrer**.
6.	Dans le volet gauche du Portail de gestion Azure, cliquez sur **Machines virtuelles**, puis sur la colonne **État** située en regard de DC2.
7.	Dans la barre de commandes, cliquez sur **Redémarrer**. Attendez que DC2 redémarre.


Ceci est votre configuration actuelle.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
Votre environnement de cloud hybride est maintenant prêt à être testé.

## Réduction des coûts récurrents de cet environnement

Afin de réduire les coûts d’exécution des machines virtuelles dans cet environnement, effectuez les tests et démonstrations nécessaires aussi rapidement que possible et supprimez ou arrêtez les machines virtuelles lorsque vous ne les utilisez pas. Par exemple, vous pouvez utiliser Azure Automation et un runbook pour arrêter automatiquement les machines virtuelles du réseau virtuel Test\_VNET à la fin de chaque journée. Pour plus d’informations, voir l’article [Prise en main d’Azure Automation](../automation-create-runbook-from-samples.md).

La passerelle VPN Azure est implémentée comme un ensemble de deux machines virtuelles qui entraînent un coût continu. Pour plus d’informations, voir la page [Virtual Network - Tarification](https://azure.microsoft.com/pricing/details/virtual-network/). Pour réduire les coûts de la passerelle VPN, créez l'environnement de test et exécutez vos tests et démonstrations requis aussi rapidement que possible ou supprimez la passerelle en procédant comme suit.

1.	À partir du Portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis sur **TestVNET**, et enfin sur **Tableau de bord**.
2.	Dans la barre des tâches, cliquez sur **Supprimer une passerelle**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit supprimée et qu’elle prenne l’état **La passerelle n’a pas été créée**.

Si vous supprimez la passerelle et que vous souhaitez restaurer l'environnement de test, vous devez d'abord créer une passerelle.

1.	À partir du Portail de gestion Azure sur votre ordinateur local, cliquez sur **Réseaux** dans le volet gauche, puis cliquez sur **TestVNET**. Sur la page Tableau de bord, vous devez voir l’état **La passerelle n’a pas été créée**.
2.	Dans la barre des tâches, cliquez sur **Créer une passerelle**, puis sur **Routage dynamique**. Cliquez sur **Oui** lorsque vous y êtes invité. Attendez que la passerelle soit établie et qu’elle prenne l’état **Connexion en cours**. Cette opération peut prendre quelques minutes.
3.	Sur la page Tableau de bord, notez la valeur du champ **Adresse IP de la passerelle**. Il s’agit de la nouvelle adresse IP publique de la passerelle VPN Azure pour le réseau virtuel TestVNET. Vous avez besoin de cette adresse IP pour reconfigurer RRAS1.
4.	Dans la barre des tâches, cliquez sur **Gérer la clé**, puis cliquez sur l’icône de copie en regard de la clé pour la copier dans le Presse-papiers. Collez cette valeur de clé dans un document et enregistrez ce dernier. Vous avez besoin de cette valeur de clé pour reconfigurer RRAS1. 

Ensuite, connectez-vous à RRAS1 en tant qu'administrateur local et exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell de niveau administrateur afin de reconfigurer RRAS1 avec la nouvelle adresse IP publique et la clé prépartagée.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

Ensuite, accédez au portail de gestion Azure sur votre ordinateur local et attendez que le réseau virtuel TestVNET affiche un état connecté.
 
## Étapes suivantes

- Configurez une [batterie de serveurs intranet SharePoint](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md), une [application métier web](virtual-networks-setup-lobapp-hybrid-cloud-testing.md) ou un [serveur de synchronisation de l’annuaire Office 365 (DirSync)](virtual-networks-setup-dirsync-hybrid-cloud-testing.md) dans cet environnement.

<!---HONumber=AcomDC_0204_2016-->