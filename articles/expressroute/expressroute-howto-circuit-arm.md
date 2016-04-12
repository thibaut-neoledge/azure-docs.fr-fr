<properties
   pageTitle="Créer et modifier un circuit ExpressRoute à l’aide de Resource Manager et de PowerShell | Microsoft Azure"
   description="Cet article explique comment créer et approvisionner un circuit ExpressRoute. Il montre également comment vérifier le circuit, le mettre à jour, le supprimer et annuler son approvisionnement."
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
   ms.date="03/03/2016"
   ms.author="cherylmc"/>

# Créer et modifier un circuit ExpressRoute à l’aide de Resource Manager et de PowerShell

   > [AZURE.SELECTOR]
   [PowerShell - Classic](expressroute-howto-circuit-classic.md)
   [PowerShell - Resource Manager](expressroute-howto-circuit-arm.md)

Cet article explique comment créer un circuit ExpressRoute à l’aide des applets de commande Windows PowerShell et du modèle de déploiement Azure Resource Manager. Les étapes suivantes vous montreront également comment vérifier l’état du circuit, mettre à jour celui-ci, le supprimer et annuler son approvisionnement.

   [AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Conditions préalables à la configuration

Pour créer un circuit ExpressRoute, vous devez effectuer ce qui suit :

- Obtenir la dernière version des modules Azure PowerShell version 1.0 ou ultérieure. Pour connaître les étapes de la configuration de votre ordinateur afin d’utiliser les modules Azure PowerShell, suivez les instructions de la page [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md).
- Consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md) et aux [workflows](expressroute-workflows.md) avant de commencer la configuration.

## Création et approvisionnement d’un circuit ExpressRoute

**Étape 1 : Importer le module PowerShell pour ExpressRoute**

Pour commencer à utiliser les applets de commande ExpressRoute, vous devez installer la dernière version du programme d’installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Resource Manager dans la session PowerShell. Vous exécuterez PowerShell en tant qu’administrateur.

```
Install-Module AzureRM

Install-AzureRM
```

Importez tous les modules AzureRM dans la plage de version sémantique connue :

```
Import-AzureRM
```

Vous pouvez également importer un module sélectionné dans la plage de version sémantique connue :

```
Import-Module AzureRM.Network
```

Connectez-vous à votre compte :

```
Login-AzureRmAccount
```

Sélectionnez l’abonnement pour lequel vous souhaitez créer un circuit ExpressRoute :

```
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"   			
```

**Étape 2 : Récupérer la liste des fournisseurs, des emplacements et des bandes passantes pris en charge**

Avant de créer un circuit ExpressRoute, vous avez besoin d’une liste des fournisseurs de services, des emplacements pris en charge et des options de bande passante. L’applet de commande PowerShell *Get-AzureRmExpressRouteServiceProvider* renvoie ces informations que vous utiliserez dans les étapes ultérieures.

```
PS C:\> Get-AzureRmExpressRouteServiceProvider
```

Vérifiez si votre fournisseur de connectivité y est référencé. Prenez note des éléments suivants, car vous en aurez besoin au moment de créer un circuit :

- Nom
- PeeringLocations
- BandwidthsOffered

Vous êtes maintenant prêt à créer un circuit ExpressRoute.

**Étape 3. Créer un circuit ExpressRoute**

Si vous n’avez pas déjà un groupe de ressources, vous devez en créer un avant de créer votre circuit ExpressRoute. Pour cela, vous pouvez exécuter la commande suivante :

```
New-AzureRmResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

L’exemple suivant montre comment créer un circuit ExpressRoute de 200 Mb/s par le biais d’Equinix dans la Silicon Valley. Si vous utilisez un autre fournisseur et différents paramètres, remplacez ces informations quand vous créez votre requête. Voici un exemple de demande pour une nouvelle clé de service :

```
New-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Assurez-vous que vous spécifiez le niveau de référence (SKU) et la famille de références corrects :

- Le niveau de référence détermine si ExpressRoute standard ou un module complémentaire ExpressRoute Premium est activé. Vous pouvez spécifier *standard* pour obtenir la référence standard ou *premium* pour le module complémentaire premium.
- La famille de références détermine le type de facturation. Vous pouvez sélectionner *metereddata* pour définir un plan de données limitées et *unlimiteddata* pour un plan de données illimitées. **Remarque :** Après avoir créé un circuit, vous ne pouvez pas modifier le type de facturation.

La réponse contient la clé de service. Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```
get-help New-AzureRmExpressRouteCircuit -detailed
```

**Étape 4. Répertorier tous les circuits ExpressRoute**

Pour obtenir la liste de tous les circuits ExpressRoute que vous avez créés, exécutez la commande *Get-AzureRmExpressRouteCircuit*.

```
#Getting service key
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La réponse ressemblera à l’exemple suivant :

```
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
```

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande *Get-AzureRmExpressRouteCircuit*. L’appel sans paramètre répertorie tous les circuits. Votre clé de service apparaît dans le champ *ServiceKey*.

```
Get-AzureRmExpressRouteCircuit
```

La réponse ressemblera à l’exemple suivant :

```
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
```

Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```
get-help Get-AzureRmExpressRouteCircuit -detailed
```

**Étape 5. Envoyer la clé de service à votre fournisseur de connectivité pour l’approvisionnement**

Quand vous créez un circuit ExpressRoute, ce circuit affiche l’état suivant :

```
ServiceProviderProvisioningState : NotProvisioned

CircuitProvisioningState         : Enabled
```

*ServiceProviderProvisioningState* fournit des informations sur l’état actuel de l’approvisionnement du côté du fournisseur de service et Status indique l’état du côté de Microsoft. Pour pouvoir être utilisé, un circuit ExpressRoute doit être dans l’état suivant :

```
ServiceProviderProvisioningState : Provisioned

