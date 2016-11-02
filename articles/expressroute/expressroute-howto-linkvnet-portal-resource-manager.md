<properties
   pageTitle="Lier un réseau virtuel à un circuit ExpressRoute à l’aide du modèle de déploiement Resource Manager et du portail Azure | Microsoft Azure"
   description="Ce document fournit une vue d’ensemble de la façon de lier des réseaux virtuels à des circuits ExpressRoute."
   services="expressroute"
   documentationCenter="na"
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
   ms.date="10/10/2016"
   ms.author="cherylmc" />


# <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Lier un réseau virtuel à un circuit ExpressRoute

> [AZURE.SELECTOR]
- [Portail Azure - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - Classique](expressroute-howto-linkvnet-classic.md)



Cet article vous aide à lier des réseaux virtuels à des circuits Azure ExpressRoute en utilisant le modèle de déploiement Resource Manager et le portail Azure. Les réseaux virtuels peuvent appartenir au même abonnement ou faire partie d’un autre abonnement.


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Conditions préalables à la configuration

- Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit ExpressRoute actif.
    - Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité.

    - Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions sur le routage, consultez l’article [Configurer le routage](expressroute-howto-routing-portal-resource-manager.md) .

    - Vérifiez que l’homologation privée Azure est être configurée, et que l’homologation BGP entre votre réseau et Microsoft est être opérationnelle pour pouvoir activer la connectivité de bout en bout.

    - Vérifiez qu’un réseau virtuel et une passerelle de réseau virtuel ont été créés et entièrement approvisionnés. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (seulement les étapes 1 à 5).

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent figurer dans la même région géopolitique lors de l’utilisation d’un circuit ExpressRoute standard. Vous pouvez lier des réseaux virtuels à l'extérieur de la zone géopolitique du circuit ExpressRoute ou lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute si vous avez activé le module complémentaire Premium d’ExpressRoute. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md) .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connecter un réseau virtuel du même abonnement à un circuit


### <a name="to-create-a-connection"></a>Pour créer une connexion

1. Assurez-vous que votre circuit ExpressRoute et l'homologation privée Azure ont été correctement configurés. Suivez les instructions dans [Créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [Configurer le routage](expressroute-howto-routing-arm.md). Votre circuit ExpressRoute doit être similaire à l’image suivante.

    ![Capture d’écran Circuit ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

    >[AZURE.NOTE] Les informations de configuration BGP ne s’affichent pas si vos homologations ont été configurées par le fournisseur de la couche 3. Si votre circuit est dans l’état Approvisionné, vous pouvez créer des connexions.

2. Vous pouvez maintenant commencer à approvisionner une connexion pour lier votre passerelle de réseau virtuel à votre circuit ExpressRoute. Cliquez sur **Connexion** > **Ajouter** pour ouvrir le panneau **Ajouter une connexion**, puis configurez les valeurs. Consultez l’exemple de référence suivant.


    ![Capture d’écran Ajouter une connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  


3. Une fois votre connexion correctement configurée, votre objet de connexion affiche les informations de la connexion.

    ![Capture d’écran Objet de connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### <a name="to-delete-a-connection"></a>Suppression d'une connexion

Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans le panneau de votre connexion.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connecter un réseau virtuel d’un autre abonnement à un circuit

Actuellement, vous ne pouvez pas connecter des réseaux virtuels entre différents abonnements avec le portail Azure. Toutefois, vous pouvez utiliser PowerShell pour le faire. Pour plus d’informations, consultez l’article [PowerShell](expressroute-howto-linkvnet-arm.md) .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).



<!--HONumber=Oct16_HO2-->


