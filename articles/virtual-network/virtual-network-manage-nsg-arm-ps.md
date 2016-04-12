<properties 
   pageTitle="Gérer des groupes de sécurité réseau à l’aide de PowerShell dans Resource Manager | Microsoft Azure"
   description="Découvrez comment gérer des groupes de sécurité réseau existants à l’aide de PowerShell dans Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="telmos" />

# Gérer les groupes de sécurité réseau avec PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modèle de déploiement classique.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Récupérer des informations

Vous pouvez afficher vos groupes de sécurité réseau existants, récupérer des règles pour un groupe de sécurité réseau existant et découvrir quelles sont les ressources associées à un groupe de sécurité réseau.

### Afficher les groupes de sécurité réseau existants
Pour afficher tous les groupes de sécurité réseau existants d’un abonnement, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

	Get-AzureRmNetworkSecurityGroup

Résultat attendu :

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


Pour afficher la liste des groupes de sécurité réseau dans un groupe de ressources spécifique, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Sortie attendue :

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### Répertorier toutes les règles pour un groupe de sécurité réseau

Pour afficher les règles d’un groupe de sécurité réseau nommé **NSG-FrontEnd**, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Sortie attendue :
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] Vous pouvez également utiliser `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` pour répertorier les règles par défaut du groupe de sécurité réseau **NSG-FrontEnd**.

### Afficher les associations de groupes de sécurité réseau

Pour afficher les ressources associées au groupe de sécurité réseau **NSG-FrontEnd**, exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Recherchez les propriétés **NetworkInterfaces** et **Subnets**, comme indiqué ci-dessous :

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

Dans l’exemple ci-dessus, le groupe de sécurité réseau n’est associé à aucune interface réseau, mais à un sous-réseau nommé **FrontEnd**.

## Gérer les règles

Vous pouvez ajouter des règles à un groupe de sécurité réseau existant, modifier des règles existantes et supprimer des règles.

### Ajouter une règle

Pour ajouter une règle autorisant le trafic **entrant** vers le port **443** à partir de n’importe quelle machine vers le groupe de sécurité réseau **NSG-FrontEnd**, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Exécutez l’applet de commande `Add-AzureRmNetworkSecurityRuleConfig`, comme indiqué ci-dessous.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Pour enregistrer les modifications apportées au groupe de sécurité réseau, exécutez l’applet de commande `Set-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	La sortie attendue affiche uniquement les règles de sécurité :

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Modifier une règle

Pour modifier la règle créée précédemment qui permet d’autoriser le trafic entrant d’**Internet** uniquement, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Exécutez l’applet de commande `Set-AzureRmNetworkSecurityRuleConfig`, comme indiqué ci-dessous.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Pour enregistrer les modifications apportées au groupe de sécurité réseau, exécutez l’applet de commande `Set-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	La sortie attendue affiche uniquement les règles de sécurité :

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Supprimer une règle

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Exécutez l’applet de commande `Remove-AzureRmNetworkSecurityRuleConfig`, comme indiqué ci-dessous.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. Pour enregistrer les modifications apportées au groupe de sécurité réseau, exécutez l’applet de commande `Set-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Sortie attendue affichant uniquement les règles de sécurité, notez que **https-rule** n’est plus répertorié :

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## Gérer les associations

Vous pouvez associer un groupe de sécurité réseau à des cartes réseau et des sous-réseaux. Vous pouvez également dissocier un groupe de sécurité réseau de n’importe quelle ressource à laquelle il est associé.

### Associer un groupe de sécurité réseau à une carte réseau

Pour associer le groupe de sécurité réseau **NSG-FrontEnd** à la carte réseau **TestNICWeb1**, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Exécutez l’applet de commande `Get-AzureRmNetworkInterface` pour récupérer la carte réseau existante et la stocker dans une variable, comme indiqué ci-dessous.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Définissez la propriété **NetworkSecurityGroup** de la variable **NIC** avec la valeur de la variable **NSG**, comme indiqué ci-dessous.

		$nic.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez l’applet de commande `Set-AzureRmNetworkInterface`, comme indiqué ci-dessous.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Sortie attendue affichant uniquement la propriété **NetworkSecurityGroup** :

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### Dissocier un groupe de sécurité réseau d’une carte réseau

Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** de la carte réseau **TestNICWeb1**, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Exécutez l’applet de commande `Get-AzureRmNetworkInterface` pour récupérer la carte réseau existante et la stocker dans une variable, comme indiqué ci-dessous.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Définissez la propriété **NetworkSecurityGroup** de la variable **NIC** avec la valeur **$null**, comme indiqué ci-dessous.

		$nic.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées à la carte réseau, exécutez l’applet de commande `Set-AzureRmNetworkInterface`, comme indiqué ci-dessous.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Sortie attendue affichant uniquement la propriété **NetworkSecurityGroup** :

		NetworkSecurityGroup : null

### Dissocier un groupe de sécurité réseau d’un sous-réseau

Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** du sous-réseau **FrontEnd**, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmVirtualNetwork` pour récupérer le réseau virtuel existant et le stocker dans une variable, comme indiqué ci-dessous.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Exécutez l’applet de commande `Get-AzureRmVirtualNetworkSubnetConfig` pour récupérer le sous-réseau **FrontEnd** et le stocker dans une variable, comme indiqué ci-dessous.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. Définissez la propriété **NetworkSecurityGroup** de la variable **subnet** avec la valeur **$null**, comme indiqué ci-dessous.

		$subnet.NetworkSecurityGroup = $null

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez l’applet de commande `Set-AzureRmVirtualNetwork`, comme indiqué ci-dessous.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Sortie attendue affichant uniquement les propriétés du sous-réseau **FrontEnd**. Notez qu’il n’y a pas de propriété pour **NetworkSecurityGroup** :

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### Association d’un groupe de sécurité réseau à un sous-réseau

