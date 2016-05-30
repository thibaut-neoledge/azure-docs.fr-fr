<properties
   pageTitle="Créer et modifier un circuit ExpressRoute à l’aide de Resource Manager et du portail Azure | Microsoft Azure"
   description="Cet article explique comment créer, approvisionner, vérifier, mettre à jour, supprimer et déprovisionner un circuit ExpressRoute."
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
   ms.date="04/15/2016"
   ms.author="cherylmc"/>

# Création et modification d’un circuit ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

Cet article explique comment créer un circuit ExpressRoute à l’aide du portail Azure et du modèle de déploiement Azure Resource Manager. Les étapes suivantes vous montreront également comment vérifier l’état du circuit, mettre à jour celui-ci, le supprimer et annuler son approvisionnement.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Avant de commencer


- Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Vérifiez que vous avez accès au [portail Azure](https://portal.azure.com).
- Assurez-vous que vous disposez des autorisations nécessaires pour créer des ressources réseau. Contactez votre administrateur de compte si vous n'avez pas les autorisations appropriées.

## Création et approvisionnement d’un circuit ExpressRoute

### 1\. Connectez-vous au portail Azure.

Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.

### 2\. Créez un circuit ExpressRoute

>[AZURE.IMPORTANT] Votre circuit ExpressRoute sera facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.

1. Vous pouvez créer un circuit ExpressRoute en sélectionnant l'option permettant de créer une ressource. Cliquez sur **Nouveau** > **Mise en réseau** > **ExpressRoute**, comme l’illustre l’image suivante :

	![Création d’un circuit ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)

2. Après avoir cliqué sur **ExpressRoute**, vous voyez s’afficher le panneau **Créer un circuit ExpressRoute**. Quand vous remplissez les valeurs de ce panneau, veillez à spécifier le bon niveau de référence (SKU) et la bonne limitation des données.

	- Le **niveau** détermine si ExpressRoute standard ou un module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier **Standard** pour obtenir la référence (SKU) standard ou **Premium** pour le module complémentaire Premium.

	- **Limitation des données** détermine le type de facturation. Vous pouvez spécifier **Limité** pour un forfait de données limité et **Illimité** pour un forfait de données illimité. Notez que vous pouvez changer le type de facturation de **Limité** en **Illimité**, mais que vous ne pouvez pas le changer de **Illimité** en **Limité**.

	![Configurer le niveau de référence (SKU) et la limitation des données](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)


### 3\. Afficher les circuits et les propriétés

**Afficher tous les circuits**

Vous pouvez afficher tous les circuits que vous avez créés en sélectionnant **Toutes les ressources** dans le menu de gauche.
	
![Afficher les circuits](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Afficher les propriétés**

	You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Afficher les propriétés](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


### 4\. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement

Dans ce panneau, **Statut du fournisseur** fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. **Statut du circuit** indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez l’article [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

Statu du fournisseur : Non approvisionné<BR> Statut du circuit : Activé

![Lancer le processus d’approvisionnement](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous.

Statut du fournisseur : En cours d’approvisionnement <BR> Statut du circuit : Activé

Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

Statut du fournisseur : Approvisionné<BR> Statut du circuit : Activé


### 5\. Vérifier régulièrement le statut et l’état de la clé du circuit

Vous pouvez afficher les propriétés du circuit qui vous intéressent en le sélectionnant. Vérifiez le **Statut du fournisseur** et vérifiez qu’il est passé à **Approvisionné** avant de continuer.


![Statut du circuit et du fournisseur](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)


### 6\. Créer votre configuration de routage

Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) pour créer et modifier des homologations de circuit.

>[AZURE.IMPORTANT] Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

### 7\. Lier un réseau virtuel à un circuit ExpressRoute

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-arm.md) quand vous travaillez avec le modèle de déploiement Resource Manager.

## Récupération de l’état d’un circuit ExpressRoute

Vous pouvez afficher l’état d’un circuit en le sélectionnant.

![Statut d’un circuit ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)


## Modification d’un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité. Pour le moment, vous ne pouvez pas modifier les propriétés du circuit ExpressRoute à l'aide du portail Azure. Toutefois, vous pouvez utiliser PowerShell pour modifier les propriétés du circuit. Pour plus d’informations, consultez la section [Modification d’un circuit ExpressRoute à l’aide de PowerShell](expressroute-howto-circuit-arm.md#modify).

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

- Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.

- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la rétrogradation de la bande passante d'un circuit n'est pas prise en charge.

- Modifiez le plan de mesure de Données limitées à Données illimitées. Notez que la modification du plan de limitation de Données illimitées à Données limitées n’est pas prise en charge.

-  Vous pouvez activer et désactiver **Autoriser les opérations classiques**.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md).


## Suppression et annulation du provisionnement d'un circuit ExpressRoute

Vous pouvez supprimer votre circuit ExpressRoute en sélectionnant l’icône **Supprimer**. Notez les points suivants :

- Vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

- Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est Activé, l’approvisionnement passe de l’état Activé à l’état **Désactivation**. Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.

- Si le fournisseur de services a désapprovisionné le circuit (l’état d’approvisionnement du fournisseur de services est **Non approvisionné**) avant que vous exécutiez l’applet de commande précédente, nous désapprovisionnons le circuit et nous cessons de vous facturer.

## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0518_2016-->