<properties 
	pageTitle="Simulation d'environnement de test de cloud hybride | Microsoft Azure" 
	description="Créez une simulation d'environnement de cloud hybride pour exécuter des tests informatiques ou de développement, à l'aide de deux réseaux virtuels Azure et d'une connexion de réseau virtuel à réseau virtuel." 
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

# Configuration d’une simulation d’environnement de cloud hybride à des fins de test

Cet article vous présente la création d'un environnement de cloud hybride simulé avec Microsoft Azure pour le test, à l'aide de deux réseaux virtuels Azure distincts. Utilisez cette configuration à la place de la [Configuration d’un environnement de cloud hybride à des fins de test](virtual-machines-windows-ps-hybrid-cloud-test-env-base.md) lorsque vous ne disposez pas d’une connexion Internet directe et d’une adresse IP publique. Voici la configuration obtenue.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)

Elle simule un environnement de production de cloud hybride. Elle comprend :

- Un réseau local simulé et simplifié hébergé dans un réseau virtuel Azure (le réseau virtuel TestLab).
- Un réseau virtuel entre sites simulé hébergé dans Azure (TestVNET).
- Une connexion de réseau virtuel à réseau virtuel entre les deux réseaux virtuels.
- Un contrôleur de domaine secondaire dans le réseau virtuel TestVNET.

Elle fournit une base et un point de départ commun pour :

- Développer et tester des applications dans une simulation d’environnement de cloud hybride.
- Créer des configurations de test des ordinateurs, certains dans le réseau virtuel TestLab et d’autres dans le réseau virtuel TestVNET, afin de simuler des charges de travail dans un cloud hybride.

La configuration de l’environnement de test de cloud hybride comprend quatre grandes étapes :

1.	Configurer le réseau virtuel TestLab.
2.	Créer le réseau virtuel entre sites.
3.	Créer la connexion VPN de réseau virtuel à réseau virtuel.
4.	Configurer DC2. 

Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour une version d’évaluation gratuite à partir de la page permettant d’[essayer Azure](https://azure.microsoft.com/pricing/free-trial/). Si vous avez un abonnement MSDN ou Visual Studio, consultez [Crédit Azure mensuel pour les abonnés Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Les machines virtuelles et les passerelles de réseau virtuel dans Azure entraînent des frais lors de leur utilisation. Ce coût est facturé sur votre abonnement de version d’évaluation gratuite, votre abonnement MSDN ou votre abonnement payant. La passerelle VPN Azure est implémentée comme un ensemble de deux machines virtuelles. Pour limiter les coûts, créez l'environnement de test et exécutez les tests et démonstrations nécessaires aussi rapidement que possible.

## Phase 1 : configuration du réseau virtuel TestLab

Suivez les instructions de la section [Environnement de test de la configuration de base](virtual-machines-windows-test-config-env.md) pour configurer les ordinateurs DC1, APP1 et CLIENT1 dans un réseau virtuel Azure nommé TestLab.

Ensuite, démarrez une invite de commandes Azure PowerShell.

> [AZURE.NOTE] Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Connectez-vous à votre compte.

	Login-AzureRMAccount

Obtenez votre nom d'abonnement à l'aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure. Utilisez le même abonnement que celui utilisé pour créer la configuration de base. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Ensuite, ajoutez un sous-réseau de passerelle au réseau virtuel TestLab de votre configuration de base, qui sera utilisé pour héberger la passerelle Azure.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Ensuite, demandez une adresse IP publique à assigner à la passerelle pour le réseau virtuel TestLab.

	$gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Ensuite, créez votre passerelle.

	$vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
	New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

N'oubliez pas que la création de passerelles peut prendre au moins 20 minutes.

Dans le portail Azure sur votre ordinateur local, connectez-vous à DC1 avec les informations d'identification CORP\\User1. Pour configurer le domaine CORP afin que les utilisateurs et les ordinateurs utilisent leur contrôleur de domaine local pour l’authentification, exécutez les commandes suivantes à partir d’une invite de commandes Windows PowerShell de niveau administrateur.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph1.png)
 
## Phase 2 : création du réseau virtuel TestVNET

Tout d'abord, créez le réseau virtuel TestVNET et protégez-le à l'aide d'un groupe de sécurité réseau.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
	$testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
	$gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Ensuite, demandez une adresse IP publique à assigner à la passerelle du réseau virtuel TestVNET et créez votre passerelle.

	$gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
	New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph2.png)
 
##Phase 3 : création de la connexion de réseau virtuel à réseau virtuel

Tout d'abord, obtenez une clé prépartagée, aléatoire, à chiffrement fort, de 32 caractères auprès de votre administrateur réseau ou de sécurité. Vous pouvez également utiliser les informations de la rubrique [Create a random string for an IPsec preshared key (Création d’une chaîne aléatoire pour une clé prépartagée IPsec)](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) pour obtenir une clé prépartagée.

Puis, utilisez ces commandes pour créer la connexion VPN de site à site, ce qui peut prendre du temps.

	$sharedKey="<pre-shared key value>"
	$gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
	$gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
	New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
	New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Après quelques minutes, la connexion doit être établie. Notez qu'à ce stade, les passerelles et les connexions créées avec Azure Resource Manager ne sont pas visibles dans le portail Azure.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph3.png)
 
## Phase 4 : configurer DC2

Créez d’abord une machine virtuelle Azure pour DC2. Exécutez ces commandes à l’invite de commandes Azure PowerShell sur votre ordinateur local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name for the base configuration>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
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

Ensuite, connectez-vous à la machine virtuelle DC2 à partir du portail Azure.

Ensuite, configurez une règle de pare-feu Windows pour autoriser le trafic pour le test de la connectivité de base. À partir d’une invite de commandes Windows PowerShell de niveau administrateur sur DC2, exécutez ces commandes.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

La commande ping doit aboutir à quatre réponses réussies à partir de l’adresse IP 10.0.0.4. Ceci est un test de trafic sur la connexion de réseau virtuel à réseau virtuel.

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

Ensuite, configurez DC2 comme contrôleur de domaine réplica pour le domaine corp.contoso.com. Exécutez ces commandes dans l’invite de commandes Windows PowerShell sur DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Notez que vous êtes invité à fournir le mot de passe CORP\\User1 et un mot de passe du Mode restauration des Services annuaire (DSRM), puis à redémarrer DC2.

Maintenant que le réseau virtuel TestVNET possède son propre serveur DNS (DC2), vous devez configurer le réseau virtuel TestVNET pour utiliser ce serveur DNS.

1.	Dans le volet gauche du portail Azure, cliquez sur l’icône des réseaux virtuels, puis sur **TestVNET**.
2.	Dans l’onglet **Paramètres**, cliquez sur **Serveurs DNS**.
3.	Sous **Serveur DNS principal**, tapez **192.168.0.4** pour remplacer 10.0.0.4.
4.	Cliquez sur **Enregistrer**.

Ceci est votre configuration actuelle.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-sim/virtual-machines-windows-ps-hybrid-cloud-test-env-sim-ph4.png)
 
Votre environnement de cloud hybride simulé est maintenant prêt pour les tests.

## Étape suivante

- Configurez une [batterie de serveurs intranet SharePoint](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), une [application métier web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) ou un [serveur de synchronisation d’annuaires Office 365 (DirSync)](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) dans cet environnement.

<!---HONumber=AcomDC_0601_2016-->