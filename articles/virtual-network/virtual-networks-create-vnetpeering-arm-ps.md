<properties
   pageTitle="Créer une homologation de réseaux virtuels à l’aide d’applets de commande PowerShell | Microsoft Azure"
   description="Découvrez comment créer un réseau virtuel à l’aide du portail Azure dans Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Créer une homologation de réseaux virtuels à l’aide d’applets de commande PowerShell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer une homologation de réseaux virtuels à l’aide de PowerShell, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, voir [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivre les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

Remarque : L’applet de commande PowerShell pour la gestion de l’homologation de réseaux virtuels est fournie avec [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0).

2. Lisez les objets de réseau virtuel :

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

	
3. Pour établir une homologation de réseaux virtuels, vous devez créer un lien pour chaque direction. L’étape suivante permet de créer tout d’abord le lien d’homologation de réseaux virtuels de VNet1 à VNet2 :

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.id 

La sortie est la suivante :

        Name			: LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

4. Cette étape permet de créer le lien d’homologation de réseaux virtuels de VNet2 à VNet1 :

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.id 

La sortie est la suivante :

        Name			: LinkToVNet1
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet2
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

5. Une fois ce lien d’homologation de réseaux virtuels créé, l’état du lien apparaît comme suit :

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

La sortie est la suivante :

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null


Il existe plusieurs propriétés configurables pour l’homologation de réseaux virtuels :

|Option|Description|Default|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Indique si l’espace d’adressage du réseau virtuel homologue doit être inclus dans le cadre de la balise Virtual\_network.|Oui|
|AllowForwardedTraffic|Spécifie si le trafic ne provenant pas du réseau virtuel homologué doit être accepté ou rejeté.|Non|
|AllowGatewayTransit|Permet au réseau virtuel homologue d’utiliser votre passerelle de réseau virtuel.|Non|
|UseRemoteGateways|Permet d’utiliser la passerelle de votre réseau virtuel homologue. Une passerelle doit être configurée pour le réseau virtuel homologue et la propriété AllowGatewayTransit doit être sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle configurée.|Non|

Chaque lien de l’homologation de réseaux virtuels présente plusieurs des propriétés ci-dessus. Par exemple, vous pouvez définir AllowVirtualNetworkAccess sur True pour l’homologation de réseaux virtuels de VNet1 à VNet2 et lui attribuer la valeur False pour le lien d’homologation de réseaux virtuels dans l’autre direction.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

Pour vérifier la valeur de la propriété après la modification, vous pouvez exécuter Get-AzureRmVirtualNetworkPeering. Dans la sortie, vous pouvez voir que la propriété AllowForwardedTraffic est remplacée par True après l’exécution des applets de commande ci-dessus.

        Name			: LinkToVNet2
        Id			: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState	: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic		: True
        AllowGatewayTransit		: False
        UseRemoteGateways		: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

Une fois l’homologation créée dans ce scénario, vous devez être en mesure d’établir des connexions de n’importe quelle machine virtuelle à n’importe quelle machine virtuelle des deux réseaux virtuels. Par défaut, la propriété AllowVirtualNetworkAccess est définie sur True et l’homologation de réseaux virtuels configure les listes de contrôle d’accès appropriées pour permettre la communication entre les réseaux virtuels. Mais vous pouvez toujours appliquer les règles de groupe de sécurité réseau pour bloquer la connectivité entre par exemple des sous-réseaux ou des machines virtuelles spécifiques afin d’avoir un contrôle précis de l’accès entre deux réseaux virtuels. Pour plus d’informations sur la création de règles de groupe de sécurité réseau, reportez-vous à [cet article](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer une homologation de réseaux virtuels entre différents abonnements à l’aide de PowerShell, suivez les étapes ci-dessous :

1. Connectez-vous à Azure en tant qu’utilisateur privilégié UserA de l’abonnement Subscription-A et exécutez l’applet de commande :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Il ne s’agit pas d’une obligation : l’homologation peut être établie si des utilisateurs ont effectué des demandes d’homologation individuelles pour leurs réseaux respectifs, à condition que les demandes correspondent. L’ajout de l’utilisateur privilégié de l’autre réseau virtuel en tant qu’utilisateur du réseau virtuel local facilite la configuration.

2. Connectez-vous à Azure en tant qu’utilisateur privilégié UserB de l’abonnement Subscription-B et exécutez l’applet de commande :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Ensuite, dans la session de connexion de l’utilisateur UserA, exécutez l’applet de commande ci-dessous :

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess


4. Ensuite, dans la session de connexion de l’utilisateur UserB, exécutez l’applet de commande ci-dessous :

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Une fois l’homologation établie, n’importe quelle machine virtuelle de VNet3 doit pouvoir communiquer avec n’importe quelle machine virtuelle de VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, vous pouvez exécuter les applets de commande PowerShell ci-dessous pour établir l’homologation de réseaux virtuels, en veillant bien à définir la propriété AllowForwardedTraffic sur True pour le lien de VNET1 à HubVNet afin d’autoriser le trafic entrant ne provenant pas de l’espace d’adressage du réseau virtuel homologue.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.id

2. Une fois l’homologation établie, vous pouvez vous reporter à [cet article](virtual-network-create-udr-arm-ps.md) et créer un itinéraire défini par l’utilisateur (UDR) pour rediriger le trafic du réseau virtuel VNet1 via une appliance virtuelle afin d’utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez la définir sur l’adresse IP de l’appliance virtuelle du réseau virtuel homologue HubVNet. Voici un exemple :

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1


## Supprimer l’homologation de réseaux virtuels

1.	Afin de supprimer l’homologation de réseaux virtuels, vous devez exécuter l’applet de commande

        Remove-AzureRmVirtualNetworkPeering  
    
    pour supprimer les deux liens, comme illustré ci-dessous :

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 

3. Une fois que vous supprimez un lien de l’homologation de réseaux virtuels, l’état du lien d’homologation passe à Disconnected (Déconnecté).
  
4. Dans cet état, vous ne pouvez pas recréer le lien tant que l’état du lien d’homologation n’est pas défini sur Initiated (Initialisé). Nous vous recommandons de supprimer les deux liens avant de recréer l’homologation de réseaux virtuels.

<!---HONumber=AcomDC_0803_2016-->