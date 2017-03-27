---
title: "Créer et modifier un circuit ExpressRoute avec PowerShell et le portail Azure Classic | Microsoft Docs"
description: "Cet article vous guide tout au long des étapes de création et d’approvisionnement d&quot;un circuit ExpressRoute. Cet article vous montre également comment vérifier l&quot;état, mettre à jour ou supprimer et annuler l’approvisionnement de votre circuit."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0134d242-6459-4dec-a2f1-4657c3bc8b23
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: ganesr;cherylmc
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 62ecd4cc2eed8623cab75777605d621e16b99977
ms.lasthandoff: 03/14/2017


---
# <a name="create-and-modify-an-expressroute-circuit"></a>Création et modification d’un circuit ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-circuit-arm.md)
> * [Classic - PowerShell](expressroute-howto-circuit-classic.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> 
>

Cet article vous guide dans les étapes de création d’un circuit Azure ExpressRoute à l’aide d’applets de commande PowerShell et du modèle de déploiement classique. Cet article vous montrera également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement d'un circuit ExpressRoute.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]


**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-you-begin"></a>Avant de commencer
### <a name="step-1-review-the-prerequisites-and-workflow-articles"></a>Étape 1. Passez en revue les conditions préalables et les articles sur le flux de travail
Veillez à consulter les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.  

### <a name="step-2-install-the-latest-versions-of-the-azure-powershell-modules"></a>Étape 2. Installez les dernières versions des modules Azure PowerShell
Suivez les instructions de [Comment installer et configurer Azure PowerShell](/powershell/azureps-cmdlets-docs) pour des étapes pas à pas permettant de configurer votre ordinateur pour l’utilisation des modules Azure PowerShell.

### <a name="step-3-log-in-to-your-azure-account-and-select-a-subscription"></a>Étape 3. Connectez-vous à votre compte Azure et sélectionnez un abonnement
1. Dans une invite Windows PowerShell exécutée avec des privilèges élevés, exécutez l’applet de commande suivante :
   
        Add-AzureAccount
2. Dans l’écran de connexion qui apparaît, connectez-vous à votre compte.
3. Obtenez la liste de vos abonnements.
   
        Get-AzureSubscription
4. Sélectionnez l’abonnement à utiliser.
   
        Select-AzureSubscription -SubscriptionName "mysubscriptionname"

## <a name="create-and-provision-an-expressroute-circuit"></a>Création et approvisionnement d’un circuit ExpressRoute
### <a name="step-1-import-the-powershell-modules-for-expressroute"></a>Étape 1. Importer les modules PowerShell pour ExpressRoute
 Si vous ne l’avez pas encore fait, vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous importez les modules à partir de l’emplacement où ils ont été installés sur votre ordinateur local. Selon la méthode utilisée pour installer les modules, l’emplacement peut être différent de l’exemple suivant. Modifiez l’exemple, si nécessaire.  

    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### <a name="step-2-get-the-list-of-supported-providers-locations-and-bandwidths"></a>Étape 2. Récupérer la liste des fournisseurs, des emplacements et des bandes passantes pris en charge
Avant de créer un circuit ExpressRoute, vous avez besoin d’une liste des fournisseurs de services, des emplacements et des options de bande passante pris en charge.

L’applet de commande PowerShell `Get-AzureDedicatedCircuitServiceProvider` retourne ces informations que vous utilisez dans les étapes ultérieures :

    Get-AzureDedicatedCircuitServiceProvider

Vérifiez si votre fournisseur de connectivité y est référencé. Prenez note des éléments suivants, car vous en avez besoin plus tard lors de la création d’un circuit :

* Nom
* PeeringLocations
* BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.         

### <a name="step-3-create-an-expressroute-circuit"></a>Étape 3. Création d’un circuit ExpressRoute
L’exemple suivant montre comment créer un circuit ExpressRoute de 200 Mb/s par le biais d’Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, utilisez ces informations quand vous créez votre requête.

> [!IMPORTANT]
> Votre circuit ExpressRoute sera facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.
> 
> 

Voici un exemple de demande pour une nouvelle clé de service :

    $Bandwidth = 200
    $CircuitName = "MyTestCircuit"
    $ServiceProvider = "Equinix"
    $Location = "Silicon Valley"

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Si vous voulez créer un circuit ExpressRoute avec le module complémentaire Premium, utilisez l’exemple suivant. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ ExpressRoute](expressroute-faqs.md) .

    New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La réponse contiendra la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help new-azurededicatedcircuit -detailed

### <a name="step-4-list-all-the-expressroute-circuits"></a>Étape 4. Répertorier tous les circuits ExpressRoute
Vous pouvez exécuter la commande `Get-AzureDedicatedCircuit` pour obtenir la liste de tous les circuits ExpressRoute que vous avez créés :

    Get-AzureDedicatedCircuit

La réponse sera similaire à l’exemple suivant :

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande `Get-AzureDedicatedCircuit` . Un appel effectué sans aucun paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey* .

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : NotProvisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

    get-help get-azurededicatedcircuit -detailed

### <a name="step-5-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>Étape 5. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement
*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. *statut* indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez l’article [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous :

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled

Un circuit ExpressRoute doit être dans l’état suivant pour pouvoir être utilisé.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled


### <a name="step-6-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Étape 6. Vérifier régulièrement le statut et l’état de la clé du circuit
Cela vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* apparaît *Approvisionné*, comme le montre l’exemple suivant :

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

### <a name="step-7-create-your-routing-configuration"></a>Étape 7. Créer votre configuration de routage
Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute (créer et modifier des homologations de circuit)](expressroute-howto-routing-classic.md) .

