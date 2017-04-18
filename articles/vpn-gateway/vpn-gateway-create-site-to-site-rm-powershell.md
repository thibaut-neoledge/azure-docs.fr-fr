---
title: "Connecter votre réseau local à un réseau virtuel Azure : VPN site à site : PowerShell | Microsoft Docs"
description: "Étapes de création d’une connexion IPsec entre votre réseau local et un réseau virtuel Azure via l’Internet public. Ces étapes vous aideront à créer une connexion de passerelle VPN de site à site à l’aide de PowerShell."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: fcc2fda5-4493-4c15-9436-84d35adbda8e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 4605ac9fe40f369d562dfcbf2abe7403f307d2a9
ms.lasthandoff: 04/12/2017


---
# <a name="create-a-vnet-with-a-site-to-site-vpn-connection-using-powershell"></a>Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell

Une connexion par passerelle VPN site à site (S2S) est une connexion via un tunnel VPN IPsec/IKE (S2S ou IKEv1). Ce type de connexion requiert un périphérique VPN local auquel est affectée une IP publique, et qui ne se situe pas derrière un NAT. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux.

![Schéma de connexion intersite d’une passerelle VPN site à site](./media/vpn-gateway-create-site-to-site-rm-powershell/site-to-site-connection-diagram.png)

Cet article vous guide lors de la création d’un réseau virtuel et d’une connexion de passerelle VPN de site à site à votre réseau local, à l’aide du modèle de déploiement Azure Resource Manager. Les connexions site à site peuvent être utilisées pour les configurations hybrides et entre les différents locaux. Vous pouvez également créer cette configuration à l’aide d’autres outils de déploiement ou pour le modèle de déploiement Classic en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Classic - Portail Azure](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Classic - Portail Classic](vpn-gateway-site-to-site-create.md)
>
>

#### <a name="additional-configurations"></a>Configurations supplémentaires
Si vous souhaitez établir une connexion entre des réseaux virtuels, mais si vous ne créez pas une connexion à un emplacement local, consultez [configurer une connexion de réseau virtuel à réseau virtuel](vpn-gateway-vnet-vnet-rm-ps.md). Si vous souhaitez ajouter une connexion de site à site à un réseau virtuel qui possède déjà une connexion, consultez [Connecter plusieurs sites locaux à un réseau virtuel](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).


## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

Vérifiez que vous disposez des éléments suivants avant de commencer la configuration :

* Un périphérique VPN compatible et une personne qui est en mesure de le configurer. Consultez [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md). Si vous ne maîtrisez pas la configuration de votre appareil VPN ou les plages d’adresses IP mentionnées dans la configuration de votre réseau local, vous devez contacter une personne qui peut vous fournir ces informations.
* Une adresse IP publique exposée en externe pour votre appareil VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial).
* La dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="Login"></a>1. Connexion à votre abonnement
Pour utiliser les applets de commande Resource Manager, passez au mode PowerShell. Pour plus d’informations, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md).

Ouvrez la console PowerShell et connectez-vous à votre compte. Utilisez l’exemple suivant pour faciliter votre connexion :

```powershell
Login-AzureRmAccount
```

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Spécifiez l’abonnement que vous souhaitez utiliser.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="VNet"></a>2. Créer un réseau virtuel et un sous-réseau de passerelle
Les exemples utilisent un sous-réseau de passerelle de /28. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau plus vaste qui inclut un plus grand nombre d’adresses en sélectionnant au moins /28 ou /27. Cela permettra à un nombre suffisant d’adresses de s’adapter à de possibles configurations supplémentaires possibles que vous êtes susceptible de souhaiter par la suite.

