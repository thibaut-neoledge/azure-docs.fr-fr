---
title: Ajout d’une passerelle de réseau virtuel à un réseau virtuel pour ExpressRoute à l’aide de Resource Manager et de PowerShell | Microsoft Docs
description: Cet article vous explique comment ajouter une passerelle de réseau virtuel à un réseau virtuel Resource Manager déjà créé pour ExpressRoute
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: charwen

---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-resource-manager-and-powershell"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute avec Resource Manager et PowerShell
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
> * [PowerShell - Classique](expressroute-howto-add-gateway-classic.md)
> 
> 

Cet article vous montrera les étapes nécessaires pour ajouter, redimensionner et supprimer une passerelle de réseau virtuel pour un réseau virtuel existant. Les étapes de cette configuration sont spécifiquement adaptées aux réseaux virtuels qui ont été créés à l’aide du **modèle de déploiement Resource Manager** et qui seront utilisés dans une configuration ExpressRoute. 

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Avant tout chose
Vérifiez que vous avez installé les applets de commande Azure PowerShell nécessaires pour cette configuration (1.0.2 ou ultérieure). Si vous n’avez pas installé les applets de commande, vous devez le faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!--HONumber=Oct16_HO2-->


