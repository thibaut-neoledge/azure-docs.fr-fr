---
title: "Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide de PowerShell | Microsoft Docs"
description: "Configurez une passerelle de réseau virtuel pour un réseau virtuel suivant le modèle de déploiement classique avec PowerShell pour une configuration ExpressRoute."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/03/2016
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 4acb64838288d36f0dc1b1eb9736b00faef21a0c
ms.openlocfilehash: 4b05e12c2b30f1e8aa3efc7cd47cb79c4958dc1e


---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-classic-deployment-model-and-powershell"></a>Configurer une passerelle de réseau virtuel pour ExpressRoute à l’aide du modèle de déploiement classique et de PowerShell
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-howto-add-gateway-resource-manager.md)
> * [PowerShell - Classique](expressroute-howto-add-gateway-classic.md)
> 
> 

Cet article vous montrera les étapes nécessaires pour ajouter, redimensionner et supprimer une passerelle de réseau virtuel pour un réseau virtuel existant. Les étapes de cette configuration sont spécifiquement adaptées aux réseaux virtuels qui ont été créés à l’aide du **modèle de déploiement classique** et qui seront utilisés dans une configuration ExpressRoute. 

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Avant tout chose
Vérifiez que vous avez installé les applets de commande Azure PowerShell nécessaires pour cette configuration (1.0.2 ou ultérieure). Si vous n’avez pas installé les applets de commande, vous devez le faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé la passerelle de réseau virtuel, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-classic.md).




<!--HONumber=Dec16_HO1-->


