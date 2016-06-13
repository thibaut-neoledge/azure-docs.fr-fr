<properties 
	pageTitle="Application métier - Phase 1 | Microsoft Azure" 
	description="Créez le réseau virtuel et d'autres éléments de l'infrastructure Azure dans la phase 1 de l'application métier dans Azure." 
	documentationCenter=""
	services="virtual-machines-windows" 
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

# Charge de travail des applications métier, phase 1 : configurer Azure
 
Au cours de cette phase de déploiement (uniquement dans Intranet) d'une application métier à haute disponibilité dans des services d'infrastructure Azure, vous créez l'infrastructure de réseau et de stockage Azure. Vous devez terminer cette opération avant de passer à la [Phase 2](virtual-machines-windows-ps-lob-ph2.md). Consultez [Déployer une application métier à haute disponibilité dans Azure](virtual-machines-windows-lob-overview.md) pour toutes les phases.

Azure doit être configuré avec les composants réseau de base suivants :

- un réseau virtuel intersite avec un sous-réseau pour l'hébergement des machines virtuelles Azure ;
- deux comptes de stockage Azure pour stocker des images de disque VHD et des disques de données supplémentaires.
- Trois groupes à haute disponibilité

## Avant de commencer

Avant de commencer la configuration de composants Azure, remplissez les tables suivantes. Pour vous aider dans la procédure de configuration d'Azure, imprimez cette section et écrivez les informations nécessaires ou copiez cette section dans un document et remplissez-la.

Pour les paramètres du réseau virtuel (VNet), remplissez la table V.

Élément | Élément de configuration | Description | Valeur 
--- | --- | --- | --- 
1\. | Nom du réseau virtuel | Le nom à attribuer au réseau virtuel Azure (par exemple, SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Emplacement du réseau virtuel | Le centre de données Azure qui contiendra le réseau virtuel. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nom du réseau local | Un nom à attribuer au réseau de votre organisation. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Adresse IP du périphérique VPN | L'adresse IPv4 publique de l'interface de votre périphérique VPN sur Internet. Consultez votre service informatique pour la déterminer. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Espace d'adressage du réseau virtuel | L'espace d'adressage (défini dans un préfixe d'adresse privé unique) pour le réseau virtuel. Consultez votre service informatique pour le déterminer. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Le premier serveur DNS pour le réseau virtuel | La quatrième adresse IP possible pour l'espace d'adressage du second sous-réseau du réseau virtuel (voir la table S). Consultez votre service informatique pour la déterminer. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Le second serveur DNS pour le réseau virtuel | La cinquième adresse IP possible pour l'espace d'adressage du second sous-réseau du réseau virtuel (voir la table S). Consultez votre service informatique pour la déterminer. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Clé IPsec partagée | Chaîne alphanumérique aléatoire de 32 caractères à utiliser pour authentifier les deux côtés de la connexion VPN de site à site. Consultez votre service informatique ou département de sécurité pour déterminer cette valeur de clé. Vous pouvez également consulter [Créer une chaîne aléatoire pour une clé IPsec prépartagée](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx).| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Table V : configuration de réseaux virtuels intersite**

Remplissez la Table S pour le sous-réseau de cette solution.

- Pour le premier sous-réseau, déterminez un espace d'adressage de 29 bits (avec une longueur de préfixe /29) pour le sous-réseau de passerelle Azure.
- Spécifiez pour le second sous-réseau un nom convivial, une adresse IP unique en fonction de l'espace d'adressage du réseau virtuel et un but descriptif. 

Consultez votre service informatique afin de déterminer ces espaces d'adressage à partir de l'espace d'adressage du réseau virtuel. Les deux espaces d'adressage doivent être au format de routage CIDR (Classless Interdomain Routing), également connu comme format de préfixe réseau. 10.24.64.0/20 est un exemple.

Élément | Nom du sous-réseau | Espace d'adressage du sous-réseau | Objectif 
--- | --- | --- | --- 
1\. | Sous-réseau de passerelle | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Le sous-réseau utilisé par les machines virtuelles de passerelle Azure.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Table S : sous-réseaux du réseau virtuel**

> [AZURE.NOTE] Cette architecture prédéfinie utilise un sous-réseau unique par souci de simplicité. Si vous voulez superposer un ensemble de filtres de trafic pour émuler l'isolation du sous-réseau, vous pouvez utiliser des [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) Azure.

Pour les deux serveurs DNS locaux que vous souhaitez utiliser lors de la configuration initiale des contrôleurs de domaine de votre réseau virtuel, remplissez la table D. Donnez à chaque serveur DNS un nom convivial et une adresse IP unique. Ce nom convivial ne doit pas nécessairement correspondre au nom d'hôte ou au nom d'ordinateur du serveur DNS. Notez que deux entrées vides sont répertoriées, mais vous pouvez en ajouter d'autres. Consultez votre service informatique pour déterminer cette liste.

Élément | Adresse IP du serveur DNS 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 

**Table D : serveurs DNS locaux**

