<properties
   pageTitle="Migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager | Microsoft Azure"
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
   ms.date="06/20/2016"
   ms.author="ganesr"/>

# Migration de circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager

Cet article décrit les enjeux de la migration d’un circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Un circuit ExpressRoute permet de se connecter à des réseaux virtuels déployés à la fois dans les modèles classique et Resource Manager. Quel que soit son mode de création, un circuit ExpressRoute peut désormais se relier à des réseaux virtuels dans les deux modèles de déploiement.

![Un circuit ExpressRoute qui se lie à des réseaux virtuels dans les deux modèles de déploiement](./media/expressroute-move/expressroute-move-1.png)

## Circuits ExpressRoute créés dans le modèle de déploiement classique

Les circuits ExpressRoute créés dans le modèle de déploiement classique doivent d’abord être migrés vers le modèle de déploiement Resource Manager, afin d’assurer la connectivité aux modèles de déploiement classique et Resource Manager. La migration d’une connexion n’entraîne ni perte ni interruption de la connectivité. Dans le modèle de déploiement classique, toutes les liaisons d’un circuit à un réseau virtuel, que ce soit dans le même abonnement ou dans différents abonnements, sont conservées.

Une fois la migration effectuée, le circuit ExpressRoute se présente, s’exécute et se comporte comme un circuit ExpressRoute créé dans le modèle de déploiement Resource Manager. Vous pouvez maintenant créer des connexions à des réseaux virtuels dans le modèle de déploiement Resource Manager.

Une fois le circuit ExpressRoute migré vers le modèle de déploiement Resource Manager, vous ne pouvez gérer son cycle de vie qu’avec le modèle de déploiement Resource Manager. Autrement dit, les opérations telles que l’ajout, la mise à jour ou la suppression d’homologations, la mise à jour des propriétés d’un circuit (bande passante, référence (SKU) et type de facturation) et la suppression de circuits ne sont possibles que dans le modèle de déploiement Resource Manager. Pour plus d’informations sur la gestion de l’accès aux deux modèles de déploiement, consultez la section ci-dessous relative aux circuits créés dans le modèle de déploiement Resource Manager.

La procédure de migration ne nécessite aucune intervention de votre fournisseur de connectivité.

## Circuits ExpressRoute créés dans le modèle de déploiement Resource Manager

Vous pouvez rendre les circuits ExpressRoute créés dans le modèle de déploiement Resource Manager, accessibles à partir des deux modèles de déploiement. Tout circuit ExpressRoute de votre abonnement peut être accessible à partir de ces deux modèles de déploiement.

- Par défaut, les circuits ExpressRoute créés dans le modèle de déploiement Resource Manager n’ont pas accès au modèle de déploiement classique.
- Les circuits ExpressRoute migrés du modèle de déploiement classique vers le modèle de déploiement Resource Manager sont, par défaut, accessibles à partir des deux modèles de déploiement.
- Un circuit ExpressRoute a toujours accès au modèle de déploiement Resource Manager, qu’il ait été créé dans le modèle de déploiement Resource Manager ou dans le modèle de déploiement classique. Cela signifie que vous pouvez créer des connexions à des réseaux virtuels dans le modèle de déploiement Resource Manager, en suivant les instructions concernant la [liaison à des réseaux virtuels](expressroute-howto-linkvnet-arm.md).
- L’accès au modèle de déploiement classique est régi par le paramètre **allowClassicOperations** du circuit ExpressRoute.

>[AZURE.IMPORTANT] Tous les quotas décrits sur la page dédiée aux [limites de service](../azure-subscription-service-limits.md) s’appliquent. Par exemple, un circuit standard peut avoir jusqu’à 10 liaisons/connexions de réseau virtuel dans les modèles de déploiement classique et Resource manager.


## Contrôle de l’accès au modèle de déploiement classique

Vous pouvez autoriser un circuit ExpressRoute à se connecter à des réseaux virtuels dans les deux modèles de déploiement, en configurant son paramètre **allowClassicOperations**.

Si vous attribuez la valeur TRUE au paramètre **allowClassicOperations**, vous pouvez connecter des réseaux virtuels des deux modèles de déploiement au circuit ExpressRoute. Vous pouvez vous connecter à des réseaux virtuels dans le modèle de déploiement classique en suivant les instructions sur la [liaison de réseaux virtuels dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md). Vous pouvez vous connecter à des réseaux virtuels dans le modèle de déploiement Resource Manager, en suivant les instructions sur la [liaison de réseaux virtuels dans le modèle de déploiement Resource Manager](expressroute-howto-linkvnet-arm.md).

Si vous attribuez la valeur FALSE au paramètre **allowClassicOperations**, le circuit n’est pas accessible à partir du modèle de déploiement classique. Toutes les liaisons de réseau virtuel dans le modèle de déploiement classique sont cependant conservées. Dans ce cas, le circuit ExpressRoute n’est pas visible dans le modèle de déploiement classique.

