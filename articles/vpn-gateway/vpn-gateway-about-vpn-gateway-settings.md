---
title: "Paramètres de la passerelle VPN pour les connexions Azure entre locaux | Microsoft Docs"
description: "Découvrez les paramètres de passerelle VPN pour les passerelles de réseau virtuel Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: ae665bc5-0089-45d0-a0d5-bc0ab4e79899
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: b902d2e79633959a6f76ddd45b1193177b0e8465
ms.openlocfilehash: 1ac5a78c8d9419e4c641bf66f8dac7aa8cbcd179
ms.contentlocale: fr-fr
ms.lasthandoff: 02/14/2017


---
# <a name="about-vpn-gateway-configuration-settings"></a>À propos des paramètres de configuration de la passerelle VPN
Une passerelle VPN est un type de passerelle de réseau virtuel qui envoie le trafic chiffré entre votre réseau virtuel et votre emplacement local à travers une connexion publique. Vous pouvez également utiliser une passerelle VPN pour acheminer le trafic entre réseaux virtuels sur l’infrastructure Azure.

Une connexion de passerelle VPN s’appuie sur la configuration de plusieurs ressources, contenant chacune des paramètres configurables. Les sections de cet article présentent les ressources et les paramètres relatifs à une passerelle VPN pour un réseau virtuel créé dans le modèle de déploiement Resource Manager. Vous trouverez les descriptions et les diagrammes de topologie de chaque solution de connexion dans l’article [À propos la passerelle VPN](vpn-gateway-about-vpngateways.md).  

## <a name="gwtype"></a>Types de passerelle
Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel de chaque type. Lorsque vous créez une passerelle de réseau virtuel, vous devez vous assurer que le type de passerelle convient pour votre configuration.

Les valeurs disponibles pour -GatewayType sont : 

* Vpn
* ExpressRoute

Une passerelle VPN nécessite le `-GatewayType` *VPN*.  

Exemple :

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
    
## <a name="gwsku"></a>SKU de passerelle

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-original-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configuration de la référence SKU de passerelle
####<a name="specifying-the-gateway-sku-in-the-azure-portal"></a>Spécification de la référence SKU de passerelle dans le portail Azure

Si vous utilisez le portail Azure pour créer une passerelle de réseau virtuel Resource Manager, vous pouvez sélectionner la référence SKU de la passerelle dans la liste déroulante. Les options qui vous sont présentées correspondent au type de passerelle et au type de VPN sélectionnés.

Par exemple, si vous sélectionnez le type de passerelle « VPN » et le type de VPN « Basé sur des stratégies », vous ne pouvez voir que la référence SKU « De base » parce que c’est la seule référence SKU disponible pour les VPN basés sur des stratégies. Si vous sélectionnez « Basé sur un itinéraire », vous pouvez sélectionner les références SKU De base, Standard et HighPerformance. 

####<a name="specifying-the-gateway-sku-using-powershell"></a>Spécification de la référence SKU de passerelle à l’aide de PowerShell

L’exemple PowerShell suivant spécifie la `-GatewaySku` en tant que *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

####<a name="changing-a-gateway-sku"></a>Modification d’une référence SKU de passerelle

Si vous voulez mettre à niveau votre référence SKU de passerelle vers une référence SKU plus puissante (De base/Standard vers Hautes performances), vous pouvez utiliser l’applet de commande PowerShell `Resize-AzureRmVirtualNetworkGateway`. Vous pouvez également réduire la taille de référence SKU à l’aide de cet applet de commande.

L’exemple PowerShell suivant montre une référence SKU de passerelle redimensionnée sur HighPerformance.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Débit agrégé estimé par référence SKU et type de passerelle
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="connectiontype"></a>Types de connexion
Dans le modèle de déploiement de Resource Manager, chaque configuration nécessite un type spécifique de connexion de passerelle de réseau virtuel. Les valeurs de PowerShell pour Resource Manager disponibles pour `-ConnectionType` sont :

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

Dans l’exemple PowerShell suivant, nous créons une connexion S2S qui nécessite le type de connexion *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Types de VPN
Lorsque vous créez la passerelle de réseau virtuel d’une configuration de passerelle VPN, vous devez spécifier un type de VPN. Le type de VPN que vous choisissez dépend de la topologie de connexion que vous souhaitez créer. Par exemple, une connexion P2S nécessite un VPN de type basé sur un itinéraire. Un type de VPN peut également dépendre du matériel que vous utiliserez. Les configurations S2S nécessitent un périphérique VPN. Certains périphériques VPN seront ne prennent en charge qu’un certain type de VPN.

