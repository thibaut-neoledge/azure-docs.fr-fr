## Comment créer un réseau virtuel à l’aide de PowerShell

Pour créer un réseau virtuel à l’aide de PowerShell, procédez comme suit :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. À partir d’une invite de commandes Azure PowerShell, exécutez l’applet de commande **Switch-AzureMode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		Switch-AzureMode AzureResourceManager
	
	Sortie attendue :

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]L’applet de commande Switch-AzureMode sera bientôt obsolète. Lorsque ce sera le cas, toutes les applets de commande Resource Manager seront renommées.
	
3. Au besoin, exécutez l’applet de commande **New-AzureResourceGroup** pour créer un groupe de ressources, comme illustré ci-dessous. Dans le cadre de notre scénario, créez un groupe de ressources nommé *TestRG*. Pour plus d'informations sur les groupes de ressources, consultez la [Présentation d'Azure Resource Manager](resource-group-overview.md/#resource-groups).

		New-AzureResourceGroup -Name TestRG -Location centralus

	Sortie attendue :
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG	

4. Exécutez l’applet de commande **New-AzureVirtualNetwork** pour créer un réseau virtuel, comme illustré ci-dessous.

		New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Sortie attendue :

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. Exécutez l’applet de commande **Get-AzureVirtualNetwork** pour stocker l’objet réseau virtuel dans une variable, comme illustré ci-dessous.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]Vous pouvez combiner les étapes 4 et 5 en exécutant **$vnet = New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus**.

6. Exécutez l’applet de commande **Add-AzureVirtualNetworkSubnetConfig** pour ajouter un sous-réseau pour le nouveau réseau virtuel, comme illustré ci-dessous.

		Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Sortie attendue :

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. Répétez l’étape 6 ci-dessus pour chaque sous-réseau à créer. La commande ci-dessous crée le sous-réseau *BackEnd* pour notre scénario.

		Add-AzureVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Bien que vous créiez des sous-réseaux, ils n’existent actuellement que dans la variable locale utilisée pour récupérer le réseau virtuel créé à l’étape 4 ci-dessus. Pour enregistrer les modifications dans Azure, exécutez l’applet de commande **Set-AzureVirtualNetwork**, comme illustré ci-dessous.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet	
		
	Sortie attendue :

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Sept15_HO3-->