> [!IMPORTANT]
> Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.
> 
> 

### <a name="step-8-link-a-virtual-network-to-an-expressroute-circuit"></a>Étape 8 : Lier un réseau virtuel à un circuit ExpressRoute
Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Pour obtenir des instructions pas à pas, voir [Liaison de circuits ExpressRoute à des réseaux virtuels](expressroute-howto-linkvnet-classic.md) . Si vous avez besoin de créer un réseau virtuel à l’aide du modèle de déploiement classique pour ExpressRoute, consultez [Créer un réseau virtuel pour ExpressRoute](expressroute-howto-vnet-portal-classic.md) pour obtenir des instructions.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Récupération de l’état d’un circuit ExpressRoute
Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande `Get-AzureCircuit` . Un appel effectué sans aucun paramètre répertorie tous les circuits.

    Get-AzureDedicatedCircuit

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

    Bandwidth                        : 1000
    CircuitName                      : MyAsiaCircuit
    Location                         : Singapore
    ServiceKey                       : #################################
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en passant, en tant que paramètre, la clé de service à l’appel.

    Get-AzureDedicatedCircuit -ServiceKey "*********************************"

    Bandwidth                        : 200
    CircuitName                      : MyTestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant l’exemple suivant :

    get-help get-azurededicatedcircuit -detailed

## <a name="modifying-an-expressroute-circuit"></a>Modification d’un circuit ExpressRoute
Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

* Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
* Augmenter la bande passante de votre circuit ExpressRoute à condition que la capacité disponible sur le port le permette. Notez que la rétrogradation de la bande passante d'un circuit n'est pas prise en charge. 
* Modifiez le plan de mesure de Données limitées à Données illimitées. Notez que la modification du plan de limitation de Données illimitées à Données limitées n’est pas prise en charge.
* Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md) .

### <a name="to-enable-the-expressroute-premium-add-on"></a>Pour activer le module complémentaire ExpressRoute Premium
Vous pouvez activer le module complémentaire ExpressRoute Premium pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Premium
    Status                           : Enabled

Les fonctionnalités du module complémentaire ExpressRoute premium seront activées sur votre circuit. Notez que nous commencerons à vous facturer la fonctionnalité du module complémentaire premium dès que la commande aura été exécutée avec succès.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Pour désactiver le module complémentaire ExpressRoute Premium
> [!IMPORTANT]
> Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.
> 
> 

#### <a name="considerations"></a>Considérations

* Vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10 avant de rétrograder du niveau premium à standard. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.
* Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.
* Pour l’homologation privée, votre table de routage doit comporter moins de 4 000 routages. Si la table de routage comporte plus de 4 000 routages, la session BGP s’arrête et n’est pas réactivée tant que le nombre de préfixes publiés n’est pas inférieur à 4 000.

#### <a name="disable-the-premium-add-on"></a>Désactiver le module complémentaire Premium
Vous pouvez désactiver le module complémentaire ExpressRoute Premium pour votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

    Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled



### <a name="to-update-the-expressroute-circuit-bandwidth"></a>Pour mettre à jour la bande passante d’un circuit ExpressRoute
Pour connaître les options de bande passante prises en charge par votre fournisseur, consultez le [FAQ ExpressRoute](expressroute-faqs.md) . Vous pouvez choisir toute taille supérieure à celle de votre circuit existant, pour autant que le port physique (sur lequel votre circuit est créé) le permette.

> [!IMPORTANT]
> Vous devrez peut-être recréer le circuit ExpressRoute si la capacité sur le port existant est inappropriée. Vous ne pouvez pas mettre le circuit à niveau si aucune capacité supplémentaire n’est disponible à cet emplacement.
>
> Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à annuler l’approvisionnement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.
> 
> 

#### <a name="resize-a-circuit"></a>Redimensionner un circuit

Une fois que vous avez décidé de la taille dont vous avez besoin, vous pouvez utiliser la commande suivante pour redimensionner votre circuit :

    Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000

    Bandwidth                        : 1000
    CircuitName                      : TestCircuit
    Location                         : Silicon Valley
    ServiceKey                       : *********************************
    ServiceProviderName              : equinix
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Votre circuit sera redimensionné du côté de Microsoft. Vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations de son côté afin de refléter cette modification. Notez que nous allons commencer à vous facturer la bande bande passante mise à jour à partir de cet instant.

Si l’erreur suivante s’affiche lors de l’augmentation de la bande passante du circuit, cela signifie qu’il ne reste pas suffisamment de bande passante sur le port physique sur lequel votre circuit est créé. Vous devez supprimer ce circuit et en créer un nouveau de la taille nécessaire. 

    Set-AzureDedicatedCircuitProperties : InvalidOperation : Insufficient bandwidth available to perform this circuit
    update operation
    At line:1 char:1
    + Set-AzureDedicatedCircuitProperties -ServiceKey ********************* ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Set-AzureDedicatedCircuitProperties], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.SetAzureDedicatedCircuitPropertiesCommand


## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Annulation de l’approvisionnement et suppression d’un circuit ExpressRoute

### <a name="considerations"></a>Considérations

* Vous devez annuler la liaison de tous les réseaux virtuels du circuit ExpressRoute pour que cette opération réussisse. Si cette opération échoue, vérifiez si des réseaux virtuels sont liés au circuit.
* Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est **En cours d’approvisionnement** ou **Approvisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.
* Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur **Non approvisionné**), vous pouvez supprimer le circuit. Cette opération arrêtera la facturation du circuit.

#### <a name="delete-a-circuit"></a>Supprimer un circuit

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

    Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## <a name="next-steps"></a>Étapes suivantes
Après avoir créé votre circuit, effectuez les opérations suivantes :

* [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-classic.md)
* [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)


