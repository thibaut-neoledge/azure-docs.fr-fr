## Déployer le modèle ARM à l'aide de l'interface de ligne de commande Azure

Pour déployer le modèle ARM que vous avez téléchargé à l’aide de l’interface de ligne de commande Azure, suivez les étapes ci-dessous.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](xplat-cli-install.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **`azure config mode`** pour passer en mode Resource Manager, comme illustré ci-dessous.

		azure config mode arm

	Voici le résultat attendu pour la commande ci-dessus :

		info:    New mode is arm

3. Si nécessaire, exécutez la commande **`azure group create`** pour créer un groupe de ressources, comme indiqué ci-dessous. Observez le résultat de la commande. La liste affichée après le résultat présente les différents paramètres utilisés. Pour plus d'informations sur les groupes de ressources, consultez la [Présentation d'Azure Resource Manager](resource-group-overview.md).

		azure group create -n TestRG -l centralus

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (ou --name)**. Nom du nouveau groupe de ressources. Pour notre scénario, *TestRG*.
	- **-l (ou --location)**. Région Azure où le nouveau groupe de ressources sera créé. Pour notre scénario, *centralus*.

4. Exécutez l’applet de commande **`azure group deployment create`** pour déployer le nouveau réseau virtuel à l’aide du modèle et des fichiers de paramètres que vous avez téléchargés et modifiés plus haut. La liste affichée après le résultat présente les différents paramètres utilisés.

		azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command group deployment create
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "TestVNetDeployment"
		+ Registering providers
		info:    Registering provider microsoft.network
		+ Waiting for deployment to complete
		data:    DeploymentName     : TestVNetDeployment
		data:    ResourceGroupName  : TestRG
		data:    ProvisioningState  : Succeeded
		data:    Timestamp          : 2015-08-14T21:56:11.152759Z
		data:    Mode               : Incremental
		data:    Name           Type    Value
		data:    -------------  ------  --------------
		data:    location       String  Central US
		data:    vnetName       String  TestVNet
		data:    addressPrefix  String  192.168.0.0/16
		data:    subnet1Prefix  String  192.168.1.0/24
		data:    subnet1Name    String  FrontEnd
		data:    subnet2Prefix  String  192.168.2.0/24
		data:    subnet2Name    String  BackEnd
		info:    group deployment create command OK

	- **-g (ou --resource-group)**. Nom du groupe de ressources dans lequel sera créé le réseau virtuel.
	- **-f (ou --template-file)**. Chemin d'accès à votre fichier de modèle ARM.
	- **-e (ou --parameters-file)**. Chemin d'accès à votre fichier de paramètres ARM.

5. Exécutez la commande **`azure network vnet show`** pour afficher les propriétés du nouveau réseau virtuel, comme illustré ci-dessous.

		azure network vnet show -g TestRG -n TestVNet

	Voici le résultat attendu pour la commande ci-dessus :

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=AcomDC_0211_2016-->