Si vous disposez déjà d’un réseau virtuel avec un sous-réseau de passerelle /29 ou de taille supérieure, vous pouvez passer d’emblée à l’étape [Ajouter votre passerelle de réseau local](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Pour créer un réseau virtuel et un sous-réseau de passerelle
Utilisez l’exemple suivant pour créer un réseau virtuel et un sous-réseau de passerelle :

Créez d’abord un groupe de ressources :

```powershell
New-AzureRmResourceGroup -Name testrg -Location 'West US'
```

Ensuite, créez votre réseau virtuel Vérifiez que les espaces d’adressage que vous spécifiez ne chevauchent pas les espaces d’adressage de votre réseau local.

L’exemple suivant crée un réseau virtuel nommé *testvnet* et deux sous-réseaux, l’un nommé *GatewaySubnet* et l’autre *Subnet1*. Il est important de créer un sous-réseau nommé spécifiquement *GatewaySubnet*. Si vous le nommez autrement, la configuration de votre connexion échouera.

Définissez les variables.

```powershell
$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
```

Créez le réseau virtuel.

```powershell
New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
-Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2
```

### <a name="gatewaysubnet"></a>Pour ajouter un sous-réseau de passerelle à un réseau virtuel que vous avez déjà créé
Cette étape est requise uniquement si vous avez besoin d’ajouter un sous-réseau de passerelle à un réseau virtuel créé au préalable.

Vous pouvez créer votre sous-réseau de passerelle à l’aide de l’exemple suivant. Assurez-vous de nommer le sous-réseau de passerelle « GatewaySubnet ». Si vous choisissez un autre nom, vous créez un sous-réseau, mais Azure ne le traitera pas comme un sous-réseau de passerelle.

Définissez les variables.

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
```

Créez le sous-réseau de passerelle.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
```

Définissez la configuration.

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

## 3. <a name="localnet"></a>Ajouter votre passerelle de réseau local
Dans un réseau virtuel, la passerelle de réseau local fait généralement référence à votre emplacement local. Vous donnez à cet emplacement un nom auquel Azure pourra se référer, puis vous spécifierez le préfixe de l’espace d’adressage de la passerelle de réseau local.

Azure utilise le préfixe d’adresse IP que vous spécifiez pour identifier le trafic à envoyer vers votre emplacement local. Cela signifie que vous devez spécifier chaque préfixe d’adresse que vous voulez associer à votre passerelle de réseau local. Vous pouvez facilement mettre à jour ces préfixes si votre réseau local est modifié.

Lorsque vous utilisez les exemples PowerShell, notez les points suivants :

* *GatewayIPAddress* est l’adresse IP de votre périphérique VPN local. Votre périphérique VPN ne peut pas se trouver derrière un NAT.
* *AddressPrefix* est votre espace d’adressage local.

Pour ajouter une passerelle de réseau local avec un préfixe d’adresse unique :

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Pour ajouter une passerelle de réseau local avec des préfixes d’adresse multiples :

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')
```

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Pour modifier des préfixes d’adresses IP de votre passerelle de réseau local
Parfois, les préfixes de votre passerelle de réseau local changent. Les étapes à suivre pour modifier vos préfixes d’adresses IP varient selon que vous avez créé une connexion à la passerelle VPN. Consultez la section [Modifier des préfixes d’adresses IP de votre passerelle de réseau local](#modify) de cet article.

## <a name="PublicIP"></a>4. Demander une adresse IP publique de la passerelle VPN
Ensuite, vous demandez qu’une adresse IP publique soit allouée à votre passerelle VPN du réseau virtuel Azure. Il ne s’agit pas de l’adresse IP affectée à votre périphérique VPN, mais de celle qui est affectée à la passerelle VPN Azure elle-même. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle de façon dynamique. Vous utilisez cette adresse IP lors de la configuration de votre périphérique VPN local pour la connexion à la passerelle.

Actuellement, la passerelle VPN Azure du modèle de déploiement de Resource Manager ne prend en charge que les adresses IP publiques à l’aide de la méthode d’allocation dynamique. Néanmoins, cela ne signifie pas que l’adresse IP est modifiée. L’adresse IP de la passerelle VPN Azure change uniquement lorsque la passerelle est supprimée, puis recréée. L’adresse IP publique de la passerelle n’est pas modifiée lors du redimensionnement, de la réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN Azure.

Utilisez l’exemple PowerShell suivant :

```powershell
$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>5. Créer la configuration de l’adressage IP de la passerelle
La configuration de la passerelle définit le sous-réseau et l’adresse IP publique à utiliser. Utilisez l’exemple suivant pour créer la configuration de votre passerelle :

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```

## <a name="CreateGateway"></a>6. Créer la passerelle de réseau virtuel
Dans cette étape, vous créez la passerelle de réseau virtuel. La création d’une passerelle peut durer un certain temps. Souvent 45 minutes, voire plus.

Utilisez les valeurs suivantes :

* Le paramètre *-GatewayType* pour une configuration de site à site est *Vpn*. Le type de passerelle dépend toujours de la configuration que vous implémentez. Par exemple, d’autres configurations de passerelle peuvent nécessiter GatewayType ExpressRoute.
* Le paramètre *-VpnType* peut avoir pour valeur *RouteBased* (appelé passerelle dynamique dans certaines documentations) ou *PolicyBased* (appelé passerelle statique dans certaines documentations). Pour plus d’informations sur les types de passerelles VPN, consultez [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md).
* La valeur de *-GatewaySku* peut être *Basic*, *Standard* ou *HighPerformance*.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku Standard
```

## <a name="ConfigureVPNDevice"></a>7. Configuration de votre périphérique VPN
À ce stade, vous avez besoin de l’adresse IP publique de la passerelle de réseau virtuel pour configurer votre périphérique VPN local. Consultez le fabricant de votre périphérique pour des informations de configuration spécifiques. Pour plus d’informations, vous pouvez vous reporter à [Périphériques VPN](vpn-gateway-about-vpn-devices.md) .

Pour trouver l’adresse IP publique de votre passerelle de réseau virtuel, utilisez l’exemple suivant :

```powershell
Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg
```

## <a name="CreateConnection"></a>8. Créer la connexion VPN
Créez ensuite la connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN. Assurez-vous de remplacer ces valeurs par les vôtres. La clé partagée doit correspondre à la valeur que vous avez utilisée pour la configuration de votre périphérique VPN. Notez que le `-ConnectionType` de site à site est *IPsec*.

Définissez les variables.

```powershell
$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
```

Créez la connexion.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

Après un bref délai, la connexion sera établie.

## <a name="toverify"></a>Pour vérifier une connexion VPN
Il existe différentes façons de vérifier votre connexion VPN.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Pour modifier des préfixes d’adresses IP d’une passerelle de réseau local
Si vous devez modifier les préfixes de votre passerelle de réseau local, suivez les instructions suivantes. Deux ensembles d’instructions vous sont fournis : Les instructions que vous choisissez d’appliquer varient selon que vous avez déjà créé ou non votre connexion à la passerelle.

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Pour modifier l’adresse IP d’une passerelle de réseau local
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Étapes suivantes
*  Une fois la connexion achevée, vous pouvez ajouter des machines virtuelles à vos réseaux virtuels. Pour plus d’informations, consultez [Machines virtuelles](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Pour plus d’informations sur le protocole BGP, consultez les articles [Vue d’ensemble du protocole BGP](vpn-gateway-bgp-overview.md) et [Comment configurer BGP](vpn-gateway-bgp-resource-manager-ps.md).

