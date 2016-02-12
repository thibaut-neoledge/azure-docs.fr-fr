<properties
   pageTitle="Configurer un circuit ExpressRoute à l’aide d’Azure Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article vous guide tout au long des étapes de création et d’approvisionnement d'un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement de votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/26/2016"
   ms.author="cherylmc"/>

# Créer et modifier un circuit ExpressRoute à l’aide d’Azure Resource Manager et de PowerShell

> [AZURE.SELECTOR]
[PowerShell - Classic](expressroute-howto-circuit-classic.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Cet article vous guide tout au long des étapes de création d'un circuit ExpressRoute à l'aide des applets de commande PowerShell et du modèle de déploiement Azure Resource Manager. Les étapes ci-dessous vous montreront également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement d'un circuit ExpressRoute.

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Conditions préalables à la configuration

- Vous devez utiliser la dernière version des modules Azure PowerShell version 1.0 ou ultérieure. Suivez les instructions de la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) pour savoir comment configurer votre ordinateur afin d’utiliser les modules Azure PowerShell. 
- Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md) et aux [workflows](expressroute-workflows.md) avant de commencer la configuration.

## Pour créer et approvisionner un circuit ExpressRoute

1. **Importez le module PowerShell pour ExpressRoute.**

 	Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

	    Install-Module AzureRM

		Install-AzureRM

	Importez tous les modules AzureRM.* dans la plage de version sémantique connue

		Import-AzureRM

	Vous pouvez également importer simplement un module sélectionné dans la plage de version sémantique connue
		
		Import-Module AzureRM.Network 

	Connectez-vous à votre compte

		Login-AzureRmAccount

	Sélectionnez l'abonnement pour lequel vous souhaitez créer le circuit ExpressRoute
		
		Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
			


2. **Récupérez la liste des fournisseurs, des emplacements et des bandes passantes pris en charge.**

	Avant de créer un circuit ExpressRoute, vous aurez besoin d’une liste des fournisseurs de services, des emplacements pris en charge et des options de bande passante. L'applet de commande PowerShell *Get-AzureRmExpressRouteServiceProvider* renvoie ces informations que vous utiliserez dans les étapes ultérieures.

		Get-AzureRmExpressRouteServiceProvider

	Vérifiez si votre fournisseur de connectivité y est référencé. Notez les éléments suivants, car vous en aurez besoin pour créer des circuits.
	
	- Nom
	- PeeringLocations
	- BandwidthsOffered

	Vous êtes maintenant prêt à créer un circuit ExpressRoute.

		
3. **Créez un circuit ExpressRoute.**

	Si vous ne l’avez pas encore fait, vous devez créer un groupe de ressources avant de créer votre circuit ExpressRoute. Pour cela, vous pouvez exécuter la commande suivante.

		New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"

	L'exemple ci-dessous montre comment créer un circuit ExpressRoute de 200 Mbps via Equinix dans Silicon Valley. Si vous utilisez un autre fournisseur et différents paramètres, remplacez ces informations lors de la création de votre requête.

	Voici un exemple de demande pour une nouvelle clé de service :

		New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200

	Assurez-vous que vous spécifiez le niveau de référence (SKU) et la famille de références corrects.
 
	 - Le niveau de référence détermine si ExpressRoute standard ou le module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier *standard* pour obtenir la référence standard ou *premium* pour le module complémentaire premium
	 - La famille de références détermine le type de facturation. Vous pouvez sélectionner *metereddata* pour définir un plan de données limitées et *unlimiteddata” pour un plan de données illimitées. **Remarque :** vous ne serez pas en mesure de modifier le type de facturation après la création d'un circuit.

	La réponse contiendra la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

		Get-Help New-AzureRmExpressRouteCircuit -detailed 

4. **Répertoriez tous les circuits ExpressRoute.**

	Vous pouvez exécuter la commande *Get-AzureRmExpressRouteCircuit* pour obtenir une liste de tous les circuits ExpressRoute que vous avez créés.

		
		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La réponse ressemblera à l'exemple ci-dessous :

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


	Vous pouvez récupérer ces informations à tout moment à l'aide de l'applet de commande *Get-AzureRmExpressRouteCircuit*. L'appel sans paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey*.

		Get-AzureRmExpressRouteCircuit

	La réponse ressemblera à l'exemple ci-dessous :

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

		Get-Help Get-AzureRmExpressRouteCircuit -detailed 

5. **Envoyez la clé de service à votre fournisseur de connectivité pour l’approvisionnement.**

	Lorsque vous créez un circuit ExpressRoute, ce circuit affichera l’état suivant :
	
		ServiceProviderProvisioningState : NotProvisioned
		
		CircuitProvisioningState         : Enabled

	*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service et Status indique l’état du côté de Microsoft. Un circuit ExpressRoute doit être dans l’état suivant pour pouvoir être utilisé.

		ServiceProviderProvisioningState : Provisioned
		
		CircuitProvisioningState         : Enabled

	Le circuit passera à l'état suivant lorsque le fournisseur de connectivité est sur le point de l’activer pour vous.

		ServiceProviderProvisioningState : Provisioned
		
		Status                           : Enabled



6. **Vérifiez régulièrement le statut et l'état de la clé du circuit.**

	Cela vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* s’affiche avec l’état *Provisioned* comme indiqué dans l’exemple ci-dessous.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

	La réponse ressemblera à l'exemple ci-dessous :

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

