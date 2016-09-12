<properties
   pageTitle="Créer et modifier un circuit ExpressRoute à l’aide de Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article explique comment créer, approvisionner, vérifier, mettre à jour, supprimer et déprovisionner un circuit ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="ganesr"/>


# Création et modification d’un circuit ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)


Cet article explique comment créer un circuit ExpressRoute à l’aide des applets de commande Windows PowerShell et du modèle de déploiement Azure Resource Manager. Cet article vous montrera également comment vérifier l’état du circuit, le mettre à jour, le supprimer et annuler son approvisionnement.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Avant de commencer


- Procurez-vous la dernière version des modules Azure PowerShell (au moins la version 1.0). Pour connaître les étapes de configuration de votre ordinateur afin d’utiliser les modules Azure PowerShell, suivez les instructions de [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).

- Examinez les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

## Création et approvisionnement d’un circuit ExpressRoute

### 1. Vous connecter à votre compte Azure et sélectionner votre abonnement

Pour commencer votre configuration, connectez-vous à votre compte Azure. Pour plus d'informations sur PowerShell, consultez la page [Utilisation de Windows PowerShell avec Resource Manager](../powershell-azure-resource-manager.md). Utilisez les exemples suivants pour faciliter votre connexion :

	Login-AzureRmAccount

Vérifiez les abonnements du compte :

	Get-AzureRmSubscription

Sélectionnez l’abonnement pour lequel vous voulez créer un circuit ExpressRoute :

	Select-AzureRmSubscription -SubscriptionId "<subscription ID>"

### 2. Récupérer la liste des fournisseurs, des emplacements et des bandes passantes pris en charge

Avant de créer un circuit ExpressRoute, vous avez besoin d’une liste des fournisseurs de services, des emplacements et des options de bande passante pris en charge.

L’applet de commande PowerShell `Get-AzureRmExpressRouteServiceProvider` retourne ces informations que vous utilisez dans les étapes ultérieures :

	Get-AzureRmExpressRouteServiceProvider

Vérifiez si votre fournisseur de connectivité y est référencé. Prenez note des éléments suivants, car vous en avez besoin plus tard lors de la création d’un circuit :

- Name

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.

### 3. Création d’un circuit ExpressRoute

Si vous n’avez pas déjà un groupe de ressources, vous devez en créer un avant de créer votre circuit ExpressRoute. Pour cela, vous pouvez exécuter la commande suivante :


	New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"


L’exemple suivant montre comment créer un circuit ExpressRoute de 200 Mb/s par le biais d’Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, utilisez ces informations quand vous créez votre requête. Voici un exemple de demande pour une nouvelle clé de service :

	New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

Assurez-vous que vous spécifiez le niveau de référence (SKU) et la famille de références corrects :

- Le niveau de référence détermine si ExpressRoute standard ou un module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier *Standard* pour obtenir la référence (SKU) standard ou *Premium* pour le module complémentaire Premium.

- La famille de références détermine le type de facturation. Vous pouvez spécifier *Metereddata* pour définir un forfait de données limité et *Unlimiteddata* pour un forfait de données illimité. Notez que vous pouvez changer le type de facturation de *Metereddata* en *Unlimiteddata*, mais que vous ne pouvez pas le changer de *Unlimiteddata* en *Metereddata*.


>[AZURE.IMPORTANT] Votre circuit ExpressRoute sera facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.

La réponse contient la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :


	get-help New-AzureRmExpressRouteCircuit -detailed


### 4\. Répertorier tous les circuits ExpressRoute

Pour obtenir la liste de tous les circuits ExpressRoute que vous avez créés, exécutez la commande `Get-AzureRmExpressRouteCircuit` :


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

La réponse ressemblera à l’exemple suivant :


	Name                             : ExpressRouteARMCircuit
	ResourceGroupName                : ExpressRouteResourceGroup
	Location                         : westus
	Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
	Etag                             : W/"################################"
	ProvisioningState                : Succeeded
	Sku                              : {
	                                     "Name": "Standard_MeteredData",
	                                     "Tier": "Standard",
	                                     "Family": "MeteredData"
	   		                           }
	CircuitProvisioningState          : Enabled
	ServiceProviderProvisioningState  : NotProvisioned
	ServiceProviderNotes              :
	ServiceProviderProperties         : {
	                                      "ServiceProviderName": "Equinix",
	                                      "PeeringLocation": "Silicon Valley",
	                                      "BandwidthInMbps": 200
	                                    }
	ServiceKey                        : **************************************
	Peerings                          : []

