Vous devez créer un réseau virtuel et un sous-réseau de passerelle avant d’effectuer les tâches suivantes. Pour plus d’informations, consultez [Configurer un réseau virtuel à l’aide du portail Azure Classic](../articles/expressroute/expressroute-howto-vnet-portal-classic.md).

## Ajout d’une passerelle

Utilisez la commande suivante pour créer une passerelle. Veillez à remplacer les valeurs pas les vôtres.

	New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType DynamicRouting -GatewaySKU  Standard

## Vérification de la création de la passerelle

Utilisez la commande suivante pour vérifier que la passerelle a été créée. Cette commande récupère également l’ID de passerelle dont vous avez besoin pour d’autres opérations.

	Get-AzureVirtualNetworkGateway

## Redimensionner une passerelle

Il existe 3 [références de passerelle](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence de passerelle à tout moment.

	Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## Supprimer une passerelle

Utilisez la commande suivante pour supprimer une passerelle.

	Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>

<!---HONumber=AcomDC_0413_2016-->