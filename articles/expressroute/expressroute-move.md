<properties
   pageTitle="Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager | Microsoft Azure"
   description="Cette page décrit tout ce que vous devez savoir sur les liaisons entre les modèles de déploiement classique et Resource Manager."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager

Cet article décrit les enjeux d’une migration d’un circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

Un circuit ExpressRoute peut être utilisé pour se connecter à des réseaux virtuels (VNets) déployés à la fois dans les modèles classique et Resource Manager. Quelle que soit la façon dont il est créé, un circuit ExpressRoute peut désormais être relié à des réseaux virtuels sur les deux modèles de déploiement.

![](./media/expressroute-move/expressroute-move-1.png)

## Circuits ExpressRoute créés dans le modèle de déploiement classique

Les circuits ExpressRoute créés dans le modèle de déploiement classique doivent tout d’abord être migrés vers le modèle de déploiement Resource Manager afin de permettre une connectivité aux modèles de déploiement classique et Resource Manager. Le transfert d’une connexion n’entraîne aucune perte de connectivité ni interruption. Toutes les liaisons de réseau virtuel du circuit associées au modèle de déploiement classique (et se trouvant dans le même abonnement ou dans plusieurs abonnements) seront conservées. Une fois la migration effectuée, le circuit ExpressRoute présentera la même interface et les mêmes performances que celles d’un circuit ExpressRoute créé dans le modèle de déploiement Resource Manager. Vous serez dès lors en mesure de créer des connexions aux réseaux virtuels dans le modèle de déploiement Resource Manager.

Une fois le circuit ExpressRoute migré vers le modèle de déploiement Resource Manager, vous pourrez gérer le cycle de vie du circuit ExpressRoute uniquement à l’aide du modèle de déploiement Resource Manager. Autrement dit, les opérations telles que l’ajout, la mise à jour ou la suppression d’homologations, la mise à jour des propriétés du circuit (bande passante, référence (SKU) et type de facturation) et la suppression des circuits peuvent être entièrement exécutées dans le modèle de déploiement Resource Manager. Pour plus d’informations sur la manière de gérer l’accès aux deux modèles de déploiement, reportez-vous à la section ci-dessous relative aux circuits créés dans le modèle de déploiement Resource Manager.

La procédure de migration ne nécessite pas de faire appel à votre fournisseur de connectivité.

## Circuits ExpressRoute créés dans le modèle de déploiement Resource Manager

Vous pouvez rendre des circuits ExpressRoute créés dans le modèle de déploiement Resource Manager accessibles sur les deux modèles de déploiement. Tout circuit ExpressRoute de votre abonnement peut être accessible à partir de ces deux modèles de déploiement.

- Par défaut, les circuits ExpressRoute qui ont été créés dans le modèle de déploiement Resource Manager n’auront pas accès au modèle de déploiement classique.
- Les circuits ExpressRoute qui ont été migrés du modèle de déploiement classique vers le modèle de déploiement Resource Manager seront, par défaut, accessibles à partir de ces deux modèles de déploiement.
- Un circuit ExpressRoute dispose toujours d’un accès au modèle de déploiement Resource Manager, qu’il ait été créé dans le modèle de déploiement Resource Manager ou dans le modèle de déploiement classique. Cela signifie que vous pouvez créer des connexions aux réseaux virtuels créés dans le modèle de déploiement Resource Manager en suivant les instructions relatives à la [liaison des réseaux virtuels](expressroute-howto-linkvnet-arm.md). 
- L’accès au modèle de déploiement classique est contrôlé par le paramètre « allowClassicOperations » du circuit ExpressRoute. 

>[AZURE.IMPORTANT] Tous les quotas décrits à la page dédiée aux [limites de service](../azure-subscription-service-limits.md) s’appliquent. Par exemple, un circuit standard peut contenir un maximum de 10 liaisons réseau virtuel / connexions sur les modèles de déploiement classique et Resource manager.


### Contrôle de l’accès à modèle de déploiement classique

Vous pouvez lier un circuit ExpressRoute à des réseaux virtuels dans les deux modèles de déploiement en définissant le paramètre « allowClassicOperations » du circuit ExpressRoute.

En définissant le paramètre « allowClassicOperations » sur TRUE, vous pouvez lier les réseaux virtuels de ces deux modèles de déploiement à un circuit ExpressRoute. Vous pouvez créer une liaison à des réseaux virtuels du modèle de déploiement classique en suivant les recommandations relatives à la [liaison de réseaux virtuels dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md). Pour créer une liaison à des réseaux virtuels du modèle de déploiement Resource Manager, suivez les recommandations relatives à la [liaison de réseaux virtuels dans le modèle de déploiement Resource Manager](expressroute-howto-linkvnet-arm.md).

Si vous définissez le paramètre « allowClassicOperations » sur FALSE, le circuit ne sera pas accessible à partir du modèle de déploiement classique. Toutes les liaisons de réseau virtuel contenues dans le modèle de déploiement classique seront cependant conservées. Dans ce cas, le circuit ExpressRoute ne sera pas visible dans le modèle de déploiement classique.

