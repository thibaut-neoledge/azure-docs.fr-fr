## Déployer le modèle ARM à l'aide de PowerShell

Pour déployer le modèle ARM téléchargé à l'aide de PowerShell, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Exécutez l'applet de commande **Switch-AzureMode** pour passer en mode Resource Manager, comme illustré ci-dessous.

		Switch-AzureMode AzureResourceManager

	Voici le résultat attendu pour la commande ci-dessus :

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]L’applet de commande Switch-AzureMode sera bientôt obsolète. Lorsque ce sera le cas, toutes les applets de commande Resource Manager seront renommées.

3. Au besoin, exécutez l’applet de commande **New-AzureResourceGroup** pour créer un groupe de ressources. La commande suivante crée un groupe de ressources nommé *TestRG* dans la région Azure *Centre des États-Unis*. Pour plus d'informations sur les groupes de ressources, consultez la [Vue d’ensemble d'Azure Resource Manager](resource-group-overview.md).

		New-AzureResourceGroup -Name TestRG -Location centralus
		
	Voici le résultat attendu pour la commande ci-dessus :

		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG

4. Exécutez l’applet de commande **New-AzureResourceGroupDeployment** pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut.

		New-AzureResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
			-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
			
	Voici le résultat attendu pour la commande ci-dessus :
		
		DeploymentName    : TestVNetDeployment
		ResourceGroupName : TestRG
		ProvisioningState : Succeeded
		Timestamp         : 8/14/2015 9:40:00 PM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
		                    Name             Type                       Value
		                    ===============  =========================  ==========
		                    location         String                     Central US
		                    vnetName         String                     TestVNet
		                    addressPrefix    String                     192.168.0.0/16
		                    subnet1Prefix    String                     192.168.1.0/24
		                    subnet1Name      String                     FrontEnd
		                    subnet2Prefix    String                     192.168.2.0/24
		                    subnet2Name      String                     BackEnd
		
		Outputs           :

5. Exécutez l’applet de commande **Get-AzureVirtualNetwork** pour afficher les propriétés du nouveau réseau virtuel, comme illustré ci-dessous.


		Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		
	Voici le résultat attendu pour la commande ci-dessus :
		
		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"
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
		                        "Etag": "W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2ed52eec-8c92-471f-b43b-2914d69f3f04"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Sept15_HO4-->