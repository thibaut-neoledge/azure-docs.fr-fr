<properties
   pageTitle="Création d’une connexion de passerelle VPN de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell pour des réseaux virtuels | Microsoft Azure"
   description="Cet article vous guide dans l’interconnexion de réseaux virtuels avec Azure Resource Manager et PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="cherylmc"/>

# Configurer une connexion de réseau virtuel à réseau virtuel à l’aide d’Azure Resource Manager et de PowerShell

> [AZURE.SELECTOR]
- [Portail Azure Classic](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Cet article vous guidera au long des étapes de création d’une connexion entre des réseaux virtuels à l’aide du modèle de déploiement Resource Manager et de PowerShell. Les réseaux virtuels peuvent être situés dans des régions identiques ou différentes et appartenir à des abonnements identiques ou différents.


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Outils et modèles de déploiement pour les connexions de réseau virtuel à réseau virtuel**

Une connexion de réseau virtuel à réseau virtuel peut être configurée dans les deux modèles de déploiement et à l’aide de différents outils. Pour plus d’informations, consultez le tableau ci-après. Nous mettons à jour ce tableau à mesure que de nouveaux articles, de nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## À propos des connexions de réseau virtuel à réseau virtuel

La connexion entre deux réseaux virtuels est semblable à la connexion d’un réseau virtuel à un emplacement de site local. Les deux types de connectivité font appel à une passerelle VPN Azure pour offrir un tunnel sécurisé utilisant Ipsec/IKE. Les réseaux virtuels que vous connectez peuvent être situés dans différentes régions. Ou dans différents abonnements. Vous pouvez même combiner une communication de réseau virtuel à réseau virtuel avec des configurations multisites. Vous établissez ainsi des topologies réseau qui combinent une connectivité entre différents locaux et une connectivité entre différents réseaux virtuels, comme indiqué dans le schéma ci-dessous.


![À propos des connexions](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Pourquoi connecter des réseaux virtuels ?

Vous pouvez décider de connecter des réseaux virtuels pour les raisons suivantes :

- **Géo-redondance et présence géographique dans plusieurs régions**
	- Vous pouvez configurer la géo-réplication ou la synchronisation avec une connectivité sécurisée sans passer par les points de terminaison accessibles sur Internet.
	- Avec Traffic Manager et l’équilibreur de charge Azure, vous pouvez configurer une charge de travail hautement disponible avec la géo-redondance dans plusieurs régions Azure. Vous pouvez par exemple configurer SQL Always On avec des groupes de disponibilité répartis dans différentes régions Azure.

- **Applications multiniveaux régionales avec une limite d’isolement ou administrative**
	- Dans la même région, vous pouvez configurer des applications multiniveaux avec plusieurs réseaux virtuels interconnectés pour des besoins d’isolement ou d’administration.


### Forum Aux Questions sur l’interconnexion de réseaux virtuels

- Les réseaux virtuels peuvent être situés dans des régions (emplacements) identiques ou différentes.

- Un service cloud ou un point de terminaison d’équilibrage de charge NE PEUT PAS s’étendre sur différents réseaux virtuels, même si ces derniers sont interconnectés.

- L’interconnexion de plusieurs réseaux virtuels Azure ne requiert pas de passerelle VPN locale, sauf si la connectivité entre différents locaux est requise.

- La connexion de réseau virtuel à réseau virtuel prend en charge la connexion de réseaux virtuels. Elle ne prend pas en charge la connexion de machines virtuelles ou de services cloud qui ne sont PAS situés dans un réseau virtuel.

- La connexion de réseau virtuel à réseau virtuel nécessite des passerelles VPN Azure avec VPN de type RouteBased (précédemment Dynamic Routing, routage dynamique).

- La connectivité de réseau virtuel peut être utilisée simultanément avec des VPN multisites, avec un maximum de 10 tunnels VPN (passerelles par défaut/standard) ou 30 tunnels VPN (passerelles à hautes performances) pour une passerelle VPN de réseau virtuel se connectant à d’autres réseaux virtuels ou à des sites locaux.

- Les espaces d’adressage des réseaux virtuels et sur les sites de réseau locaux ne doivent pas se chevaucher. Le chevauchement des espaces d’adressage entraîne l’échec des connexions de réseau virtuel à réseau virtuel.

- Les tunnels redondants entre deux réseaux virtuels ne sont pas pris en charge.

- Tous les tunnels VPN du réseau virtuel partagent la bande passante disponible sur la passerelle VPN Azure, ainsi que le même contrat SLA concernant le temps d’activité des passerelles VPN dans Azure.

- Le trafic de réseau virtuel à réseau virtuel transite sur le réseau Microsoft, et non sur Internet.

- Le trafic de réseau virtuel à réseau virtuel au sein d’une même région est gratuit dans les deux sens. Le trafic de réseau virtuel à réseau virtuel de sortie entre différentes régions est facturé au tarif du transfert de données sortantes entre réseaux virtuels en fonction des régions sources. Pour plus d’informations, consultez la [page sur la tarification](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## Quelle procédure dois-je utiliser ?

Cet article présente différentes procédures qui s’appliquent aux réseaux virtuels créés à l’aide du modèle de déploiement Resource Manager. Les étapes de configuration de réseau virtuel à réseau virtuel dépendent du fait que les réseaux virtuels figurent dans le même abonnement ou dans différents abonnements.

![Les deux connexions](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Pour les étapes de configuration, la principale différence réside dans le fait que vous puissiez ou non créer et configurer toutes les ressources de passerelle et de réseau virtuel au sein de la même session PowerShell. Ce document décrit les procédures relatives à chaque situation. Le graphique ci-dessus décrit une connexion de réseau virtuel à réseau virtuel dans un même abonnement, et une connexion de réseau virtuel à réseau virtuel entre différents abonnements.


- [Réseaux virtuels situés dans le même abonnement](#samesub)
- [Réseaux virtuels situés dans différents abonnements](#difsub)


## <a name ="samesub"/>Connexion de réseaux virtuels situés dans le même abonnement

Cette configuration s’applique aux réseaux virtuels situés dans le même abonnement, comme illustré dans le schéma ci-dessous :

![Connexion de réseau virtuel à réseau virtuel dans un même abonnement](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Avant de commencer

- Assurez-vous de disposer d’un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
	
- Vous aurez besoin d’installer les applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

### <a name ="Step1"/>Étape 1 : planifiez vos plages d’adresses IP


Il est important de choisir les plages que vous utiliserez pour paramétrer votre configuration réseau. N’oubliez pas que vous devez vous assurer qu’aucune plage de réseaux virtuels ou de réseaux locaux ne se chevauche.

Dans les étapes suivantes, nous allons créer deux réseaux virtuels avec leurs sous-réseaux de passerelle respectifs et leur configuration. Nous allons ensuite créer une connexion de passerelle VPN entre les deux réseaux virtuels.

Pour cet exercice, utilisez les valeurs suivantes pour les réseaux virtuels :

**Valeurs pour TestVNet1 :**

- Nom du réseau virtuel : TestVNet1
- Groupe de ressources : TestRG1
- Emplacement : Est des États-Unis
- TestVNet1 : 10.11.0.0/16 et 10.12.0.0/16
- FrontEnd : 10.11.0.0/24
- BackEnd : 10.12.0.0/24
- GatewaySubnet : 10.12.255.0/27
- Serveur DNS : 8.8.8.8
- GatewayName : VNet1GW
- Adresse IP publique : VNet1GWIP
- Type de VPN : RouteBased
- Connection(1to4) : VNet1toVNet4
- Connection(1to5) : VNet1toVNet5
- Type de connexion : VNet2VNet


**Valeurs pour TestVNet4 :**

- Nom du réseau virtuel : TestVNet4
- TestVNet2 : 10.41.0.0/16 et 10.42.0.0/16
- Serveur frontal : 10.41.0.0/24
- Serveur principal : 10.42.0.0/24
- Sous-réseau de passerelle : 10.42.255.0.0/27
- Groupe de ressources : TestRG4
- Emplacement : États-Unis de l’Ouest
- Serveur DNS : 8.8.8.8
- Nom de la passerelle : VNet4GW
- Adresse IP publique : VNet4GWIP
- Type de VPN : RouteBased
- Connexion : VNet4toVNet1
- Type de connexion : VNet2VNet



### <a name ="Step2"/>Étape 2 : créez et configurez TestVNet1

1. Déclarer vos variables

	Dans cet exercice, nous allons commencer par déclarer les variables. L’exemple suivant déclare les variables avec les valeurs de cet exercice. Veillez à les remplacer par vos valeurs lors de la configuration dans un contexte de production. Vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Modifiez les variables, puis copiez et collez-les dans la console PowerShell.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Se connecter à l’abonnement 1

	Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d'informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

	Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

		Login-AzureRmAccount

	Vérifiez les abonnements associés au compte.

		Get-AzureRmSubscription 

	Spécifiez l’abonnement à utiliser.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Création d’un groupe de ressources

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Créer les configurations de sous-réseau pour TestVNet1

	L’exemple ci-dessous crée un réseau virtuel nommé TestVNet1 et trois sous-réseaux nommés GatewaySubnet, FrontEnd et Backend. Lorsque vous remplacez les valeurs, pensez à toujours nommer votre sous-réseau de passerelle « GatewaySubnet ». Si vous le nommez autrement, la création de votre passerelle échoue.

	Dans l’exemple ci-dessous, notre sous-réseau de passerelle utilise /27. Même si, techniquement, vous pouvez créer un sous-réseau de passerelle à l’aide d’un sous-réseau aussi petit que /29, nous vous déconseillons de le faire. Si possible, nous vous recommandons d’utiliser une valeur plus grande, par exemple /27 ou /26, au cas où vous vouliez utiliser des configurations existantes ou futures qui pourraient nécessiter un sous-réseau de passerelle plus grand.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Créer TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Demander une adresse IP publique

	Ensuite, vous demandez qu’une adresse IP publique soit allouée à la passerelle que vous créez pour votre réseau virtuel. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser, car elle est allouée de façon dynamique à votre passerelle. Vous utiliserez cette adresse IP dans la section de configuration suivante. Utilisez l’exemple ci-dessous. La méthode d’allocation pour cette adresse doit être dynamique.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Créer la configuration de la passerelle

	La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Créer la passerelle pour TestVNet1

	Dans cette étape, vous allez créer la passerelle de réseau virtuel de votre TestVNet1. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle peut prendre un certain temps (30 minutes ou plus).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Étape 3 : créez et configurez TestVNet4

Une fois que vous avez configuré TestVNet1, vous devez répéter les étapes pour créer TestVNet4. Suivez les étapes ci-dessous, en remplaçant les valeurs par les vôtres si nécessaire. Cette étape peut être réalisée dans la même session PowerShell, car elle se trouve dans le même abonnement.

1. Déclarer vos variables

	Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Avant de continuer, assurez-vous que vous êtes toujours connecté à l’abonnement 1.

2. Création d’un groupe de ressources

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Créer les configurations de sous-réseau pour TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Créer TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Demander une adresse IP publique

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Créer la configuration de la passerelle

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Créer la passerelle TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Étape 4 : connectez les passerelles

1. Obtenir les passerelles de réseau virtuel

	Dans cet exemple, étant donné que les deux passerelles se trouvent dans le même abonnement, cette étape peut être effectuée dans la même session PowerShell.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Créer la connexion TestVNet1 à TestVNet4

	Dans cette étape, vous allez créer la connexion de TestVNet1 à TestVNet4. Une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour cette clé partagée. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Créer la connexion TestVNet4 à TestVNet1

	Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet4 à TestVNet1. Vérifiez que les clés partagées correspondent.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Après quelques minutes, la connexion doit être établie.

## <a name ="Verify"/>Vérification d’une connexion de réseau virtuel à réseau virtuel

Les exemples ci-dessous expliquent comment vérifier votre connexion. Veillez à modifier les valeurs pour les faire correspondre à votre environnement.

### Vérification de votre connexion à l'aide du portail Azure

Vous pouvez vérifier une connexion VPN dans le portail Azure en accédant à **Passerelles de réseau virtuel** -> **cliquez sur le nom de votre passerelle** -> **Paramètres** -> **Connexions**. Sélectionnez le nom de la connexion pour afficher des informations supplémentaires dans le panneau **Connexion**.


### Vérification de votre connexion à l'aide de PowerShell

Vous pouvez également vérifier que votre connexion a abouti en exécutant la commande *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Vous pouvez utiliser l’exemple suivant en le configurant avec vos propres valeurs. Quand vous y êtes invité, sélectionnez A pour exécuter Tout.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Une fois l'applet de commande exécutée, faites défiler pour afficher les valeurs. Dans l’exemple de sortie PowerShell ci-dessous, l’état de la connexion indique *Connecté* et vous pouvez voir les octets d’entrée et de sortie.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>Connexion de réseaux virtuels situés dans différents abonnements

Les étapes de configuration ci-dessous ajoutent une connexion de réseau virtuel à réseau virtuel supplémentaire pour connecter TestVNet1 à TestVNet5, qui se trouve dans un autre abonnement. La différence réside dans le fait qu’une partie des étapes de configuration doit être exécutée dans une session PowerShell distincte dans le contexte d’un deuxième abonnement, notamment lorsque les deux abonnements appartiennent à différentes organisations. À la fin de la procédure ci-dessous, la configuration obtenue est illustrée dans le schéma ci-dessous :

![Connexion de réseau virtuel à réseau virtuel dans différents abonnements](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Les instructions ci-dessous sont la suite des étapes précédentes répertoriées plus haut. Vous devez terminer les étapes [1](#Step1) et [2](#Step2) pour créer et configurer TestVNet1 et la passerelle VPN pour TestVNet1. Si vous souhaitez connecter uniquement des réseaux virtuels dans différents abonnements, vous pouvez ignorer les étapes 3 et 4 de l’exercice précédent et passer directement à l’étape 5 ci-dessous.

### Étape 5 : vérifiez les plages d’adresses IP supplémentaires

Il est important de s’assurer que l’espace d’adressage IP du nouveau réseau virtuel TestVNet5 ne chevauche aucune de vos plages de réseaux virtuels ou de passerelles de réseau locales.

Dans cet exemple, les réseaux virtuels peuvent appartenir à différentes organisations. Pour cet exercice, vous pouvez utiliser les valeurs suivantes pour TestVNet5 :

**Valeurs pour TestVNet5 :**

- Nom du réseau virtuel : TestVNet5
- Groupe de ressources : TestRG5
- Emplacement : Japon de l’Est
- TestVNet5 : 10.51.0.0/16 et 10.52.0.0/16
- Serveur frontal : 10.51.0.0/24
- Serveur principal : 10.52.0.0/24
- Sous-réseau de passerelle : 10.52.255.0.0/27
- Serveur DNS : 8.8.8.8
- Nom de la passerelle : VNet5GW
- Adresse IP publique : VNet5GWIP
- Type de VPN : RouteBased
- Connexion : VNet5toVNet1
- Type de connexion : VNet2VNet

**Valeurs supplémentaires pour TestVNet1 :**

- Connexion : VNet1toVNet5


### Étape 6 : créez et configurez TestVNet5

Cette étape doit être effectuée dans le cadre du nouvel abonnement. Cette partie peut être effectuée par l’administrateur dans une organisation différente qui possède l’abonnement.

1. Déclarer vos variables

	Veillez à remplacer les valeurs par celles que vous souhaitez utiliser pour votre configuration.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Se connecter à l’abonnement 5

	Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

		Login-AzureRmAccount

	Vérifiez les abonnements associés au compte.

		Get-AzureRmSubscription 

	Spécifiez l’abonnement à utiliser.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Création d’un groupe de ressources

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Créer les configurations de sous-réseau pour TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Créer TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Demander une adresse IP publique

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Créer la configuration de la passerelle

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Créer la passerelle TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Étape 7 : connectez les passerelles

Dans cet exemple, étant donné que les passerelles se trouvent dans différents abonnements, nous avons divisé cette étape en deux sessions PowerShell notées [Abonnement 1] et [Abonnement 5].

1. **[Abonnement 1]** Obtenir la passerelle de réseau virtuel pour Abonnement 1

	Veillez à ouvrir une session et à vous connecter à Abonnement 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 5 par message électronique ou une autre méthode.

		$vnet1gw.Name
		$vnet1gw.Id

	Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Abonnement 5]** Obtenir la passerelle de réseau virtuel pour Abonnement 5

	Veillez à ouvrir une session et à vous connecter à Abonnement 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Copiez la sortie des éléments suivants et envoyez-la à l’administrateur d’Abonnement 1 par message électronique ou une autre méthode.

		$vnet5gw.Name
		$vnet5gw.Id

	Ces deux éléments ont des valeurs similaires à l’exemple de sortie suivant :

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Abonnement 1]** Créer la connexion TestVNet1 à TestVNet5

	Dans cette étape, vous allez créer la connexion de TestVNet1 à TestVNet5. La différence réside dans le fait que $vnet5gw ne peut pas être obtenu directement, car il se trouve dans un abonnement différent. Vous devez créer un objet PowerShell avec les valeurs communiquées par Abonnement 1 dans les étapes précédentes. Remplacez le nom, l’ID et la clé partagée par vos propres valeurs. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.

	Veillez à vous connecter à Abonnement 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Abonnement 5]** Créer la connexion TestVNet5 à TestVNet1

	Cette étape est similaire à celle présentée ci-dessus, sauf que vous créez la connexion de TestVNet5 à TestVNet1. La procédure de création d’un objet PowerShell basé sur les valeurs obtenues à partir d’Abonnement 1 s’applique également ici. Dans cette étape, vérifiez que les clés partagées correspondent.

	Veillez à vous connecter à Abonnement 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Vérification de votre connexion

	Après avoir effectué ces étapes, vous pouvez vérifier votre connexion à l’aide des méthodes décrites dans [Vérification d’une connexion de réseau virtuel à réseau virtuel](#Verify).

## Étapes suivantes

Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Consultez [Création d’une machine virtuelle](../virtual-machines/virtual-machines-windows-hero-tutorial.md) pour connaître les différentes étapes.

<!---HONumber=AcomDC_0427_2016-->