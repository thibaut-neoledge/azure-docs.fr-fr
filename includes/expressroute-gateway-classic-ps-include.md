Vous devez créer un réseau virtuel et un sous-réseau de passerelle avant d’effectuer les tâches suivantes. Pour plus d’informations, consultez [Configurer un réseau virtuel à l’aide du portail Azure Classic](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) .   

## <a name="add-a-gateway"></a>Ajout d’une passerelle
Utilisez la commande suivante pour créer une passerelle. Veillez à remplacer les valeurs pas les vôtres.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Vérification de la création de la passerelle
Utilisez la commande suivante pour vérifier que la passerelle a été créée. Cette commande récupère également l’ID de passerelle dont vous avez besoin pour d’autres opérations.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionner une passerelle
Il existe un certain nombre de [Références (SKU) de passerelle](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Vous pouvez utiliser la commande suivante pour modifier la référence de passerelle à tout moment.

> [!IMPORTANT]
> Cette commande ne fonctionne pas pour la passerelle UltraPerformance. Pour modifier votre passerelle en passerelle UltraPerformance, commencez par supprimer la passerelle ExpressRoute, puis créez une passerelle UltraPerformance. Pour mettre à niveau votre passerelle à partir d’une passerelle UltraPerformance, commencez par supprimer la passerelle UltraPerformance, puis créez-en une. 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Supprimer une passerelle
Utilisez la commande suivante pour supprimer une passerelle.

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>