## Opérations prises en charge dans le modèle de déploiement classique

Les opérations classiques suivantes sont prises en charge sur un circuit ExpressRoute si vous attribuez la valeur TRUE au paramètre **allowClassicOperations**.

 - Obtenir des informations sur le circuit ExpressRoute
 - Créer/mettre à jour/obtenir/supprimer des liaisons à des réseaux virtuels classiques
 - Créer/mettre à jour/obtenir/supprimer des autorisations de liaison de réseau virtuel pour une connectivité entre plusieurs abonnements

Les opérations classiques suivantes sont impossibles si vous attribuez la valeur TRUE au paramètre **allowClassicOperations**.

 - Créer/mettre jour/obtenir/supprimer des homologations BGP (Border Gateway Protocol) pour les homologations privées Azure, les homologations publiques Azure et les homologations Microsoft
 - Supprimer des circuits ExpressRoute

## Communication entre les modèles de déploiement classique et Resource Manager

Le circuit ExpressRoute se comporte comme un pont entre les modèles de déploiement classique et Resource Manager. Le trafic entre les machines virtuelles des réseaux virtuels dans le modèle de déploiement classique et celles des réseaux virtuels dans le modèle de déploiement Resource Manager transite via ExpressRoute si les deux réseaux virtuels sont reliés au même circuit ExpressRoute.

Le débit cumulé est limité par le débit de la passerelle du réseau virtuel. Dans ce cas, le trafic n’entre ni dans vos réseaux ni dans ceux de votre fournisseur de connectivité. Le trafic entre les réseaux virtuels reste entièrement confiné dans le réseau Microsoft.

## Accès aux ressources d’homologation publiques Azure et Microsoft

Vous conservez l’accès aux ressources généralement accessibles via l’homologation publique Azure et l’homologation Microsoft, sans interruption.

## Opérations prises en charge

Cette section décrit les opérations prises en charge pour les circuits ExpressRoute :

 - Vous pouvez utiliser un circuit ExpressRoute pour accéder aux réseaux virtuels déployés dans les modèles classique et Resource Manager.
 - Vous pouvez migrer un circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager. Après sa migration, le circuit ExpressRoute se présente, s’exécute et se comporte comme n’importe quel circuit ExpressRoute créé dans le modèle de déploiement Resource Manager.
 - Seul le circuit ExpressRoute peut faire l’objet d’une migration. Cette opération ne permet pas de migrer les liaisons de circuit, les réseaux virtuels et les passerelles VPN.
 - Une fois le circuit ExpressRoute migré vers le modèle de déploiement Resource Manager, vous ne pouvez gérer son cycle de vie qu’avec le modèle de déploiement Resource Manager. Autrement dit, les opérations telles que l’ajout, la mise à jour ou la suppression d’homologations, la mise à jour des propriétés d’un circuit (bande passante, référence (SKU) et type de facturation) et la suppression de circuits ne sont possibles que dans le modèle de déploiement Resource Manager.
 - Le circuit ExpressRoute se comporte comme un pont entre les modèles de déploiement classique et Resource Manager. Le trafic entre les machines virtuelles des réseaux virtuels dans le modèle de déploiement classique et celles des réseaux virtuels dans le modèle de déploiement Resource Manager transite via ExpressRoute si les deux réseaux virtuels sont reliés au même circuit ExpressRoute.
 - La connectivité entre différents abonnements est prise en charge dans les modèles de déploiement classique et Resource Manager.

## Ce qui n'est pas pris en charge

Cette section décrit les opérations non prises en charge pour les circuits ExpressRoute :

 - Migration des liaisons de circuit, passerelles et réseaux virtuels du modèle de déploiement classique au modèle de déploiement Resource Manager.
 - Gestion du cycle de vie d’un circuit ExpressRoute à partir du modèle de déploiement classique.
 - Prise en charge du contrôle RBAC (Role-Based Access Control) dans le modèle de déploiement classique. Vous ne pouvez effectuer aucun contrôle RBAC sur un circuit dans le modèle de déploiement classique. Un administrateur/coadministrateur de l’abonnement peut lier des réseaux virtuels au circuit (ou les en dissocier).

## Configuration

Suivez les instructions dans [Migration d’un circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-howto-move-arm.md).

## Étapes suivantes

- Pour plus d’informations sur les workflows, consultez [Workflows d’approvisionnement du circuit ExpressRoute et états du circuit](expressroute-workflows.md).
- Pour configurer votre connexion ExpressRoute :

	- [Création d’un circuit ExpressRoute](expressroute-howto-circuit-arm.md)
	- [Configuration du routage](expressroute-howto-routing-arm.md)
	- [Lier un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0622_2016-->