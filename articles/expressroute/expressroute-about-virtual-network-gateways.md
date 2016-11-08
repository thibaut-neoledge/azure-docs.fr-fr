---
title: À propos des passerelles de réseau virtuel pour ExpressRoute | Microsoft Docs
description: En savoir plus sur les passerelles de réseau virtuel pour ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager, azure-service-management

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: cherylmc

---
# <a name="about-virtual-network-gateways-for-expressroute"></a>À propos des passerelles de réseau virtuel pour ExpressRoute
Une passerelle de réseau virtuel est conçue pour faire circuler le trafic réseau entre les réseaux virtuels Azure et les emplacements locaux. Lorsque vous configurez une connexion ExpressRoute, vous devez créer et configurer une passerelle de réseau virtuel et une connexion à la passerelle de réseau virtuel.

Lorsque vous créez une passerelle de réseau virtuel, vous spécifiez plusieurs paramètres. L’un des paramètres requis spécifie si la passerelle sera utilisée pour le trafic ExpressRoute ou le trafic VPN de site à site. Dans le modèle de déploiement de Resource Manager, le paramètre est « -GatewayType ».

Lorsque le trafic réseau est envoyé sur une connexion privée dédiée, vous utilisez le type de passerelle « ExpressRoute ». C’est ce que l’on appelle une passerelle ExpressRoute. Lorsque le trafic réseau est transmis chiffré sur l’Internet public, vous utilisez le type de passerelle « Vpn ». Il s’agit alors d’une passerelle VPN. Les connexions site à site, point à site et réseau virtuel à réseau virtuel utilisent toutes une passerelle VPN. 

Chaque réseau virtuel ne peut posséder qu’une seule passerelle de réseau virtuel par type de passerelle. Par exemple, une passerelle de réseau virtuel peut utiliser le type de passerelle VPN et une autre le type de passerelle ExpressRoute. Cet article décrit la passerelle de réseau virtuel ExpressRoute.

## <a name="<a-name="gwsku"></a>gateway-skus"></a><a name="gwsku"></a>SKU de passerelle
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Si vous souhaitez mettre à niveau votre passerelle vers une référence (SKU) de passerelle plus puissante, dans la plupart des cas, vous pouvez utiliser l’applet de commande PowerShell « Resize-AzureRmVirtualNetworkGateway ». Cela fonctionne pour les mises à niveau vers les références (SKU) Standard HighPerformance. Toutefois, pour mettre à niveau vers la référence (SKU) UltraPerformance, vous devez recréer la passerelle.

### <a name="<a-name="aggthroughput"></a>estimated-aggregate-throughput-by-gateway-sku"></a><a name="aggthroughput"></a>Débit agrégé estimé par SKU de passerelle
Le tableau ci-dessous présente les types de passerelle et le débit total estimé. Cette table s’applique aux modèles de déploiement classique et Resource Manager.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

## <a name="<a-name="resources"></a>rest-apis-and-powershell-cmdlets"></a><a name="resources"></a>API REST et applets de commande PowerShell
Pour accéder à des ressources techniques supplémentaires et connaître les exigences spécifiques en matière de syntaxe lors de l’utilisation d’API REST et d’applets de commande PowerShell pour les configurations de passerelles de réseau virtuel, consultez les pages suivantes :

| **Classique** | **Gestionnaire de ressources** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les configurations de connexion disponibles, consultez la rubrique [Vue d’ensemble d’ExpressRoute](expressroute-introduction.md) . 

<!--HONumber=Oct16_HO2-->