### Opérations prises en charge dans le modèle de déploiement classique

Les opérations classiques suivantes sont prises en charge sur un circuit ExpressRoute lorsque le paramètre « allowClassicOperations » est défini sur TRUE.

 - Obtenir des informations sur le circuit ExpressRoute
 - Création / mise à jour / obtention / suppression de liaisons de réseau virtuel aux réseaux virtuels classiques
 - Création / mise à jour / obtention, suppression d’autorisations de liaison de réseau virtuel pour la connectivité entre différents abonnements

Vous ne serez pas en mesure d’effectuer les opérations classiques suivantes si vous définissez le paramètre « allowClassicOperations » sur TRUE.

 - Création / mise à jour / obtention / suppression des homologations BGP pour les homologations privées Azure, les homologations publiques Azure et les homologations Microsoft
 - Suppression d’un circuit ExpressRoute

## Communication entre les modèles de déploiement classique et Resource Manager

Le circuit ExpressRoute agira comme un pont entre le modèle de déploiement classique et le modèle de déploiement Resource Manager. Le trafic entre les machines virtuelles déployées dans des réseaux virtuels sur le modèle de déploiement classique et celles déployées dans des réseaux virtuels sur le modèle de déploiement Resource Manager transitera via ExpressRoute si les deux réseaux virtuels sont liés au même circuit ExpressRoute. Le débit cumulé sera limité par la capacité de débit de la passerelle de réseau virtuel. Dans ce cas, le trafic n’entrera ni dans vos réseaux ni dans ceux de votre fournisseur de connectivité. Le flux de trafic entre les réseaux virtuels est entièrement contenu dans le réseau Microsoft.

## Accès aux ressources d’homologations publiques Azure et Microsoft

Vous pouvez continuer à accéder aux ressources généralement accessibles via l’homologation publique Azure et l’homologation Microsoft sans interruption.

## Opérations prises en charge

Cette section décrit les opérations prises en charge grâce à cette fonctionnalité.

 - Un circuit ExpressRoute peut être utilisé pour accéder à des réseaux virtuels déployés à la fois dans les modèles classique et Resource Manager.
 - Vous pouvez transférer un circuit ExpressRoute du modèle de déploiement classique au modèle de déploiement Resource Manager. Une fois la migration effectuée, le circuit ExpressRoute présentera la même interface et les mêmes performances que n’importe quel autre circuit ExpressRoute créé dans le modèle de déploiement Resource Manager.
 - Seul le circuit ExpressRoute peut être déplacé. Les liaisons du circuit, les réseaux virtuels et les passerelles VPN ne seront pas transférés dans le cadre de cette opération.
 - Une fois le circuit ExpressRoute migré vers le modèle de déploiement Resource Manager, vous pourrez gérer le cycle de vie du circuit ExpressRoute uniquement à l’aide du modèle de déploiement Resource Manager. Autrement dit, les opérations telles que l’ajout, la mise à jour ou la suppression d’homologations, la mise à jour des propriétés du circuit (bande passante, référence (SKU) et type de facturation) et la suppression des circuits peuvent être entièrement exécutées dans le modèle de déploiement Resource Manager.
 - Le circuit ExpressRoute agira comme un pont entre le modèle de déploiement classique et le modèle de déploiement Resource Manager. Le trafic entre les machines virtuelles déployées dans des réseaux virtuels sur le modèle de déploiement classique et celles déployées dans des réseaux virtuels sur le modèle de déploiement Resource Manager transitera via ExpressRoute si les deux réseaux virtuels sont liés au même circuit ExpressRoute. 
 - Connectivité entre différents abonnements dans les modèles de déploiement classique et Resource Manager.

## Opérations NON prises en charge

Cette section décrit les opérations qui ne sont pas prises en charge avec cette fonctionnalité.

 - Transfert des liaisons du circuit, des passerelles et des réseaux virtuels du modèle de déploiement classique au modèle de déploiement Resource Manager.
 - Gestion du cycle de vie du circuit ExpressRoute à partir du modèle de déploiement classique.
 - Prise en charge RBAC pour le modèle de déploiement classique. Vous ne serez pas en mesure d’effectuer des contrôles RBAC sur un circuit du modèle de déploiement classique. Un administrateur ou coadministrateur de l’abonnement ne pourra ni créer ni supprimer de liaisons de réseaux virtuels sur le circuit.

## Configuration

Suivez les instructions de l’article [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-howto-move-arm.md)

## Étapes suivantes

- Pour plus d’informations sur les workflows, consultez [Workflows d’approvisionnement du circuit ExpressRoute et états du circuit](expressroute-workflows.md).
- Configurez votre connexion ExpressRoute.

	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
	- [Configuration du routage](expressroute-howto-routing-arm.md)
	- [Liaison d’un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->