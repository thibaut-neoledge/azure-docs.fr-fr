Pour modifier l’adresse IP de la passerelle, utilisez l’applet de commande `New-AzureRmVirtualNetworkGatewayConnection` Tant que vous conservez le nom existant de la passerelle de réseau local, les paramètres sont remplacés. À l’heure actuelle, l’applet de commande « Set » ne prend pas en charge la modification de l’adresse IP de la passerelle.

### <a name="a-namegwipnoconnectionahow-to-modify-the-gateway-ip-address-no-gateway-connection"></a><a name="gwipnoconnection"></a>Modification de l’adresse IP de la passerelle sans connexion de passerelle
Pour mettre à jour l’adresse IP de votre passerelle de réseau local si celle-ci ne dispose pas encore de connexion, utilisez l’exemple ci-dessous. Vous pouvez également mettre à jour les préfixes d’adresse en même temps. Les paramètres que vous spécifiez remplacent les paramètres existants. Veillez à utiliser le nom existant de votre passerelle de réseau local. Si vous ne le faites pas, vous créerez une passerelle de réseau local au lieu de remplacer la passerelle existante.

Utilisez l’exemple suivant en remplaçant les valeurs par les vôtres.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="a-namegwipwithconnectionahow-to-modify-the-gateway-ip-address-existing-gateway-connection"></a><a name="gwipwithconnection"></a>Modification de l’adresse IP de la passerelle avec une connexion de passerelle existante
S’il existe déjà une connexion de passerelle, vous devez tout d’abord supprimer la connexion. Ensuite, vous pouvez modifier l’adresse IP de la passerelle et recréer une connexion. Cela entraînera une interruption de votre connexion VPN.

> [!IMPORTANT]
> Ne supprimez pas la passerelle VPN. Si vous la supprimez, vous devrez reprendre toute la procédure de création et reconfigurer votre routeur local avec l’adresse IP qui sera attribuée à la nouvelle passerelle.
> 
> 

1. Supprimez la connexion. Vous pouvez trouver le nom de votre connexion à l’aide de l’applet de commande `Get-AzureRmVirtualNetworkGatewayConnection` .
   
        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName
2. Modifiez la valeur de GatewayIpAddress. Vous pouvez également modifier vos préfixes d’adresse à ce stade si nécessaire. Notez que les paramètres de la passerelle de réseau local existante seront remplacés. Lors de la modification, utilisez le nom existant de votre passerelle de réseau local pour que les paramètres soient remplacés. Si vous ne le faites pas, vous créerez une passerelle de réseau local au lieu de modifier la passerelle existante.
   
        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName
3. Créez la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion spécifié pour votre configuration. Pour les autres types de connexion, consultez la page sur les [applets de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Pour obtenir le nom de VirtualNetworkGateway, vous pouvez exécuter l’applet de commande `Get-AzureRmVirtualNetworkGateway` .
   
    Définissez les variables :
   
        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName
   
    Créez la connexion :
   
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'



<!--HONumber=Nov16_HO2-->


