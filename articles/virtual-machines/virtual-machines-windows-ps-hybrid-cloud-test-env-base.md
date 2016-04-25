<properties 
	pageTitle="Environnement de test de cloud hybride | Microsoft Azure" 
	description="Apprenez à créer un environnement de cloud hybride complet avec un réseau local simplifié pour les professionnels de l'informatique ou le test des développements." 
	services="virtual-machines-windows" 
	documentationCenter="" 
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
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configuration d’un environnement de cloud hybride pour le test

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.
 
Cette rubrique vous guide lors de la création d'un environnement de cloud hybride avec Microsoft Azure pour le test. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

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

Si vous n’avez pas encore d’abonnement Azure, vous pouvez obtenir un compte gratuit sur la page [Essayer Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN ou Visual Studio, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Les machines virtuelles et les passerelles de réseau virtuel dans Azure entraînent des frais lors de leur utilisation. La passerelle VPN Azure est implémentée comme un ensemble de deux machines virtuelles. Pour plus d’informations, voir la page [Virtual Network - Tarification](https://azure.microsoft.com/pricing/details/virtual-network/). Pour limiter les coûts d’exécution de la passerelle VPN, créez l’environnement de test et exécutez les tests et démonstrations nécessaires aussi rapidement que possible.

Cette configuration nécessite un sous-réseau de test comportant jusqu’à quatre ordinateurs connectés directement à Internet à l’aide d’une adresse IP publique. Si vous ne disposez pas de ces ressources, vous pouvez également configurer une [simulation d’environnement de cloud hybride à des fins de test](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). L’environnement de test de cloud hybride simulé nécessite uniquement un abonnement Azure.

## Phase 1 : configurer les ordinateurs sur le sous-réseau de réseau d’entreprise

Suivez les instructions de la section décrivant la procédure de configuration du sous-réseau d’entreprise dans le [Guide de laboratoire de test : configuration de base pour Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) (en anglais) pour configurer les ordinateurs DC1, APP1 et CLIENT1 sur un sous-réseau nommé Corpnet. **Ce sous-réseau doit être isolé de votre réseau d’entreprise, car il est connecté directement à Internet par le biais de l’ordinateur RRAS1.**

Ensuite, connectez-vous à DC1 avec les informations d'identification CORP\\User1. Pour configurer le domaine CORP afin que les utilisateurs et les ordinateurs utilisent leur contrôleur de domaine local pour l’authentification, exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## Phase 2 : configurer RRAS1

RRAS1 fournit le routage du trafic et les services de périphérique VPN entre les ordinateurs sur le sous-réseau de réseau d'entreprise et le réseau virtuel TestVNET. RRAS1 doit avoir deux cartes réseau installées.

Tout d'abord, installez le système d'exploitation sur RRAS1.

1.	Démarrez l'installation de Windows Server 2012 R2.
2.	Suivez les instructions pour terminer l'installation, en spécifiant un mot de passe fort pour le compte d'administrateur local. Ouvrez une session en utilisant le compte d'administrateur local.
3.	Connectez RRAS1 à un réseau qui a accès à Internet et exécutez Windows Update pour installer les dernières mises à jour pour Windows Server 2012 R2.
4.	Connectez une carte réseau au sous-réseau de réseau d'entreprise et l'autre directement à Internet. RRAS1 peut se trouver derrière un pare-feu Internet mais ne doit pas être derrière un traducteur d'adresses réseau (NAT).

Ensuite, configurez les propriétés TCP/IP de RRAS1. Vous aurez besoin d'une configuration d'adresse IP publique, y compris une adresse et un masque de sous-réseau (ou une longueur de préfixe) ainsi que la passerelle par défaut et les serveurs DNS de votre fournisseur de services Internet (ISP). Vous avez besoin de l’adresse IP publique pour l’étape 3.

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

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## Phase 3 : créer le réseau virtuel Azure entre différents locaux

Démarrez une invite de commandes Azure PowerShell.

> [AZURE.NOTE] Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Connectez-vous à votre compte.

	Login-AzureRMAccount

Obtenez votre nom d'abonnement à l'aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure. Utilisez le même abonnement que celui utilisé pour créer la configuration de base. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Ensuite, créez un groupe de ressources pour l’environnement de test hybride.

Pour déterminer un nom de groupe de ressources unique, utilisez cette commande pour répertorier vos groupes de ressources existants.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Créez votre nouveau groupe de ressources avec ces commandes.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Les machines virtuelles basées sur Resource Manager requièrent un compte de stockage basé sur Resource Manager. Pour chaque compte de stockage, vous devez choisir un nom global unique contenant uniquement des chiffres et des lettres minuscules. Vous pouvez utiliser cette commande pour répertorier les comptes de stockage existants.

	Get-AzureRMStorageAccount | Sort Name | Select Name

Créez un compte de stockage avec les commandes suivantes.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Puis, créez le réseau virtuel Azure qui hébergera votre environnement de cloud hybride et protégez-le avec un groupe de sécurité réseau.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

Puis utilisez ces commandes pour créer des passerelles pour la connexion VPN de site à site. Vous avez besoin de l’adresse IP publique de l’interface Internet de RRAS1 à partir de l’étape 2.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

N'oubliez pas que la création de passerelles peut prendre au moins 20 minutes.

Ensuite, utilisez la commande suivante pour déterminer l’adresse IP publique de la passerelle VPN Azure pour votre réseau virtuel.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

Notez l’adresse IP dans le champ **IPAddress** de l’affichage. Vous en aurez besoin à l’étape 4.

Ensuite, obtenez une clé prépartagée, aléatoire, à chiffrement fort, de 32 caractères auprès de votre administrateur réseau ou de sécurité. Vous pouvez également utiliser les informations sous [Créer une chaîne aléatoire pour une clé prépartagée IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) pour obtenir une clé prépartagée.

Utilisez ces commandes pour créer la connexion VPN de site à site dans Azure.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## Phase 4 : créer la connexion VPN de site à site

Tout d’abord, configurez RRAS1 avec le service Routage et accès distant en tant que périphérique VPN pour le sous-réseau Corpnet. Connectez-vous à RRAS1 en tant qu'administrateur local et exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Ensuite, configurez RRAS1 pour recevoir la connexion VPN de site à site à partir de la passerelle VPN Azure. Redémarrez RRAS1, puis connectez-vous en tant qu'administrateur local et exécutez les commandes suivantes à partir d'une invite de commandes Windows PowerShell. Vous devez fournir l’adresse IP de la passerelle VPN Azure et la valeur de clé prépartagée obtenue à l’étape 3.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Quelques minutes sont nécessaires pour que la connexion entre RRAS1 et la passerelle VPN Azure soit établie.

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
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## Phase 5 : configurer DC2

Commencez par créer une machine virtuelle Azure pour DC2 avec les commandes suivantes à partir de l'invite de commandes Azure PowerShell sur votre ordinateur local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Ensuite, utilisez le portail Azure pour vous connecter à la nouvelle machine virtuelle DC2 avec les informations d’identification du compte d’administrateur local.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d'une invite de commandes Windows PowerShell de niveau administrateur sur DC2, exécutez :

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit obtenir quatre réponses correctes à partir de l’adresse IP 10.0.0.1. Si vous utilisez la *configuration de cloud hybride simulé*, vous devez voir quatre réponses réussies à partir de l’adresse IP 10.0.0.4. Il s’agit d’un test de trafic sur la connexion VPN de site à site ou la connexion de réseau virtuel à réseau virtuel.

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

1.	Dans le volet gauche du portail Azure, cliquez sur l’icône des réseaux virtuels, puis sur **TestVNET**.
2.	Dans l’onglet **Paramètres**, cliquez sur **Serveurs DNS**.
3.	Dans **Serveur DNS principal**, tapez **192.168.0.4** pour remplacer 10.0.0.4.
4.	Cliquez sur Enregistrer.

Ensuite, redémarrez DC2 afin qu’elle utilise la nouvelle configuration de serveur DNS avec les commandes suivantes à l’invite de commandes Azure PowerShell.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Votre environnement de cloud hybride est maintenant prêt à être testé.
 
## Étapes suivantes

- Configurez une [batterie de serveurs intranet SharePoint](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), une [application métier web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) ou un [serveur de synchronisation d’annuaires Office 365 (DirSync)](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) dans cet environnement.

<!---HONumber=AcomDC_0413_2016-->