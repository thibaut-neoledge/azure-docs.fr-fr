<properties
   pageTitle="Créer et modifier un circuit ExpressRoute à l’aide du modèle de déploiement classique et de PowerShell | Microsoft Azure"
   description="Cet article vous guide tout au long des étapes de création et d’approvisionnement d'un circuit ExpressRoute. Cet article vous montre également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement de votre circuit."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="ganesr"/>

# Création et modification d’un circuit ExpressRoute

> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-circuit-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)
[PowerShell - Classic](expressroute-howto-circuit-classic.md)

Cet article vous guide dans les étapes de création d’un circuit Azure ExpressRoute à l’aide d’applets de commande PowerShell et du modèle de déploiement classique. Cet article vous montrera également comment vérifier l'état, mettre à jour ou supprimer et annuler l’approvisionnement d'un circuit ExpressRoute.

**À propos des modèles de déploiement Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Avant de commencer

- Vous devez utiliser la dernière version des modules Azure PowerShell. Vous pouvez télécharger les modules PowerShell les plus récents à partir de la section PowerShell de la [page des téléchargements Azure](https://azure.microsoft.com/downloads/). Suivez les instructions de [Comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour des étapes pas à pas permettant de configurer votre ordinateur pour l’utilisation des modules Azure PowerShell.

- Veillez à consulter les [conditions préalables](expressroute-prerequisites.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.

## Création et approvisionnement d’un circuit ExpressRoute

### 1\. Importer les modules PowerShell pour ExpressRoute

 Vous devez importer les modules Azure et ExpressRoute dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Pour cela, exécutez les commandes suivantes :

	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

### 2\. Récupérer la liste des fournisseurs, des emplacements et des bandes passantes pris en charge

Avant de créer un circuit ExpressRoute, vous avez besoin d’une liste des fournisseurs de services, des emplacements et des options de bande passante pris en charge.

L’applet de commande PowerShell `Get-AzureDedicatedCircuitServiceProvider` retourne ces informations que vous utilisez dans les étapes ultérieures :

	Get-AzureDedicatedCircuitServiceProvider

Vérifiez si votre fournisseur de connectivité y est référencé. Prenez note des éléments suivants, car vous en avez besoin plus tard lors de la création d’un circuit :

- Nom

- PeeringLocations

- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.

### 3\. Création d’un circuit ExpressRoute

L’exemple suivant montre comment créer un circuit ExpressRoute de 200 Mb/s par le biais d’Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et des paramètres différents, utilisez ces informations quand vous créez votre requête.

>[AZURE.IMPORTANT] Votre circuit ExpressRoute sera facturé à partir de l’émission d'une clé de service. Effectuez cette opération seulement quand le fournisseur de connectivité prêt à approvisionner le circuit.


Voici un exemple de demande pour une nouvelle clé de service :

	$Bandwidth = 200
	$CircuitName = "MyTestCircuit"
	$ServiceProvider = "Equinix"
	$Location = "Silicon Valley"

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Standard -BillingType MeteredData

Si vous voulez créer un circuit ExpressRoute avec le module complémentaire Premium, utilisez l’exemple suivant. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ ExpressRoute](expressroute-faqs.md).

	New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -sku Premium - BillingType MeteredData


La réponse contiendra la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

	get-help new-azurededicatedcircuit -detailed

### 4\. Répertorier tous les circuits ExpressRoute

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

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande `Get-AzureDedicatedCircuit`. Un appel effectué sans aucun paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey*.

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

### 5\. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement


*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service. Le *statut* indique l’état du côté Microsoft. Pour plus d’informations sur les états d’approvisionnement des circuits, consultez l’article [Flux de travail](expressroute-workflows.md#expressroute-circuit-provisioning-states).

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous :

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled

Un circuit ExpressRoute doit être dans l’état suivant pour pouvoir être utilisé.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled


### 6\. Vérifier régulièrement le statut et l’état de la clé du circuit

Cela vous permet de savoir quand votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* est *Approvisionné*, comme le montre l’exemple suivant :

	Get-AzureDedicatedCircuit

	Bandwidth                        : 200
	CircuitName                      : MyTestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled

### 7\. Créer votre configuration de routage

Pour obtenir des instructions pas à pas, consultez l’article [Configuration du routage des circuits ExpressRoute (créer et modifier des homologations de circuit)](expressroute-howto-routing-classic.md).

>[AZURE.IMPORTANT] Ces instructions s’appliquent seulement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement un VPN IP, comme MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

### 8\. Lier un réseau virtuel à un circuit ExpressRoute

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Pour obtenir des instructions pas à pas, voir [Liaison de circuits ExpressRoute à des réseaux virtuels](expressroute-howto-linkvnet-classic.md). Si vous avez besoin de créer un réseau virtuel à l’aide du modèle de déploiement classique pour ExpressRoute, consultez [Créer un réseau virtuel pour ExpressRoute](expressroute-howto-vnet-portal-classic.md) pour obtenir des instructions.

## Récupération de l’état d’un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande `Get-AzureCircuit`. Un appel effectué sans aucun paramètre répertorie tous les circuits.

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

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en passant la clé de service comme paramètre à l’appel :

	Get-AzureDedicatedCircuit -ServiceKey "*********************************"

	Bandwidth                        : 200
	CircuitName                      : MyTestCircuit
	Location                         : Silicon Valley
	ServiceKey                       : *********************************
	ServiceProviderName              : equinix
	ServiceProviderProvisioningState : Provisioned
	Sku                              : Standard
	Status                           : Enabled


Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

	get-help get-azurededicatedcircuit -detailed

## Modification d’un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

- Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute. Notez que la rétrogradation de la bande passante d'un circuit n'est pas prise en charge.
- Modifiez le plan de mesure de Données limitées à Données illimitées. Notez que la modification du plan de limitation de Données illimitées à Données limitées n’est pas prise en charge.
- Vous pouvez activer et désactiver *Autoriser les opérations classiques*.

Pour plus d’informations sur les limites et les limitations, reportez-vous au [FAQ ExpressRoute](expressroute-faqs.md).

### Pour activer le module complémentaire ExpressRoute Premium

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

### Pour désactiver le module complémentaire ExpressRoute Premium

>[AZURE.IMPORTANT] Cette opération peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.

Notez les points suivants :

- Vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10 avant de rétrograder du niveau premium à standard. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.

- Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites pas, votre demande de mise à jour échoue et nous appliquons les tarifs Premium.

- Pour l’homologation privée, votre table de routage doit comporter moins de 4 000 routages. Si la table de routage comporte plus de 4 000 routages, la session BGP s’arrête et n’est pas réactivée tant que le nombre de préfixes publiés n’est pas inférieur à 4 000.

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



### Pour mettre à jour la bande passante d’un circuit ExpressRoute

Pour connaître les options de bande passante prises en charge par votre fournisseur, consultez le [FAQ ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n’importe quelle taille supérieure à la taille de votre circuit existant.

>[AZURE.IMPORTANT] Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. La rétrogradation de la bande passante vous oblige à désapprovisionner le circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.

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

## Suppression et annulation du provisionnement d'un circuit ExpressRoute

Notez les points suivants :

- Vous devez annuler la liaison de tous les réseaux virtuels du circuit ExpressRoute pour que cette opération réussisse. Si cette opération échoue, vérifiez si des réseaux virtuels sont liés au circuit.

- Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est activé, le statut passe de l’état Activé à l’état *Désactivation*. Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Nous continuerons à réserver des ressources et à vous facturer jusqu'à ce que le fournisseur de services termine l'annulation de l’approvisionnement et nous envoie une notification.

- Si le fournisseur de services a désapprovisionné le circuit (l’état d’approvisionnement du fournisseur de services est *Non approvisionné*) avant que vous n’exécutiez l’applet de commande ci-dessus, nous désapprovisionnons le circuit et nous cessons de vous facturer.

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

	Remove-AzureDedicatedCircuit -ServiceKey "*********************************"



## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-classic.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0720_2016-->