Le type de VPN que vous choisissez doit satisfaire à toutes les exigences de connexion de la solution que vous souhaitez créer. Par exemple, si vous souhaitez créer une connexion de passerelle VPN S2S et une connexion de passerelle VPN P2S pour le même réseau virtuel, vous utiliserez un VPN de type *basé sur un itinéraire* car P2S requiert un VPN de type basé sur un itinéraire. Vous devez également vérifier que votre périphérique VPN prend en charge une connexion VPN basée sur un itinéraire. 

Une fois qu’une passerelle de réseau virtuel a été créée, vous ne pouvez plus modifier le type de VPN. Vous devez supprimer la passerelle de réseau virtuel et en créer une nouvelle. Il existe deux types de VPN :

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

L’exemple PowerShell suivant spécifie la `-VpnType` en tant que *RouteBased*. Lorsque vous créez une passerelle, vous devez vous assurer que -VpnType convient pour votre configuration. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

## <a name="requirements"></a>Conditions requises de la passerelle
[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Sous-réseau de passerelle
Vous devez créer un sous-réseau de passerelle afin de configurer une passerelle de réseau virtuel pour votre réseau virtuel. Le sous-réseau de passerelle contient les adresses IP utilisées par les services de passerelle de réseau virtuel. Pour fonctionner correctement, le sous-réseau de passerelle doit être nommé *SousRéseau_Passerelle* . Ce nom indique à Azure que ce sous-réseau doit être utilisé pour la passerelle.

Lorsque vous créez le sous-réseau de passerelle, vous spécifiez le nombre d’adresses IP que contient le sous-réseau. Les adresses IP dans le sous-réseau de passerelle sont allouées au service de passerelle. Certaines configurations nécessitent d’allouer davantage d’adresses IP aux services de passerelle. Assurez-vous que votre sous-réseau de passerelle contient suffisamment d’adresses IP pour pouvoir évoluer au rythme de votre croissance future et prendre en charge d’éventuelles nouvelles configurations de connexion. Bien qu’il soit possible de créer un sous-réseau de passerelle aussi petit que /29, nous vous recommandons de créer un sous-réseau de passerelle de taille /28 ou supérieure (/28, /27, /26, etc.). Prenez connaissance des recommandations relatives à la configuration que vous souhaitez créer et vérifiez que votre sous-réseau de passerelle suit ces recommandations.

L’exemple PowerShell Resource Manager suivant montre un sous-réseau de passerelle nommé GatewaySubnet. Vous pouvez voir que la notation CIDR spécifie une taille /27, ce qui permet d’avoir un nombre suffisamment élevé d’adresses IP pour la plupart des configurations actuelles.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Passerelles de réseau local
Lorsque vous créez une configuration de passerelle VPN, la passerelle du réseau local représente souvent votre emplacement local. Dans le modèle de déploiement classique, la passerelle de réseau local a été appelée Site local. 

Vous donnez un nom à la passerelle de réseau local (l’adresse IP publique du périphérique VPN local) et spécifiez les préfixes d’adresse qui se situent dans l’emplacement local. Azure examine les préfixes d’adresse de destination pour le trafic réseau, consulte la configuration que vous avez spécifiée pour votre passerelle de réseau local, et route les paquets en conséquence. Vous spécifiez également des passerelles de réseau local pour les configurations avec interconnexion de réseaux virtuels qui utilisent une connexion de passerelle VPN.

L’exemple PowerShell suivant crée une nouvelle passerelle de réseau local :

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Parfois, vous devez modifier les paramètres de passerelle de réseau local. C’est le cas, par exemple, lorsque vous ajoutez ou modifiez la plage d’adresses, ou lorsque l’adresse IP du périphérique VPN change. Pour un réseau virtuel classique, vous pouvez modifier ces paramètres via la page Réseaux locaux du portail Classic. Pour Resource Manager, voir [Modification des paramètres de passerelle de réseau local à l’aide de PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>API REST et applets de commande PowerShell
Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles VPN, consultez les pages suivantes :

| **Classique** | **Gestionnaire de ressources** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les configurations de connexion disponible, consultez la rubrique [À propos de la passerelle VPN](vpn-gateway-about-vpngateways.md) . 