Pour réassocier le groupe de sécurité réseau **NSG-FrontEnd** au sous-réseau **FrontEnd**, procédez comme suit.

1. Exécutez l’applet de commande `Get-AzureRmVirtualNetwork` pour récupérer le réseau virtuel existant et le stocker dans une variable, comme indiqué ci-dessous.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Exécutez l’applet de commande `Get-AzureRmVirtualNetworkSubnetConfig` pour récupérer le sous-réseau **FrontEnd** et le stocker dans une variable, comme indiqué ci-dessous.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. Exécutez l’applet de commande `Get-AzureRmNetworkSecurityGroup` pour récupérer le groupe de sécurité réseau existant et le stocker dans une variable, comme indiqué ci-dessous.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. Définissez la propriété **NetworkSecurityGroup** de la variable **subnet** avec la valeur **$null**, comme indiqué ci-dessous.

		$subnet.NetworkSecurityGroup = $nsg

4. Pour enregistrer les modifications apportées au sous-réseau, exécutez l’applet de commande `Set-AzureRmVirtualNetwork`, comme indiqué ci-dessous.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Sortie attendue affichant uniquement la propriété **NetworkSecurityGroup** du sous-réseau **FrontEnd** :

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## Suppression d'un groupe de sécurité réseau

Vous ne pouvez supprimer un groupe de sécurité réseau que s’il n’est associé à aucune ressource. Pour supprimer un groupe de sécurité réseau, procédez comme suit.

1. Pour consulter les ressources associées à un groupe de sécurité réseau, exécutez `azure network nsg show` comme illustré dans [Afficher les associations de groupes de sécurité réseau](#View-NSGs-associations).
2. Si le groupe de sécurité réseau est associé à des cartes réseau, exécutez `azure network nic set` comme illustré dans [Dissocier un groupe de sécurité réseau d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si le groupe de sécurité réseau est associé à un sous-réseau, exécutez `azure network vnet subnet set` comme illustré dans [Dissocier un groupe de sécurité réseau d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) pour chaque sous-réseau.
4. Pour supprimer le groupe de sécurité réseau, exécutez l’applet de commande `Remove-AzureRmNetworkSecurityGroup`, comme indiqué ci-dessous.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] Le paramètre **-Force** garantit que vous n’avez pas besoin de confirmer la suppression.

## Étapes suivantes

- [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.

<!---HONumber=AcomDC_0323_2016-->