CircuitProvisioningState         : Enabled
```

Le circuit passe à l’état suivant quand le fournisseur de connectivité est sur le point de l’activer pour vous.

```
ServiceProviderProvisioningState : Provisioned

Status                           : Enabled
```

**Étape 6. Vérifier régulièrement le statut et l’état de la clé du circuit**

Vérifiez le statut et l’état de la clé du circuit pour déterminer si votre fournisseur a activé votre circuit. Une fois le circuit configuré, *ServiceProviderProvisioningState* a pour valeur *Provisioned* comme indiqué dans l’exemple ci-dessous :

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La réponse ressemblera à l’exemple suivant :

```
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

```

**Étape 7. Créer votre configuration de routage**

Pour obtenir des instructions pas à pas, reportez-vous à la [configuration du routage du circuit ExpressRoute (créer et modifier des homologations de circuit)](expressroute-howto-routing-arm.md).

**Étape 8. Lier un réseau virtuel à un circuit ExpressRoute**

Maintenant, vous devez lier un réseau virtuel à votre circuit ExpressRoute. Vous pouvez utiliser [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) avec le mode de déploiement de Resource Manager. Nous travaillons actuellement à rendre cela possible à l’aide de PowerShell.

## Obtenir l’état d’un circuit ExpressRoute

Vous pouvez récupérer ces informations à tout moment à l’aide de l’applet de commande *Get-AzureRmExpressRouteCircuit*. L’appel sans paramètre répertorie tous les circuits.

```
Get-AzureRmExpressRouteCircuit
```

La réponse ressemblera à ce qui suit :

```
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
```

Vous pouvez obtenir des informations sur un circuit ExpressRoute spécifique en transmettant à l’appel le nom du groupe de ressources et le nom du circuit comme paramètres :

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

La réponse ressemblera à l’exemple suivant :

```
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
```

Vous pouvez obtenir une description détaillée de tous les paramètres en exécutant la commande suivante :

```
get-help get-azurededicatedcircuit -detailed
```

## Modifier un circuit ExpressRoute

Vous pouvez modifier certaines propriétés d'un circuit ExpressRoute sans affecter la connectivité.

Vous pouvez effectuer les opérations suivantes sans entraîner d’interruption de service :

- Activer ou désactiver le module complémentaire ExpressRoute Premium pour votre circuit ExpressRoute.
- Augmenter la bande passante de votre circuit ExpressRoute.

Pour plus d’informations sur les limites et les limitations, reportez-vous à la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md).

### Activer le module complémentaire ExpressRoute Premium

Vous pouvez activer le module complémentaire ExpressRoute Premium pour votre circuit existant à l’aide de l’extrait de code PowerShell suivant :

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Les fonctionnalités du module complémentaire ExpressRoute Premium sont ainsi activées pour votre circuit. Notez que Microsoft commence à facturer la fonctionnalité du module complémentaire Premium dès que la commande a été exécutée avec succès.

### Désactiver le module complémentaire ExpressRoute Premium

Vous pouvez désactiver le module complémentaire ExpressRoute Premium dans votre circuit existant à l’aide de l’applet de commande PowerShell suivante :

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Le module complémentaire Premium est maintenant désactivé pour votre circuit.

Notez que cette opération peut échouer si vous utilisez des ressources supérieures à ce qui est autorisé pour le circuit standard.

- Avant de passer du niveau Premium au niveau Standard, vous devez vous assurer que le nombre de réseaux virtuels liés au circuit est inférieur à 10. Si vous ne le faites pas, votre demande de mise à jour échoue et Microsoft applique les tarifs Premium.
- Vous devez dissocier tous les réseaux virtuels dans d'autres régions géopolitiques. Si vous ne le faites pas, votre demande de mise à jour échoue et Microsoft applique les tarifs Premium.
- Pour l’homologation privée, votre table de routage doit comporter moins de 4 000 routages. Si la table de routage comporte plus de 4 000 routages, la session BGP s’arrête et ne sera pas réactivée tant que le nombre de préfixes publiés ne sera pas inférieur à 4 000.

### Mettre à jour la bande passante d’un circuit ExpressRoute

Pour connaître les options de bande passante prises en charge par votre fournisseur, consultez la page [Forum Aux Questions ExpressRoute](expressroute-faqs.md). Vous pouvez choisir n'importe quelle taille supérieure à la taille de votre circuit existant. Une fois que vous avez décidé de la taille dont vous avez besoin, utilisez la commande suivante pour redimensionner votre circuit :

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Microsoft se charge de redimensionner votre circuit. Vous devrez ensuite contacter votre fournisseur de connectivité pour qu’il mette à jour les configurations de son côté afin de refléter cette modification. Une fois cette modification apportée, Microsoft commence à facturer l’option de bande passante que vous avez mise à jour.

**Important** : Vous ne pouvez pas réduire la bande passante d’un circuit ExpressRoute sans interrompre le service. Le fait de passer à un niveau inférieur de bande passante vous oblige à annuler l’approvisionnement du circuit ExpressRoute, puis à réapprovisionner un nouveau circuit ExpressRoute.

## Supprimer et annuler l’approvisionnement d’un circuit ExpressRoute

Vous pouvez supprimer votre circuit ExpressRoute en exécutant la commande suivante :

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

Pour que cette opération réussisse, vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est activé, l’approvisionnement passe de l’état enabled (activé) à l’état *disabling* (désactivation). Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Microsoft continuera à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine l’annulation de l’approvisionnement et nous avertisse.

Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur *not provisioned* (non approvisionné)) avant que vous n’exécutiez l’applet de commande ci-dessus, Microsoft annule l’approvisionnement du circuit et cesse la facturation.

## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!-----------HONumber=AcomDC_0309_2016-->