Vous pouvez récupérer ces informations à tout moment en utilisant l’applet de commande `Get-AzureRmExpressRouteCircuit`. Un appel effectué sans paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey* :


	Get-AzureRmExpressRouteCircuit


La réponse ressemblera à l’exemple suivant :


	Name                             : ExpressRouteARMCircuit
	ResourceGroupName                : ExpressRouteResourceGroup
	Location                         : westus
	Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
	Etag                             : W/"################################"
	ProvisioningState                : Succeeded
	Sku                              : {
	                                     "Name": "Standard_MeteredData",
	                                     "Tier": "Standard",
	                                     "Family": "MeteredData"
	   		                           }
	CircuitProvisioningState         : Enabled
	ServiceProviderProvisioningState : NotProvisioned
	ServiceProviderNotes             :
	ServiceProviderProperties        : {
	                                     "ServiceProviderName": "Equinix",
	                                     "PeeringLocation": "Silicon Valley",
	                                     "BandwidthInMbps": 200
	   		                           }
	ServiceKey                       : **************************************
	Peerings                         : []


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :


	get-help Get-AzureRmExpressRouteCircuit -detailed

### 5. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement

*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. Le statut indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :


	ServiceProviderProvisioningState : NotProvisioned
	CircuitProvisioningState         : Enabled



Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

	ServiceProviderProvisioningState : Provisioned
	CircuitProvisioningState         : Enabled

### 6. Vérifier régulièrement le statut et l’état de la clé du circuit

La vérification du statut et de l’état de la clé du circuit vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* a la valeur *Approvisionné*, comme le montre l’exemple suivant :


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse ressemblera à l’exemple suivant :


	Name                             : ExpressRouteARMCircuit
	ResourceGroupName                : ExpressRouteResourceGroup
	Location                         : westus
	Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
	Etag                             : W/"################################"
	ProvisioningState                : Succeeded
	Sku                              : {
	                                     "Name": "Standard_MeteredData",
	                                     "Tier": "Standard",
	                                     "Family": "MeteredData"
	                                   }
	CircuitProvisioningState         : Enabled
	ServiceProviderProvisioningState : Provisioned
	ServiceProviderNotes             :
	ServiceProviderProperties        : {
	                                     "ServiceProviderName": "Equinix",
	                                     "PeeringLocation": "Silicon Valley",
	                                     "BandwidthInMbps": 200
	   	                            }
	ServiceKey                       : **************************************
	Peerings                         : []

### 7. Créer votre configuration de routage

Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute](expressroute-howto-routing-arm.md) pour créer et modifier des homologations de circuit.


>[AZURE.IMPORTANT] Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

### 8. Lier un réseau virtuel à un circuit ExpressRoute

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Utilisez l’article [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-arm.md) quand vous travaillez avec le modèle de déploiement Resource Manager.

## Récupération de l’état d’un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande `Get-AzureRmExpressRouteCircuit`. Un appel effectué sans paramètre répertorie tous les circuits.

	Get-AzureRmExpressRouteCircuit