Pour router des paquets du réseau virtuel Azure vers le réseau de votre organisation par le biais de la connexion VPN de site à site, vous devez configurer le réseau virtuel avec un réseau local qui contient une liste d'espaces d'adressage (en notation CIDR) pour tous les emplacements accessibles sur le réseau local de votre organisation. La liste des espaces d'adressage qui définissent votre réseau local doit être unique et ne doit pas chevaucher l'espace d'adressage utilisé pour d'autres réseaux virtuels ou d'autres réseaux locaux.

Pour l'ensemble des espaces d'adressage du réseau local, remplissez la table L. Notez que trois entrées vides sont répertoriées, mais que vous en avez généralement besoin de plus. Consultez votre service informatique pour déterminer la liste des espaces d'adressage.

Élément | Espace d'adressage du réseau local 
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Table L : préfixes d'adresses pour le réseau local**

Démarrez d'abord une invite de commandes Azure PowerShell.

> [AZURE.NOTE] Les jeux de commandes suivants font appel à Azure PowerShell 1.0 et versions ultérieures. Pour plus d’informations, consultez [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Tout d’abord, démarrez une invite Azure PowerShell et connectez-vous à votre compte.

	Login-AzureRMAccount

Obtenez votre nom d'abonnement à l'aide de la commande suivante.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Définissez votre abonnement Azure. Remplacez tous les éléments entre guillemets, y compris les caractères < and >, par les noms appropriés.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Créez ensuite un groupe de ressources pour votre application métier. Pour déterminer un nom de groupe de ressources unique, utilisez cette commande pour répertorier vos groupes de ressources existants.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Créez votre nouveau groupe de ressources avec ces commandes.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Les machines virtuelles basées sur Resource Manager requièrent un compte de stockage basé sur Resource Manager.

Élément | Nom du compte de stockage | Objectif 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Le compte de stockage premium utilisé par les machines virtuelles du serveur SQL.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | Le compte de stockage standard utilisé par toutes les autres machines virtuelles dans la charge de travail. 

**Table ST : comptes de stockage**

Vous aurez besoin de ces noms lors de la création des machines virtuelles au cours des phases 2, 3 et 4.

Pour chaque compte de stockage, vous devez choisir un nom global unique contenant uniquement des chiffres et des lettres minuscules. Vous pouvez utiliser cette commande pour répertorier les comptes de stockage existants.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Pour créer le premier compte de stockage, exécutez ces commandes.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Premium_LRS -Location $locName

Pour créer le second compte de stockage, exécutez ces commandes.

	$saName="<Table ST – Item 2 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Créez ensuite le réseau virtuel Azure qui hébergera votre application métier.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$lobSubnetName="<Table S – Item 2 – Subnet name column>"
	$lobSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$lobSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $lobSubnetName -AddressPrefix $lobSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$lobSubnet -DNSServer $dnsServers

Puis utilisez ces commandes pour créer des passerelles pour la connexion VPN de site à site.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="LOBAppPublicIPAddress"
	$vnetGatewayIpConfigName="LOBAppPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="LOBAppAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="LOBAppLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="LOBAppS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Et, enfin, configurez le périphérique VPN local pour qu’il se connecte à la passerelle VPN Azure. Pour plus d’informations, consultez [Configurer votre périphérique VPN](../virtual-network/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Pour configurer votre périphérique VPN sur site, vous avez besoin des éléments suivants :

- L’adresse IPv4 publique de la passerelle VPN Azure pour votre réseau virtuel, affichée à l’aide de la commande **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**.
- la clé IPsec prépartagée pour la connexion VPN de site à site (Table V- Élément 8 – Colonne Valeur).

Ensuite, assurez-vous que l'espace d'adressage du réseau virtuel est accessible à partir de votre réseau local. Pour cela, il vous suffit généralement d'ajouter un itinéraire correspondant à l'espace d'adressage du réseau virtuel à votre périphérique VPN, puis de transmettre cet itinéraire au reste de l'infrastructure de routage du réseau de votre organisation. Consultez votre service informatique pour déterminer la procédure à suivre.

Définissez ensuite les noms des trois groupes à haute disponibilité. Remplissez la table A.

Élément | Objectif | Nom du groupe à haute disponibilité 
--- | --- | --- 
1\. | Contrôleurs de domaine | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Serveurs SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Serveurs Web | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Nom du groupe à haute disponibilité**

Vous aurez besoin de ces noms lors de la création des machines virtuelles au cours des phases 2, 3 et 4.

Créez ces groupes à haute disponibilité grâce à ces commandes Azure PowerShell.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Vous trouverez ci-dessous la configuration résultant de l'exécution de cette phase.

![](./media/virtual-machines-windows-ps-lob-ph1/workload-lobapp-phase1.png)

## Étape suivante

- Pour poursuivre la configuration de cette charge de travail, utilisez la [Phase 2](virtual-machines-windows-ps-lob-ph2.md).

<!---HONumber=AcomDC_0601_2016-->