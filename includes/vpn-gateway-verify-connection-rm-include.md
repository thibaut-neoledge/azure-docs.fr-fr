Vous pouvez vérifier une connexion VPN dans le Portail Azure en accédant à **Passerelles de réseau virtuel** **>** ***cliquez sur le nom de votre passerelle*** **>** **Paramètres** **>** **Connexions**. Sélectionnez le nom de la connexion pour afficher des informations supplémentaires concernant la connexion. Dans l’exemple ci-dessous, la connexion n’est pas activée et aucune donnée ne transite.


![Vérifier la connexion](./media/vpn-gateway-verify-connection-rm-include/connectionverify450.png)


### Vérification de votre connexion à l'aide de PowerShell

Il est également possible de vérifier que votre connexion a réussi à l’aide de `Get-AzureRmVirtualNetworkGatewayConnection –Debug`. Vous pouvez utiliser l'exemple d'applet de commande suivant, en le configurant selon vos propres valeurs. Quand vous y êtes invité, sélectionnez A pour exécuter Tout.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Une fois l'applet de commande exécutée, faites défiler pour afficher les valeurs. Dans l’exemple ci-dessous, l’état de la connexion indique *Connecté* et vous pouvez voir les octets d’entrée et de sortie.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }