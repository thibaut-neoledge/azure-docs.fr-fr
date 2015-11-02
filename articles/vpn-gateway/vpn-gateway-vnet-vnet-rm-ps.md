<properties
   pageTitle="Créer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article vous guide dans l’interconnexion de réseaux virtuels avec Azure Resource Manager et PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/20/2015"
   ms.author="cherylmc"/>

# Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Cet article vous guidera à travers les étapes à l'aide du modèle de déploiement Resource Manager. Vous pouvez sélectionner l'article correspondant au modèle de déploiement et l'outil de déploiement à l'aide des onglets ci-dessus.

>[AZURE.NOTE]Il est important de comprendre qu’Azure fonctionne actuellement avec deux modèles de déploiement : Resource Manager et classique. Avant de commencer votre configuration, assurez-vous que vous comprenez les modèles de déploiement et les outils. Pour plus d’informations sur les modèles de déploiement, consultez [Modèles de déploiement Azure](../azure-classic-rm.md).

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels que vous connectez peuvent être situés dans différentes régions. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma ci-dessous.


![Schéma de connectivité de réseau virtuel à réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 
## Pourquoi connecter des réseaux virtuels ?

Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

- **Géo-redondance et présence géographique dans plusieurs régions**
	- Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
	- Avec l’équilibrage de charge Azure et les technologies de clustering Microsoft ou tierces, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.

- **Applications multiniveaux régionales avec une forte limite d’isolement**
	- Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés avec une isolation renforcée et une communication sécurisée entre les niveaux.

### Points à noter

Cet article vous guide lors de la connexion de deux réseaux virtuels : VNet1 et VNet2. Vous devez disposer d’une bonne connaissance de la mise en réseau pour remplacer les plages d’adresses IP qui sont compatibles avec les exigences liées à la conception de votre réseau.


![Connexion de réseau virtuel à réseau virtuel](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)


- Les réseaux virtuels peuvent être situés dans des régions (emplacements) identiques ou différentes.

- Un service cloud ou un point de terminaison d’équilibrage de charge NE PEUT PAS s’étendre sur différents réseaux virtuels, même si ces derniers sont interconnectés.

- L’interconnexion de plusieurs réseaux virtuels Azure ne requiert PAS de passerelle VPN locale, sauf si la connectivité entre différents locaux est requise.

- La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels. Elle ne prend pas en charge la connexion de machines virtuelles ou de services cloud qui ne sont PAS situés dans un réseau virtuel.

- La connexion de réseau virtuel à réseau virtuel nécessite des passerelles VPN Azure avec VPN de type RouteBased (précédemment Dynamic).

- La connectivité de réseau virtuel peut être utilisée simultanément avec des VPN multisites, avec un maximum de 10 tunnels VPN pour une passerelle VPN de réseau virtuel se connectant à d’autres réseaux virtuels ou à des sites locaux.

- Les espaces d’adressage des réseaux virtuels et sur les sites de réseau locaux ne doivent pas se chevaucher. Le chevauchement des espaces d’adressage entraîne l’échec de la création des réseaux virtuels.

- Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN du réseau virtuel partagent la bande passante disponible sur la passerelle VPN Azure, ainsi que le même contrat SLA concernant le temps d’activité des passerelles VPN dans Azure.

- Le trafic de réseau virtuel à réseau virtuel circule sur la dorsale principale d’Azure.

## Avant tout chose

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure. Si vous ne possédez pas déjà un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou vous inscrire à une [évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/).

- Applets de commande Azure PowerShell 0.9.8. Vous pouvez télécharger et installer cette version à partir de la section Windows PowerShell de la [page de téléchargement](http://azure.microsoft.com/downloads/). Cet article a été écrit pour la version 0.9.8, bien qu’il soit possible d’utiliser cette procédure (en apportant quelques modifications aux applets de commande) avec la version préliminaire PowerShell 1.0.

**À propos de l’utilisation de cette procédure avec la version préliminaire Azure PowerShell 1.0**

	[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)] 


## 1\. Planification de vos plages d’adresses IP


Il est important de choisir les plages que vous utiliserez pour paramétrer votre configuration réseau. Du point de vue de VNet1, VNet2 est simplement une autre connexion VPN définie dans la plateforme Azure. Et pour VNet2, VNet1 est simplement une autre connexion VPN. N’oubliez pas que vous devez vous assurer qu’aucune plage de réseaux virtuels ou de réseaux locaux ne se chevauche.


Dans les étapes suivantes, nous allons créer deux réseaux virtuels avec leurs sous-réseaux de passerelle respectifs et leur configuration. Nous allons ensuite créer une connexion de passerelle VPN entre les deux réseaux virtuels.

Pour cet exercice, utilisez les valeurs suivantes pour les réseaux virtuels :

Valeurs pour VNet1 :

- Nom du réseau virtuel = VNet1
- Groupe de ressources = testrg1
- Espace d’adressage = 10.1.0.0/16 
- Région = Ouest des États-Unis
- Sous-réseau de passerelle = 10.1.0.0/28
- Sous-réseau 1 = 10.1.1.0/28

Valeurs pour VNet2 :

- Nom du réseau virtuel = VNet2
- Groupe de ressources = testrg2
- Espace d’adressage = 10.2.0.0/16
- Région = Est du Japon
- Sous-réseau de passerelle = 10.2.0.0/28
- Sous-réseau 1 = 10.2.1.0/28

## 2\. Connexion à votre abonnement 

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

		Add-AzureAccount

Si vous avez plusieurs abonnements, utilisez *Select-AzureSubscription* pour vous connecter à l’abonnement que vous voulez utiliser.

		Select-AzureSubscription "yoursubscription"

Basculez ensuite sur le mode Azure Resource Manager.
		
		Switch-AzureMode -Name AzureResourceManager

## 3\. Créez un réseau virtuel


Utilisez l’exemple ci-dessous pour créer un réseau virtuel et un sous-réseau de passerelle. Remplacez les valeurs par les vôtres. Dans cet exemple, nous allons créer VNet1. Vous devez répéter ces étapes pour créer VNet2 ultérieurement.

Créez d’abord un groupe de ressources.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

Ensuite, créez votre réseau virtuel L’exemple ci-dessous crée un réseau virtuel nommé *VNet1* et deux sous-réseaux, un nommé *GatewaySubnet* et l’autre *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous le nommez autrement, la configuration de votre connexion échouera. Dans l’exemple ci-dessous, notre sous-réseau de passerelle utilise /28. Vous pouvez choisir d’utiliser un sous-réseau de passerelle jusqu’à /29. Notez que certaines fonctionnalités (comme la connexion coexistante ExpressRoute/site à site) requièrent un sous-réseau de passerelle supérieur à /27. Vous pouvez donc créer votre sous-réseau de passerelle pour prendre en charge d’autres fonctionnalités à utiliser à l’avenir.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 4\. Demander une adresse IP publique


Ensuite, vous demandez qu’une adresse IP publique soit allouée à la passerelle que vous créez pour votre réseau virtuel. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser, car elle est allouée de façon dynamique à votre passerelle. Vous utiliserez cette adresse IP dans la section de configuration suivante.

Utilisez l’exemple ci-dessous. La méthode d’allocation pour cette adresse doit être dynamique.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## 5\. Créer la configuration de la passerelle


La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Créer la passerelle


Dans cette étape, vous allez créer la passerelle de réseau virtuel de votre réseau virtuel. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle peut prendre du temps, soyez donc patient.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Créer VNet2


Une fois que vous avez configuré VNet1, répétez les étapes précédentes pour paramétrer VNet2 et la configuration de sa passerelle. Une fois que vous avez terminé la configuration des deux réseaux virtuels et de leurs passerelles, passez à l’**Étape 8. Connecter les passerelles**.

## 8\. Connecter les passerelles

Dans cette étape, vous allez créer les connexions de la passerelle VPN entre les deux passerelles de réseau virtuel. Une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour cette clé partagée. Il est important que la clé partagée corresponde aux deux configurations.

Lorsque vous créez des connexions, notez que cela peut prendre du temps.

**De VNet1 à VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**de VNet2 à VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    

Après quelques minutes, la connexion doit être établie. Notez qu’à ce stade, les passerelles et les connexions créées avec Azure Resource Manager ne sont pas visibles dans le portail en version préliminaire.

## Vérification des connexions

À ce stade, les connexions VPN créées avec Resource Manager ne sont pas visibles dans la version préliminaire du portail. Vous pouvez néanmoins vérifier que votre connexion a abouti en exécutant la commande *Get-AzureVirtualNetworkGatewayConnection –Debug*. Nous proposerons prochainement à cet effet une applet de commande, avec la possibilité d'afficher votre connexion dans la version préliminaire du portail.

Vous pouvez utiliser l'exemple d'applet de commande suivant. Veillez à modifier les valeurs pour les faire correspondre à chaque connexion que vous souhaitez vérifier. Quand vous y êtes invité, sélectionnez *A* pour exécuter Tout.

		Get-AzureVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 Une fois l'applet de commande exécutée, faites défiler pour afficher les valeurs. Dans l’exemple ci-dessous, l’état de la connexion indique *Connecté*, et vous pouvez voir les octets d’entrée et de sortie.

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

## Connexion de réseaux virtuels existants

Si vous avez déjà créé des réseaux virtuels en mode Azure Resource Manager et que vous souhaitez les connecter, vérifiez les points suivants :

- Vous disposez d’un sous-réseau de passerelle d’au moins /29 ou plus pour chaque réseau virtuel.
- Les plages d’adressage de vos réseaux virtuels ne se chevauchent pas.

Si vous devez ajouter des sous-réseaux de passerelle à vos réseaux virtuels, utilisez l’exemple ci-dessous et remplacez les valeurs par les vôtres. Assurez-vous de nommer le sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la configuration de votre VPN ne fonctionnera pas comme prévu.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

Après avoir vérifié que les sous-réseaux de votre passerelle sont correctement configurés, passez à l’**Étape 4. Demander une adresse IP publique** et suivez les étapes.


## Étapes suivantes

Vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. [Créer une machine virtuelle](../virtual-machines/virtual-machines-windows-tutorial.md).

Pour plus d’informations sur les passerelles VPN, consultez la [FAQ sur la passerelle VPN](vpn-gateway-faq.md).

<!---HONumber=Oct15_HO4-->