Si vous devez modifier les préfixes de votre site local, utilisez les instructions ci-dessous. Deux jeux d'instructions sont fournis et varient selon que vous avez déjà créé votre connexion à la passerelle VPN.

### Ajouter ou supprimer des préfixes sans une connexion à la passerelle VPN


- **Pour ajouter** d’autres préfixes d’adresses à un site local que vous avez créé, mais qui ne dispose pas encore d’une connexion à la passerelle VPN, utilisez l’exemple ci-dessous.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Pour supprimer** un préfixe d’adresse d’un site local ne disposant pas d’une connexion VPN, utilisez l’exemple ci-dessous. Abandonnez les préfixes dont vous n'avez plus besoin. Dans cet exemple, le préfixe 20.0.0.0/24 (de l'exemple précédent) n'est plus nécessaire. Nous allons donc modifier le site local et exclure ce préfixe.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Ajouter ou supprimer des préfixes avec une connexion à la passerelle VPN

Si vous avez créé votre connexion VPN et que vous souhaitez ajouter ou supprimer des préfixes d'adresses IP contenues dans votre site local, vous devez effectuer les étapes suivantes dans l'ordre. Cela interrompra votre connexion VPN car vous devrez supprimer et recréer la passerelle. Toutefois, étant donné que vous avez demandé une adresse IP pour la connexion, vous n’aurez pas besoin de reconfigurer votre routeur VPN local, sauf si vous décidez de modifier les valeurs que vous avez déjà utilisées.

 
1. Supprimez la connexion à la passerelle. 
2. Modifiez les préfixes de votre site local. 
3. Créez une connexion de passerelle. 

Vous pouvez utiliser l'exemple suivant comme référence.


	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0107_2016-->