La réponse ressemblera à ce qui suit :


	Name                             : ExpressRouteARMCircuit
	ResourceGroupName                : ExpressRouteResourceGroup
	Location                         : westus
	Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
	Etag                             : W/"################################"
	ProvisioningState                : Succeeded
	Sku                              : {
	                                     "Name": "Standard_MeteredData",
	                                     "Tier": "Standard",
	                                     "Family": "MeteredData"
	                                   }
	CircuitProvisioningState         : Enabled
	ServiceProviderProvisioningState : Provisioned
	ServiceProviderNotes             :
	ServiceProviderProperties        : {
	   		                             "ServiceProviderName": "Equinix",
	   		                             "PeeringLocation": "Silicon Valley",
	   		                             "BandwidthInMbps": 200
	   		                           }
	ServiceKey                       : **************************************
	Peerings                         : []


Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en transmettant à l’appel le nom du groupe de ressources et le nom du circuit comme paramètres :


	Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse ressemblera à l’exemple suivant :


	Name                             : ExpressRouteARMCircuit
	ResourceGroupName                : ExpressRouteResourceGroup
	Location                         : westus
	Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
	Etag                             : W/"################################"
	ProvisioningState                : Succeeded
	Sku                              : {
	                                     "Name": "Standard_MeteredData",
	   		                             "Tier": "Standard",
	   		                             "Family": "MeteredData"
	   		                           }
	CircuitProvisioningState         : Enabled
	ServiceProviderProvisioningState : Provisioned
	ServiceProviderNotes             :
	ServiceProviderProperties        : {
	                                     "ServiceProviderName": "Equinix",
	                                     "PeeringLocation": "Silicon Valley",
	                                     "BandwidthInMbps": 200
	   		                           }
	ServiceKey                       : **************************************
	Peerings                         : []


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

	get-help get-azurededicatedcircuit -detailed


## <a name="modify"></a>Modification d’un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

- Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la rétrogradation de la bande passante d'un circuit n'est pas prise en charge.
- Modifiez le plan de mesure de Données limitées à Données illimitées. Notez que la modification du plan de limitation de Données illimitées à Données limitées n’est pas prise en charge.
-  Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md).

### Pour activer le module complémentaire ExpressRoute Premium

Vous pouvez activer le module complémentaire ExpressRoute Premium pour votre circuit existant à l’aide de l’extrait de code PowerShell suivant :

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Premium"
	$ckt.sku.Name = "Premium_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Les fonctionnalités du module complémentaire ExpressRoute Premium sont ainsi activées pour votre circuit. Notez que nous commençons à vous facturer la fonctionnalité du module complémentaire Premium dès que la commande a été exécutée avec succès.

### Pour désactiver le module complémentaire ExpressRoute Premium

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Avant de passer du niveau Premium au niveau Standard, vous devez vérifier que le nombre de réseaux virtuels liés au circuit est inférieur à 10. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.

- Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.

- Pour l’homologation privée, votre table de routage doit comporter moins de 4 000 routages. Si la table de routage comporte plus de 4 000 routages, la session BGP s’arrête et ne sera pas réactivée tant que le nombre de préfixes publiés ne sera pas inférieur à 4 000.

Vous pouvez désactiver le module complémentaire ExpressRoute Premium dans votre circuit existant à l’aide de l’applet de commande PowerShell suivante :


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Tier = "Standard"
	$ckt.sku.Name = "Standard_MeteredData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


### Pour mettre à jour la bande passante d’un circuit ExpressRoute

Pour connaître les options de bande passante prises en charge par votre fournisseur, consultez le [FAQ ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n'importe quelle taille supérieure à la taille de votre circuit existant.

>[AZURE.IMPORTANT] Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à annuler l’approvisionnement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.

Une fois que vous avez décidé de la taille dont vous avez besoin, utilisez la commande suivante pour redimensionner votre circuit :


	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt


Microsoft se charge de redimensionner votre circuit. Vous devrez ensuite contacter votre fournisseur de connectivité pour qu’il mette à jour les configurations de son côté afin de refléter cette modification. Une fois cette modification apportée, nous commençons à vous facturer pour l’option de bande passante mise à jour.


### Pour modifier la référence de limité à illimité

Vous pouvez modifier la référence d'un circuit ExpressRoute à l'aide de l'extrait de code PowerShell suivant :

	$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	$ckt.Sku.Family = "UnlimitedData"
	$ckt.sku.Name = "Premium_UnlimitedData"

	Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Pour contrôler l'accès aux environnements classique et Resource Manager  

Consultez les instructions dans [Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-howto-move-arm.md).


## Annulation de l’approvisionnement et suppression d’un circuit ExpressRoute

Notez les points suivants :

- Vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

- Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est **En cours d’approvisionnement** ou **Approvisionné**, vous devez vous mettre en relation avec votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine le désapprovisionnement du circuit et nous en avertisse.

- Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur **Non approvisionné**), vous pouvez supprimer le circuit. Cette opération arrêtera la facturation du circuit

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

	Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"



## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0831_2016-->