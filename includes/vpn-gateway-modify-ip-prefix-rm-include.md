### <a name="noconnection"></a>Ajout ou suppression de préfixes sans connexion de passerelle

- **Pour ajouter** des préfixes d’adresses à une passerelle de réseau local que vous avez créée, mais qui ne dispose pas encore d’une connexion de passerelle, utilisez l’exemple ci-dessous. Veillez à remplacer les valeurs par les vôtres.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Pour supprimer** un préfixe d’adresse d’une passerelle de réseau local ne disposant pas d’une connexion VPN, utilisez l’exemple ci-dessous. Abandonnez les préfixes dont vous n'avez plus besoin. Dans cet exemple, le préfixe 20.0.0.0/24 (de l’exemple précédent) n’est plus nécessaire. Nous allons donc modifier la passerelle de réseau local et exclure ce préfixe.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Ajout ou suppression de préfixes avec une connexion de passerelle existante

Si vous avez créé votre connexion de passerelle et souhaitez ajouter ou supprimer des préfixes d’adresses IP contenues dans votre passerelle de réseau local, vous devez effectuer les étapes suivantes dans l’ordre. Cela entraînera une interruption de votre connexion VPN. Lors de la mise à jour de vos préfixes, vous devez d’abord supprimer la connexion, modifier les préfixes, puis créer une connexion. Dans les exemples ci-dessous, veillez à remplacer les valeurs par les vôtres.

>[AZURE.IMPORTANT] Ne supprimez pas la passerelle VPN. Si vous la supprimez, vous devrez reprendre toute la procédure de création et reconfigurer votre routeur local avec les nouveaux paramètres.
 
1. Supprimez la connexion.

		Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modifiez les préfixes d’adresse de votre passerelle de réseau local.

	Définissez la variable pour LocalNetworkGateway.

		$local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

	Modifiez les préfixes.

		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
		-AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Créez la connexion. Dans cet exemple, nous allons configurer un type de connexion IPsec. Lorsque vous recréez votre connexion, utilisez le type de connexion spécifié pour votre configuration. Pour les autres types de connexion, consultez la page sur [les applets de commande PowerShell](https://msdn.microsoft.com/library/mt603611.aspx).

 	Définissez la variable pour VirtualNetworkGateway.

		$gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

	Créez la connexion. Notez que cet exemple utilise la variable $local que vous avez définie à l’étape précédente.


		New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
		-ResourceGroupName MyRGName -Location 'West US' `
		-VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
		-ConnectionType IPsec `
		-RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0810_2016-->