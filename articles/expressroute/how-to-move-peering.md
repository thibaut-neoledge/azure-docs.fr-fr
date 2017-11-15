---
title: "Déplacer un appairage public sur Azure ExpressRoute vers l’appairage Microsoft | Microsoft Docs"
description: "Cet article explique comment déplacer votre appairage public vers l’appairage Microsoft sur ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2017
ms.author: cherylmc
ms.openlocfilehash: 311e1de3200cd684bbec1329ebd5217b4fb3a2e2
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2017
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Déplacer un appairage public vers l’appairage Microsoft

ExpressRoute prend désormais en charge les services PaaS Azure, telles que Stockage Azure et SQL Database, à l’aide de l’appairage Microsoft avec des filtres de routes. Vous avez maintenant besoin d’un seul domaine de routage pour accéder aux services SaaS et PaaS Microsoft. Vous pouvez tirer parti des filtres de routes afin de publier sélectivement les préfixes de service PaaS pour les régions Azure que vous souhaitez utiliser.

Cet article vous permet de déplacer la configuration d’un appairage public vers l’appairage Microsoft sans temps d’arrêt. Pour plus d’informations sur les domaines de routage et les appairages, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).

> [!IMPORTANT]
> Vous devez avoir le module complémentaire ExpressRoute Premium pour pouvoir utiliser l’appairage Microsoft. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ ExpressRoute](expressroute-faqs.md#expressroute-premium).

## <a name="before-you-begin"></a>Avant de commencer

* Pour vous connecter à l’appairage Microsoft, vous devez configurer et gérer un processus NAT. Votre fournisseur de connectivité peut-configurer et gérer le processus NAT en tant que service géré. Si vous envisagez d’accéder aux services SaaS Azure et PaaS Azure sur l’appairage Microsoft, vous devez dimensionner le pool d’adresses IP NAT correctement. Pour plus d’informations sur le processus NAT pour ExpressRoute, consultez [Configuration NAT requise pour l’homologation Microsoft](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Si vous avez une ACL réseau pour les points de terminaison du service PaaS Azure quand vous utilisez l’appairage public Azure, vous devez vérifier que le pool d’adresses IP NAT configuré avec l’appairage Microsoft est inclus dans l’ACL qui est configurée pour le point de terminaison de service.

Pour effectuer un déplacement vers l’appairage Microsoft sans temps d’arrêt, vous devez suivre, dans l’ordre indiqué, les étapes fournies dans cet article.

## <a name="1-create-microsoft-peering"></a>1. Créer un appairage Microsoft

Si l’appairage Microsoft n’a pas été créé, utilisez un des articles suivants pour le créer. Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l’appairage Microsoft pour votre circuit.

  * [Créer l’appairage Microsoft à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Créer l’appairage Microsoft à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#msft)
  * [Créer l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](howto-routing-cli.md#msft)

## <a name="2-validate-microsoft-peering-is-enabled"></a>2. Vérifier que l’appairage Microsoft est activé

Vérifiez que l’appairage Microsoft est activé et que les préfixes publics publiés sont dans l’état configuré.

  * [Portail Azure](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Interface de ligne de commande Azure](howto-routing-cli.md#getmsft)

## <a name="3-configure-and-attach-a-route-filter-to-the-circuit"></a>3. Configurer un filtre de routes et le joindre au circuit

Par défaut, les nouveaux appairages Microsoft ne publient pas de préfixes tant qu’un filtre de routes n’est pas joint au circuit. Quand vous créez une règle de filtre de routes, vous pouvez spécifier la liste des communautés de service pour les régions Azure que vous souhaitez utiliser pour les services PaaS Azure, comme l’indique la capture d’écran suivante :

![Fusionner l’appairage public](.\media\how-to-move-peering\public.png)

Utilisez un des articles suivants pour configurer des filtres de routes.

  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide du portail Azure](how-to-routefilter-portal.md)
  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide d’Azure PowerShell](how-to-routefilter-powershell.md)
  * [Configurer des filtres de routes pour l’appairage Microsoft à l’aide de l’interface de ligne de commande Azure](how-to-routefilter-cli.md)

## <a name="4-delete-the-public-peering"></a>4. Supprimer l’appairage public

Après avoir vérifié que l’appairage Microsoft est configuré et que les préfixes que vous souhaitez utiliser sont correctement publiés sur l’appairage Microsoft, vous pouvez supprimer l’appairage public. Pour supprimer l’appairage public, utilisez un des articles suivants :

  * [Supprimer un appairage public Azure à l’aide du portail Azure](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Supprimer un appairage public Azure à l’aide d’Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Supprimer un appairage public Azure à l’aide de l’interface de ligne de commande](howto-routing-cli.md#deletepublic)

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).