7. **Configurer le routage et lier un réseau virtuel**

	a. **Créez votre configuration de routage.** Pour des instructions pas à pas, reportez-vous à [Créer et modifier le routage pour un circuit ExpressRoute](expressroute-howto-routing-arm.md). Notez que ces instructions de routage s’appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement IPVPN, à l’image de MPLS), votre fournisseur de connectivité configure et gère le routage pour vous. Vous ne pourrez pas créer ou gérer des homologations dans ce cas.
	
	b. **Liaison de votre réseau virtuel à un circuit ExpressRoute.** Après avoir vérifié que le routage a été configuré, vous devez lier votre réseau virtuel à votre circuit ExpressRoute. Pour obtenir des instructions pas à pas, voir [Liaison de réseaux virtuels à des circuits ExpressRoute](expressroute-howto-linkvnet-arm.md).

##  Pour obtenir l'état d'un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande *Get-AzureRmExpressRouteCircuit*. L'appel sans paramètre répertorie tous les circuits.

		Get-AzureRmExpressRouteCircuit

La réponse sera similaire à l’exemple ci-dessous :

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

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en transmettant le nom du groupe de ressources et du circuit comme paramètre à l'appel.

		Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"


La réponse ressemblera à l'exemple ci-dessous :

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

		Get-Help Get-azurededicatedcircuit -detailed 

## Pour modifier un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité. Reportez-vous à la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour plus d'informations sur les limites et les limitations.

Vous pouvez modifier les paramètres suivants sans entraîner de temps d'arrêt :

- Activer ou désactiver le module complémentaire ExpressRoute premium pour votre circuit ExpressRoute sans aucune interruption de service.
- Augmenter la bande passante de votre circuit ExpressRoute sans aucune interruption de service.



### Activation du module complémentaire ExpressRoute premium

Vous pouvez activer le module complémentaire ExpressRoute premium pour votre circuit existant à l'aide de l'extrait PowerShell suivant :

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.Sku.Tier = "Premium"
		$ckt.sku.Name = "Premium_MeteredData"

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
	
		
Les fonctionnalités du module complémentaire ExpressRoute premium seront activées sur votre circuit. Notez que nous commencerons à vous facturer la fonctionnalité du module complémentaire premium dès que la commande aura été exécutée avec succès.

### Désactivation du module complémentaire ExpressRoute premium

Vous pouvez désactiver le module complémentaire ExpressRoute premium pour votre circuit existant. Lorsque vous désactivez le module complémentaire ExpressRoute premium, notez les points suivants :

- Vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10 avant de rétrograder du niveau premium à standard. Si vous ne le faites, votre demande de mise à jour échouera et le tarif premium vous sera facturé.
- Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites, votre demande de mise à jour échouera et le tarif premium vous sera facturé.
- Votre table de routage doit être inférieure à 4 000 routages pour l'homologation privée. Si la taille de la table de routage est supérieure à 4 000 routages, la session BGP s’arrêtera et ne sera pas réactivée tant que le nombre de préfixes publiés ne sera pas inférieur à 4 000.

Pour désactiver le module complémentaire premium, utilisez l'exemple d'applet de commande PowerShell ci-dessous. Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui autorisé pour le circuit standard.
	
		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
		
		$ckt.Sku.Tier = "Standard"
		$ckt.sku.Name = "Standard_MeteredData"
		
		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

### Mise à jour de la bande passante d’un circuit ExpressRoute

Consultez le [Forum Aux Questions ExpressRoute](expressroute-faqs.md) pour connaître les options de bande passante prises en charge par votre fournisseur. Vous pouvez choisir n’importe quelle taille **supérieure** à la taille de votre circuit existant sans entraîner de temps d’arrêt.

>[AZURE.IMPORTANT] Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. La rétrogradation de la bande passante vous oblige à annuler le déploiement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.

Une fois que vous sélectionné la taille dont vous avez besoin, vous pouvez utiliser l’exemple ci-dessous pour redimensionner votre circuit. Une fois les applets de commande exécutées, votre circuit sera redimensionné du côté de Microsoft. Vous devez contacter votre fournisseur de connectivité pour mettre à jour les configurations de son côté afin de refléter cette modification. Notez que nous allons commencer à vous facturer la bande bande passante mise à jour à partir de cet instant.

		$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

		$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

		Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

## Pour supprimer et annuler l’approvisionnement d’un circuit ExpressRoute

Vous pouvez supprimer votre circuit ExpressRoute. Lorsque vous supprimez un circuit ExpressRoute, notez les points suivants :

- Vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute pour que cette opération réussisse. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

- Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est activé, le système passe de l’état d’activation à l’état *disabling* (désactivation). Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu'à ce que le fournisseur de services termine l'annulation de l’approvisionnement et nous envoie une notification.

- Si le fournisseur de services a annulé l’approvisionnement du circuit avant que vous n’exécutiez l’applet de commande (l’état d’approvisionnement du fournisseur de services affichant la valeur *not provisioned* (non approvisionné)), nous annulerons l’approvisionnement du circuit et cesserons la facturation.

Pour supprimer votre circuit ExpressRoute, utilisez l'exemple d'applet de commande PowerShell ci-dessous.

		Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"

## Étapes suivantes

Après avoir créé votre circuit, veillez à procéder comme suit :

1.  [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
2.  [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0204_2016-->