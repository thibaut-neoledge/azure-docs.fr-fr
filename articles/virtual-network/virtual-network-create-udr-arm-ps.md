---
title: Contrôle du routage et utilisation des appliances virtuelles dans Resource Manager à l’aide de PowerShell | Microsoft Docs
description: Découvrez comment contrôler le routage et utiliser des appliances virtuelles dans Resource Manager à l’aide de PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial

---
# Création d’itinéraires définis par l’utilisateur (UDR) dans Resource Manager à l’aide de PowerShell
[!INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des itinéraires définis par l'utilisateur (UDR) dans le modèle de déploiement classique](virtual-network-create-udr-classic-ps.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu'elles sont présentées dans ce document, commencez par créer l'environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), cliquez sur **Déployer dans Azure**, remplacez les valeurs des paramètres par défaut si nécessaire, puis suivez les instructions dans le portail.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Création des itinéraires définis par l'utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

[!INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Créez un itinéraire pour que tout le trafic destiné au sous-réseau principal (192.168.2.0/24) soit acheminé vers l’équipement virtuel **FW1** (192.168.0.4).
   
        $route = New-AzureRmRouteConfig -Name RouteToBackEnd `
            -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4
2. Créez une table de routage nommée **UDR-FrontEnd** dans la région **westus** qui contient l’itinéraire créé ci-dessus.
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-FrontEnd -Route $route
3. Créez une variable qui contient le réseau virtuel où se situe le sous-réseau. Dans notre scénario, le réseau virtuel est nommé **TestVNet**.
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
4. Associez la table de routage créée précédemment au sous-réseau **FrontEnd**.
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            -AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

> [!WARNING]
> La sortie de la commande ci-dessus affiche le contenu de l’objet de configuration de réseau virtuel, qui existe uniquement sur l’ordinateur sur lequel vous exécutez PowerShell. Vous devez exécuter l’applet de commande **Set-AzureVirtualNetwork** pour enregistrer ces paramètres dans Azure.
> 
> 

1. Enregistrez la nouvelle configuration de sous-réseau dans Azure.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
   
    Sortie attendue :
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
   
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
                                ...,
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              },
                                ...
                            ]    

## Création des itinéraires définis par l'utilisateur (UDR) pour le sous-réseau Backend
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau Backend selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Créez un itinéraire pour que tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) soit acheminé vers l’équipement virtuel **FW1** (192.168.0.4).
   
        $route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
            -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4
2. Créez une table de routage nommée **UDR-BackEnd** dans la région **uswest** qui contient l’itinéraire créé ci-dessus.
   
        $routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
            -Name UDR-BackEnd -Route $route
3. Associez la table de routage créée précédemment au sous-réseau **BackEnd**.
   
        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
            -AddressPrefix 192.168.2.0/24 -RouteTable $routeTable
4. Enregistrez la nouvelle configuration de sous-réseau dans Azure.
   
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
   
    Sortie attendue :
   
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : westus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              : 
                            Name         Value
                            ===========  =====
                            displayName  VNet 
   
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
                              ...,
                              {
                                "Name": "BackEnd",
                                "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
                                  },
                                  {
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
                                  }
                                ],
                                "NetworkSecurityGroup": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
                                },
                                "RouteTable": {
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
                                },
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## Activer le transfert IP sur FW1
Pour activer le transfert IP sur la carte réseau utilisée par **FW1**, suivez les étapes ci-dessous.

1. Créez une variable qui contient les paramètres de la carte réseau utilisée par FW1. Dans notre scénario, la carte réseau est nommée **NICFW1**.
   
        $nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1
2. Activez le transfert IP et enregistrez les paramètres de la carte réseau.
   
        $nicfw1.EnableIPForwarding = 1
        Set-AzureRmNetworkInterface -NetworkInterface $nicfw1
   
    Sortie attendue :
   
        Name                 : NICFW1
        ResourceGroupName    : TestRG
        Location             : westus
        Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
        Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState    : Succeeded
        Tags                 : 
                               Name         Value                  
                               ===========  =======================
                               displayName  NetworkInterfaces - DMZ
   
        VirtualMachine       : {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
                               }
        IpConfigurations     : [
                                 {
                                   "Name": "ipconfig1",
                                   "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
                                   "PrivateIpAddress": "192.168.0.4",
                                   "PrivateIpAllocationMethod": "Static",
                                   "Subnet": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
                                   },
                                   "PublicIpAddress": {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
                                   },
                                   "LoadBalancerBackendAddressPools": [],
                                   "LoadBalancerInboundNatRules": [],
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
        DnsSettings          : {
                                 "DnsServers": [],
                                 "AppliedDnsServers": [],
                                 "InternalDnsNameLabel": null,
                                 "InternalFqdn": null
                               }
        EnableIPForwarding   : True
        NetworkSecurityGroup : null
        Primary              : True

<!---HONumber=AcomDC_0810_2016-->