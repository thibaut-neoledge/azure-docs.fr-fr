---
title: "Déplacer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager : PowerShell : Azure | Microsoft Docs"
description: "Cette page décrit comment déplacer un circuit classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 08152836-23e7-42d1-9a56-8306b341cd91
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/03/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 6d11b75fdd33260be3d975d9bc25fdac3cf22b49
ms.openlocfilehash: 73f42b25d667f07205e7e67556c367f1a0e6e215


---
# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model-using-powershell"></a>Déplacer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager à l’aide de PowerShell

Pour utiliser un circuit ExpressRoute pour les modèles de déploiement classique et Resource Manager, vous devez déplacer ce circuit vers le modèle de déploiement Resource Manager. Les sections suivantes vous expliquent toutes les étapes pour déplacer votre circuit à l’aide de PowerShell.

## <a name="before-you-begin"></a>Avant de commencer
* Vérifiez que vous disposez de la dernière version des modules Azure PowerShell (au moins la version 1.0). Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Vous devez avoir bien compris les limites et les limitations.
* Vérifiez que le circuit est totalement opérationnel dans le modèle de déploiement classique.
* Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.

## <a name="move-an-expressroute-circuit"></a>Déplacer un circuit ExpressRoute

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Étape 1 : Collecter des informations sur le circuit à partir du modèle de déploiement classique
Connectez-vous à l’environnement classique Azure et collectez la clé de service.

1. Connectez-vous à votre compte Azure.

        Add-AzureAccount

2. Sélectionnez l’abonnement Azure approprié.

        Select-AzureSubscription "<Enter Subscription Name here>"

3. Importez les modules PowerShell pour Azure et ExpressRoute.

        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
        Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

4. Utilisez l’applet de commande ci-dessous pour obtenir les clés de service pour tous les circuits imprimés ExpressRoute. Après avoir récupéré les clés, copiez la **clé de service** du circuit que vous souhaitez déplacer vers le modèle de déploiement Resource Manager.

        Get-AzureDedicatedCircuit

### <a name="step-2-sign-in-and-create-a-resource-group"></a>Étape 2 : connexion et création d’un groupe de ressources
Connectez-vous à l’environnement Resource Manager et créez un groupe de ressources.

1. Connectez-vous à votre environnement Azure Resource Manager.

        Login-AzureRmAccount

2. Sélectionnez l’abonnement Azure approprié.

        Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

3. Modifiez l’extrait de code ci-dessous pour créer un groupe de ressources si vous n’en avez pas déjà un.

        New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Étape 3 : Transférer le circuit ExpressRoute vers le modèle de déploiement Resource Manager
Vous êtes maintenant prêt à déplacer votre circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager. Avant de continuer, passez en revue les informations fournies sous [Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md).

Pour déplacer votre circuit, modifiez et exécutez l’extrait de code suivant :

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

> [!NOTE]
> Une fois le déplacement terminé, le nouveau nom répertorié dans l’applet de commande précédente sera utilisé pour traiter la ressource. Le circuit sera essentiellement renommé.
> 

## <a name="modify-circuit-access"></a>Modifier l’accès d’un circuit

### <a name="to-enable-expressroute-circuit-access-for-both-deployment-models"></a>Pour activer l’accès du circuit ExpressRoute pour les deux modèles de déploiement
Après avoir déplacé votre circuit ExpressRoute classique vers le modèle de déploiement Resource Manager, vous pouvez activer l’accès aux deux modèles de déploiement. Exécutez les applets de commande suivantes pour activer l’accès aux deux modèles de déploiement :

1. Obtenez les informations sur le circuit.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. Définissez « Autoriser les opérations classiques » sur TRUE.

        $ckt.AllowClassicOperations = $true

3. Mettez à jour le circuit. Une fois cette opération terminée avec succès, vous serez en mesure d’afficher le circuit dans le modèle de déploiement classique.

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

4. Exécutez l’applet de commande suivante pour obtenir les informations concernant le circuit ExpressRoute. Vous devez être en mesure de voir la clé de service répertoriée. 

        get-azurededicatedcircuit

5. Vous pouvez maintenant gérer les liens au circuit ExpressRoute à l’aide des commandes du modèle de déploiement classique pour les réseaux virtuels classiques, et des commandes Resource Manager pour les réseaux virtuels Resource Manager. Les articles suivants vont vous guider tout au long de la gestion des liens vers le circuit ExpressRoute :

    * [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement Resource Manager](expressroute-howto-linkvnet-arm.md)
    * [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md)

### <a name="to-disable-expressroute-circuit-access-to-the-classic-deployment-model"></a>Pour désactiver l’accès du circuit ExpressRoute au modèle de déploiement classique
Exécutez les applets de commande suivantes pour désactiver l’accès au modèle de déploiement classique.

1. Obtenez les informations concernant le circuit ExpressRoute.

        $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

2. Définissez « Autoriser les opérations classiques » sur FALSE.

        $ckt.AllowClassicOperations = $false

3. Mettez à jour le circuit. Une fois cette opération terminée avec succès, vous ne serez pas en mesure d’afficher le circuit dans le modèle de déploiement classique.

        Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## <a name="next-steps"></a>Étapes suivantes

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Feb17_HO1-->


