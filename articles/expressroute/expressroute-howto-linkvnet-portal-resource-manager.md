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
   ms.date="04/14/2016"
   ms.author="cherylmc" />

# Lier un réseau virtuel à un circuit ExpressRoute

> [AZURE.SELECTOR]
- [Portail Azure - Resource Manager](expressroute-howto-linkvnet-portal-resource-manager.md)
- [PowerShell - Resource Manager](expressroute-howto-linkvnet-arm.md)
- [PowerShell - Classique](expressroute-howto-linkvnet-classic.md)



Cet article vous aidera à lier des réseaux virtuels à des circuits ExpressRoute à l'aide du modèle de déploiement Resource Manager et du portail Azure. Les réseaux virtuels peuvent appartenir au même abonnement, ou faire partie d’un autre abonnement.


**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Conditions préalables à la configuration

- Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vous devez disposer d’un circuit ExpressRoute actif. 
	- Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité. 
	
	- Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions de routage, consultez l'article sur la [configuration du routage](expressroute-howto-routing-portal-resource-manager.md).
	
	- L’homologation privée Azure doit être configurée et l’homologation BGP entre votre réseau et Microsoft doit être opérationnelle pour que vous puissiez activer la connectivité de bout en bout.
	
	- Vous devez disposer d'un réseau virtuel et d’une passerelle de réseau virtuel créés et totalement approvisionnés. Suivez les instructions pour créer une [passerelle VPN](../articles/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (seulement les étapes 1 à 5).

Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute. Tous les circuits ExpressRoute doivent être situés dans la même région géopolitique. Si vous avez activé le module complémentaire Premium d’ExpressRoute, vous pouvez lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute. Pour plus d'informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md).

## Connecter un réseau virtuel du même abonnement à un circuit


### Pour créer une connexion

1. Assurez-vous que votre circuit ExpressRoute et l'homologation privée Azure ont été correctement configurés. Suivez les instructions dans les articles pour [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [configurer le routage](expressroute-howto-routing-arm.md). Votre circuit ExpressRoute doit ressembler à l'image ci-dessous.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)

	>[AZURE.NOTE] Les informations de configuration BGP ne s’afficheront pas si vos homologations ont été configurées par le fournisseur de couche 3. Si votre circuit possède l’état approvisionné, vous pourrez créer des connexions.

2. Vous pouvez maintenant commencer à approvisionner une connexion entre votre passerelle de réseau virtuel et votre circuit ExpressRoute. Cliquez sur **Connexion** **>** **Ajouter** pour ouvrir le panneau **Ajouter une connexion**, puis configurez les valeurs. Consultez l’exemple de référence ci-dessous.
	

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)
 
	
3. Une fois votre connexion correctement configurée, votre objet de connexion affichera les informations de connexion.

	![](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)


### Suppression d'une connexion

Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans le panneau de votre connexion.

## Connecter un réseau virtuel d’un autre abonnement au circuit

Actuellement, vous ne pouvez pas connecter des réseaux virtuels entre différents abonnements avec le portail Azure. Toutefois, vous pouvez utiliser PowerShell pour le faire. Consultez l’article [PowerShell](expressroute-howto-linkvnet-arm.md) pour plus d’informations.

## Étapes suivantes

Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_0420_2016-->