---
title: "Homologation de réseaux virtuels Azure - PowerShell | Microsoft Docs"
description: "Découvrez comment créer une homologation de réseaux virtuels au moyen d’Azure PowerShell."
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 57991a59feb784fd2e5277e893110de2a776a060
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Créer une homologation de réseaux virtuels au moyen d’Azure PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Pour créer une homologation de réseaux virtuels à l’aide de PowerShell, suivez les étapes ci-dessous :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.

    > [!NOTE]
    > L’applet de commande PowerShell pour la gestion de l’homologation de réseaux virtuels est fournie avec [Azure PowerShell 1.6](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. Lisez les objets de réseau virtuel :

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. Pour établir une homologation de réseaux virtuels, vous devez créer un lien pour chaque direction. L’étape suivante permet de créer tout d’abord un lien d’homologation de réseaux virtuels de VNet1 à VNet2 :

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    Sortie retournée :
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. Cette étape permet de créer un lien d’homologation de réseaux virtuels de VNet2 à VNet1 :

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    Sortie retournée :

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. Une fois le lien d’homologation de réseaux virtuels créé, entrez la commande suivante pour afficher l’état du lien :

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    Sortie retournée :
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Il existe plusieurs propriétés configurables pour l’homologation de réseaux virtuels :
   
   | Option | Description | Default |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Indique si l’espace d’adressage du réseau virtuel homologue doit être inclus dans le cadre de la balise Virtual_network. |Oui |
   | AllowForwardedTraffic |Spécifie si le trafic ne provenant pas d’un réseau virtuel homologué est accepté ou rejeté. |Non |
   | AllowGatewayTransit |Permet au réseau virtuel homologue d’utiliser votre passerelle de réseau virtuel. |Non |
   | UseRemoteGateways |Permet d’utiliser la passerelle de votre réseau virtuel homologue. Le réseau virtuel homologue doit avoir une passerelle configurée et la propriété AllowGatewayTransit sélectionnée. Vous ne pouvez pas utiliser cette option si vous avez une passerelle configurée. |Non |

    Chaque lien de l’homologation de réseaux virtuels présente l’ensemble des propriétés ci-dessus. Par exemple, vous pouvez définir `AllowVirtualNetworkAccess` sur *True* pour l’homologation de réseaux virtuels de VNet1 à VNet2 et lui attribuer la valeur *False* pour le lien d’homologation de réseaux virtuels dans l’autre direction.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    Pour vérifier la valeur de la propriété après la modification, vous pouvez exécuter `Get-AzureRmVirtualNetworkPeering`. Dans la sortie, vous pouvez voir que la propriété `AllowForwardedTraffic` est remplacée par *True* après l’exécution des applets de commande ci-dessus.

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    Une fois l’homologation établie, les machines virtuelles doivent être en mesure de communiquer entre elles sur les deux réseaux virtuels. Par défaut, la propriété `AllowVirtualNetworkAccess` est définie sur *True* et l’homologation de réseaux virtuels configure les listes de contrôle d’accès appropriées pour permettre la communication entre les réseaux virtuels. Vous pouvez toujours appliquer les règles de groupe de sécurité réseau pour bloquer la connectivité entre des sous-réseaux ou des machines virtuelles spécifiques afin d’avoir un contrôle précis de l’accès entre deux réseaux virtuels. Lisez l’article [Groupes de sécurité réseau](virtual-networks-create-nsg-arm-ps.md) pour en savoir plus sur les groupes de sécurité réseau.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Pour créer une homologation de réseaux virtuels entre des abonnements à l’aide de PowerShell, suivez les étapes ci-dessous :

1. Connectez-vous à Azure avec le compte de l’utilisateur privilégié UserA de l’abonnement Subscription-A et exécutez l’applet de commande suivante :

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    Cela n’est pas obligatoire. L’homologation peut être établie si des utilisateurs ont effectué des demandes d’homologation individuelles pour leurs réseaux virtuels respectifs, à condition que les demandes correspondent. L’ajout d’un utilisateur privilégié de l’autre réseau virtuel en tant qu’utilisateur du réseau virtuel local facilite la configuration.
2. Connectez-vous à Azure avec le compte de l’utilisateur privilégié UserB de l’abonnement Subscription-B et exécutez l’applet de commande suivante :

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Si l’homologation que vous créez est entre deux réseaux virtuels créés à l’aide du modèle de déploiement Azure Resource Manager, exécutez les étapes restantes de cette section. Si les deux réseaux virtuels ont été créés via des modèles de déploiement différents, ignorez les étapes restantes de cette section et suivez les étapes de la section [Homologation de réseaux virtuels créés via des modèles de déploiement différents](#x-model) de cet article.

3. Dans la session de connexion de l’UtilisateurA, exécutez la commande suivante :

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. Dans la session de connexion de l’utilisateur UserB, exécutez l’applet de commande ci-dessous :

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. Une fois l’homologation établie, n’importe quelle machine virtuelle de VNet3 doit pouvoir communiquer avec n’importe quelle machine virtuelle de VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Dans ce scénario, exécutez les applets de commande PowerShell ci-dessous pour établir l’homologation de réseaux virtuels. Vous devez définir la propriété `AllowForwardedTraffic` sur *True* et lier VNET1 à HubVnet afin d’autoriser le trafic entrant ne provenant pas de l’espace d’adressage du réseau virtuel homologue.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. Une fois l’homologation établie, vous pouvez vous reporter à [cet article](virtual-network-create-udr-arm-ps.md) et créer un itinéraire défini par l’utilisateur (UDR) pour rediriger le trafic du réseau virtuel VNET1 via une appliance virtuelle afin d’utiliser ses fonctionnalités. Lorsque vous spécifiez l’adresse du tronçon suivant dans l’itinéraire, vous pouvez la définir sur l’adresse IP de l’appliance virtuelle du réseau virtuel homologue HubVNet. Voici un exemple :

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans le *même* abonnement, passez directement à l’étape 2. La possibilité de créer une homologation de réseaux virtuels créés via des modèles de déploiement différents qui se trouvent dans des abonnements *différents* est disponible dans la **version préliminaire**. Les fonctionnalités de la version préliminaire n’ont pas le même niveau de fiabilité et de contrat de niveau de service que les fonctionnalités disponibles en version générale. Si vous créez une homologation entre des réseaux virtuels déployés via des modèles de déploiement différents dans des abonnements différents, vous devez tout d’abord effectuer les tâches suivantes :
    - Enregistrez la fonction de version préliminaire dans votre abonnement Azure en entrant la commande suivante à partir de PowerShell : `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` et `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - Exécutez les étapes 1 à 2 de la section [Homologation entre des abonnements](#x-sub) de cet article.
2. Lisez l’objet de réseau virtuel pour **VNET1**, le réseau virtuel Azure Resource Manager, en entrant la commande suivante :

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. Pour établir une homologation de réseaux virtuels dans ce scénario, un seul lien est nécessaire, plus précisément de **VNET1** à **VNET2**. Cette étape nécessite de connaître l’ID de ressource de votre réseau virtuel Classic. Le format d’ID du groupe de ressources ressemble à l’exemple suivant :

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Veillez à remplacer SubscriptionID, ResourceGroupName et VirtualNetworkName par les noms appropriés.

    Vous pouvez le faire en entrant la commande suivante :

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. Une fois le lien d’homologation de réseaux virtuels créé, vous pouvez voir l’état du lien, tel qu’illustré dans la sortie ci-dessous :
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>Supprimer une homologation de réseaux virtuels
1. Afin de supprimer une homologation de réseaux virtuels, vous devez exécuter l’applet de commande suivante :

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    Pour supprimer les deux liens, utilisez les commandes suivantes :

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. Une fois que vous supprimez un lien d’une homologation de réseaux virtuels, l’état du lien d’homologation affiché devient *Disconnected* (Déconnecté). Dans cet état, vous ne pouvez pas recréer le lien tant que l’état du lien d’homologation n’est pas défini sur *Initiated* (Initialisé). Nous vous recommandons de supprimer les deux liens avant de recréer l’homologation de réseaux virtuels.


