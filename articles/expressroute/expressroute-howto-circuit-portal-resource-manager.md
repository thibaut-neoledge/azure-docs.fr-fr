---
title: "Créer et modifier un circuit ExpressRoute à l’aide du portail Azure | Microsoft Docs"
description: "Cet article explique comment créer, approvisionner, vérifier, mettre à jour, supprimer et déprovisionner un circuit ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 2e6c204a1b520f5994c30c518f7ced11d77bde24
ms.lasthandoff: 05/02/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>Création et modification d’un circuit ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

Cet article explique comment créer un circuit ExpressRoute à l’aide du portail Azure et du modèle de déploiement Azure Resource Manager. Les étapes suivantes vous montreront également comment vérifier l’état du circuit, mettre à jour celui-ci, le supprimer et annuler son approvisionnement.


## <a name="before-you-begin"></a>Avant de commencer
* Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vérifiez que vous avez accès au [portail Azure](https://portal.azure.com).
* Assurez-vous que vous disposez des autorisations nécessaires pour créer des ressources réseau. Contactez votre administrateur de compte si vous n'avez pas les autorisations appropriées.
* Vous pouvez [visualiser une vidéo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) avant de commencer afin de mieux comprendre les étapes.

## <a name="create-and-provision-an-expressroute-circuit"></a>Création et approvisionnement d’un circuit ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Connectez-vous au portail Azure.
Dans un navigateur, accédez au [portail Azure](http://portal.azure.com) et connectez-vous avec votre compte Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Créez un circuit ExpressRoute
> [!IMPORTANT]
> Votre circuit ExpressRoute sera facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.
> 
> 

1. Vous pouvez créer un circuit ExpressRoute en sélectionnant l'option permettant de créer une ressource. Cliquez sur **Nouveau** > **Mise en réseau** > **ExpressRoute**, comme illustré dans l’image suivante :
   
    ![Création d’un circuit ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Après avoir cliqué sur **ExpressRoute**, vous voyez s’afficher le panneau **Créer un circuit ExpressRoute**. Quand vous remplissez les valeurs de ce panneau, veillez à spécifier le bon niveau de référence (SKU) et la bonne limitation des données.
   
   * **niveau** détermine si ExpressRoute standard ou un module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier **Standard** pour obtenir la référence (SKU) standard ou **Premium** pour le module complémentaire Premium.
   * **Limitation des données** détermine le type de facturation. Vous pouvez spécifier **Limité** pour un forfait de données limité, et **Illimité** pour un forfait de données illimité. Notez que vous pouvez changer le type de facturation de **Limité** en **Illimité**, mais que vous ne pouvez pas le changer de **Illimité** en **Limité**.
     
     ![Configurer le niveau de référence (SKU) et la limitation des données](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> N’oubliez pas que l’emplacement d’homologation indique [l’emplacement physique](expressroute-locations.md) où vous vous homologuez auprès de Microsoft. Cet emplacement n’est **pas** lié à la propriété « Emplacement », qui fait référence à la zone géographique où se trouve le fournisseur de ressources réseau Azure. Bien que ces éléments ne soient pas liés, nous vous conseillons de choisir un fournisseur de ressources réseau géographiquement proche de l’emplacement d’homologation du circuit. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. Afficher les circuits et les propriétés
**Afficher tous les circuits**

Vous pouvez afficher tous les circuits que vous avez créés en sélectionnant **Toutes les ressources** dans le menu de gauche.

![Afficher les circuits](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Afficher les propriétés**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Afficher les propriétés](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement
Dans ce panneau, **Statut du fournisseur** fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. **Statut du circuit** indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez l’article [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

Statu du fournisseur : Non approvisionné<BR>
Statut du circuit : Activé

![Lancer le processus d’approvisionnement](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous.

Statut du fournisseur : En cours d’approvisionnement <BR>
Statut du circuit : Activé

Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

Statut du fournisseur : Approvisionné<BR>
Statut du circuit : Activé

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Vérifier régulièrement le statut et l’état de la clé du circuit
Vous pouvez afficher les propriétés du circuit qui vous intéressent en le sélectionnant. Vérifiez le **Statut du fournisseur** et vérifiez qu’il est passé à **Approvisionné** avant de continuer.

![Statut du circuit et du fournisseur](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Créer votre configuration de routage
Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) pour créer et modifier des homologations de circuit.

> [!IMPORTANT]
> Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Lier un réseau virtuel à un circuit ExpressRoute
Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-arm.md) quand vous travaillez avec le modèle de déploiement Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Récupération de l’état d’un circuit ExpressRoute
Vous pouvez afficher l’état d’un circuit en le sélectionnant. 

![Statut d’un circuit ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Modification d’un circuit ExpressRoute
Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

* Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
* Augmenter la bande passante de votre circuit ExpressRoute à condition que la capacité disponible sur le port le permette. Notez que la rétrogradation de la bande passante d'un circuit n'est pas prise en charge. 
* Modifiez le plan de mesure de Données limitées à Données illimitées. Notez que la modification du plan de limitation de Données illimitées à Données limitées n’est pas prise en charge.
* Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md).

Pour modifier un circuit ExpressRoute, cliquez sur la **Configuration** comme illustré dans la figure ci-dessous.

![Modifier le circuit](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

Vous pouvez modifier la bande passante, la référence (SKU), le modèle de facturation et autoriser les opérations classiques dans le panneau de configuration.

> [!IMPORTANT]
> Vous devrez peut-être recréer le circuit ExpressRoute si la capacité sur le port existant est inappropriée. Vous ne pouvez pas mettre le circuit à niveau si aucune capacité supplémentaire n’est disponible à cet emplacement.
>
> Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à annuler l’approvisionnement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.
> 
> L’opération de désactivation du module complémentaire Premium peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation de l’approvisionnement et suppression d’un circuit ExpressRoute
Vous pouvez supprimer votre circuit ExpressRoute en sélectionnant l’icône **Supprimer** . Notez les points suivants :

* Vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.
* Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est **En cours d’approvisionnement** ou **Approvisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.
* Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur **Non approvisionné**), vous pouvez supprimer le circuit. Cette opération arrêtera la facturation du circuit

## <a name="next-steps"></a>Étapes suivantes
Après avoir créé votre circuit, effectuez les opérations suivantes :

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)


