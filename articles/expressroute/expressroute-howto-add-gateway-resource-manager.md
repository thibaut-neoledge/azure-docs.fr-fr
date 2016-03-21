<properties
   pageTitle="Ajout d’une passerelle VPN à un réseau virtuel pour ExpressRoute à l’aide de Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article vous explique comment ajouter une passerelle VPN à un réseau virtuel Resource Manager déjà créé pour ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Ajout d’une passerelle VPN à un réseau virtuel Resource Manager pour une configuration ExpressRoute 

Cet article vous guidera à travers les étapes pour ajouter un sous-réseau de passerelle et créer une passerelle VPN pour un réseau virtuel existant. Les étapes de cette configuration sont spécifiquement adaptées aux réseaux virtuels qui ont été créés à l’aide du **modèle de déploiement Resource Manager** et qui seront utilisés dans une configuration ExpressRoute. Si vous avez besoin d’informations sur la création de passerelles pour les réseaux virtuels à l’aide du modèle de déploiement classique, consultez [Configuration d’un réseau virtuel pour ExpressRoute à l’aide du portail Classic](expressroute-howto-vnet-portal-classic.md).

## Avant tout chose

Vérifiez que vous avez installé les applets de commande Azure PowerShell nécessaires pour cette configuration (1.0.2 ou ultérieure). Si vous n’avez pas installé les applets de commande, vous devez le faire avant de commencer les étapes de configuration. Pour plus d’informations sur l’installation d’Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Vérification de la création de la passerelle

Utilisez la commande suivante pour vérifier que la passerelle a été créée.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Étapes suivantes

Une fois que vous avez créé la passerelle VPN, vous pouvez lier votre réseau virtuel à un circuit ExpressRoute. Consultez [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).

<!---------HONumber=AcomDC_0309_2016-->