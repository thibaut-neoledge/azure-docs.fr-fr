<properties
   pageTitle="Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager | Microsoft Azure"
   description="Cette page décrit comment déplacer un circuit classique vers le modèle de déploiement Resource Manager."
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
   ms.date="04/01/2016"
   ms.author="ganesr"/>


# Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager

## Conditions préalables à la configuration

- Vous devez utiliser la dernière version des modules Azure PowerShell (au moins la version 1.0).
- Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
- Avant de poursuivre, examinez les informations fournies sous [Transfert des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md). Assurez-vous que vous avez bien compris les limites et limitations des possibilités.
- Si vous souhaitez déplacer un circuit Azure ExpressRoute à partir d’un modèle de déploiement classique vers un modèle de déploiement Azure Resource Manager, le circuit doit être entièrement configuré et opérationnel dans le modèle de déploiement classique.
- Assurez-vous que vous disposez d’un groupe de ressources créé dans le modèle de déploiement Resource Manager.

## Transférer le circuit ExpressRoute vers le modèle de déploiement Resource Manager

Vous devez déplacer un circuit ExpressRoute vers le modèle de déploiement Resource Manager pour pouvoir l’utiliser à la fois dans des modèles de déploiement classiques et Resource Manager. Vous pouvez le faire en exécutant les commandes PowerShell suivantes.

### Étape 1 : Collecter des informations sur le circuit à partir du modèle de déploiement classique

Vous devez d’abord collecter des informations sur votre circuit ExpressRoute.

Connectez-vous à l’environnement classique Azure et collectez la clé de service. Vous pouvez utiliser l’extrait de code PowerShell suivant pour recueillir les informations :

	# Sign in to your Azure account
	Add-AzureAccount

	# Select the appropriate Azure subscription
	Select-AzureSubscription "<Enter Subscription Name here>"

	# Import the PowerShell modules for Azure and ExpressRoute
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

	# Get the service keys of all your ExpressRoute circuits
	Get-AzureDedicatedCircuit

Copiez la **clé de service** du circuit que vous souhaitez transférer vers le modèle de déploiement Resource Manager.

### Étape 2 : Se connecter à l’environnement Resource Manager et créer un groupe de ressources

Vous pouvez créer un groupe de ressources en utilisant l’extrait de code suivant :

	# Sign in to your Azure Resource Manager environment
	Login-AzureRmAccount

	# Select the appropriate Azure subscription
	Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

	#Create a new resource group if you don't already have one
	New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

Vous pouvez également utiliser un groupe de ressources existant si vous en avez un.

### Étape 3 : Transférer le circuit ExpressRoute vers le modèle de déploiement Resource Manager

Vous êtes maintenant prêt à transférer votre circuit ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager. Passez en revue les informations fournies sous [Transférer des circuits ExpressRoute du modèle de déploiement classique vers le modèle de déploiement Resource Manager](expressroute-move.md) avant de continuer.

Pour ce faire, exécutez l’extrait de code suivant :

	Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Une fois le déplacement terminé, le nouveau nom répertorié dans l’applet de commande précédente sera utilisé pour traiter la ressource. Le circuit sera essentiellement renommé.

## Activer un circuit ExpressRoute pour les deux modèles de déploiement

Vous devez déplacer votre circuit ExpressRoute vers le modèle de déploiement Resource Manager avant de contrôler l’accès au modèle de déploiement.

Exécutez l’applet de commande suivante pour activer l’accès aux deux modèles de déploiement :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous serez en mesure d’afficher le circuit dans le modèle de déploiement classique.

Exécutez la commande suivante pour obtenir les détails concernant le circuit ExpressRoute :

    get-azurededicatedcircuit

Vous devez être en mesure de voir la clé de service répertoriée. Vous pouvez désormais gérer les liens vers le circuit ExpressRoute. Les articles suivants vont vous guider tout au long de la gestion des liens vers le circuit ExpressRoute :

- [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement Resource Manager](expressroute-howto-linkvnet-arm.md)
- [Liaison de réseaux virtuels à des circuits ExpressRoute dans le modèle de déploiement classique](expressroute-howto-linkvnet-classic.md)


## Désactiver le circuit ExpressRoute créés dans le modèle de déploiement classique

Exécutez l’applet de commande suivante pour désactiver l’accès au modèle de déploiement classique :

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Une fois cette opération terminée avec succès, vous ne serez pas en mesure d’afficher le circuit dans le modèle de déploiement classique.

## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0420_2016-->