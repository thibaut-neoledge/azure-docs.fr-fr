Les étapes de cette tâche utilisent un réseau virtuel basé sur les valeurs ci-dessous. Les noms et paramètres supplémentaires sont également présentés dans cette liste. Nous n’utilisons pas cette liste directement dans la procédure, mais nous ajoutons des variables en fonction des valeurs dans cette liste. Vous pouvez copier la liste et l’utiliser en tant que référence, en remplaçant les valeurs par les vôtres.

Liste de référence de configuration :
	
- Nom du réseau virtuel : « TestVNet »
- Espace d’adressage du réseau virtuel : 192.168.0.0/16
- Groupe de ressources : « TestRG »
- Nom du sous-réseau 1 : « FrontEnd »
- Espace d’adressage du sous-réseau 1 : « 192.168.0.0/16 »
- Nom de sous-réseau de passerelle : « GatewaySubnet » Vous devez toujours nommer un sous-réseau de passerelle *GatewaySubnet*.
- Espace d'adressage du sous-réseau de passerelle : « 192.168.200.0/26 »
- Région : « Est des États-Unis »
- Nom de la passerelle : « GW »
- Nom d’adresse IP de la passerelle : « GWIP »
- Nom de configuration IP de la passerelle : « gwipconf »
-  Type : « ExpressRoute » Ce type est requis pour une configuration ExpressRoute.
- Nom d’adresse IP publique de passerelle = « gwpip »


## Ajout d’une passerelle

1. Connectez-vous à votre abonnement Azure.

		Login-AzureRmAccount
		Get-AzureRmSubscription 
		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Déclarez vos variables pour cet exercice. Cet exemple utilise les variables dans l’exemple ci-dessous. Veillez à les modifier pour refléter les paramètres que vous souhaitez utiliser.
		
		$RG = "TestRG"
		$Location = "East US"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
		$VNetName = "TestVNet"

3. Stockez l’objet de réseau virtuel en tant que variable.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Ajoutez un sous-réseau de passerelle à votre réseau virtuel. Le sous-réseau de passerelle doit être nommé « GatewaySubnet ». Vous devez créer une passerelle définie sur /27 ou plus (/26, /25, etc.).
			
		Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Définissez la configuration.

			Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Stockez le sous-réseau de passerelle en tant que variable.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Demandez une adresse IP publique L’adresse IP est demandée avant de créer la passerelle. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser, car elle est allouée de façon dynamique. Vous utiliserez cette adresse IP dans la section de configuration suivante. La méthode AllocationMethod doit être dynamique.

		$pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Créez la configuration de votre passerelle. La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Dans cette étape, vous spécifiez la configuration qui sera utilisée lors de la création de la passerelle. Cette étape ne crée pas réellement l’objet de passerelle. Utilisez l’exemple ci-dessous pour créer la configuration de votre passerelle.

		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Créez la passerelle. Dans cette étape, le type **-GatewayType** est particulièrement important. Vous devez utiliser la valeur **ExpressRoute**. Notez qu’après l’exécution de ces applets de commande, la création de la passerelle peut prendre 20 minutes ou plus.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## Vérification de la création de la passerelle

Utilisez la commande suivante pour vérifier que la passerelle a été créée.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## Redimensionner une passerelle

Il existe un certain nombre de [Références (SKU) de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence de passerelle à tout moment.

>[AZURE.IMPORTANT] Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle en passerelle UltraPerformance, commencez par supprimer la passerelle ExpressRoute, puis créez une passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, commencez par supprimer la passerelle UltraPerformance, puis créez-en une.

	$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
	Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## Supprimer une passerelle

Utilisez la commande suivante pour supprimer une passerelle.

	Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  

<!---HONumber=AcomDC_0928_2016-->