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
   ms.date="12/04/2015"
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
New-AzureRmResourceGroup -Name “ExpressRouteResourceGroup” -Location "West US"
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

**Step 7.  Create your routing configuration.**

For step-by-step instructions, refer to the [ExpressRoute circuit routing configuration (create and modify circuit peerings)](expressroute-howto-routing-arm.md).

**Step 8.  Link a virtual network to an ExpressRoute circuit.**

Next, link a virtual network to your ExpressRoute circuit. You can use [this template](https://github.com/Azure/azure-quickstart-templates/tree/ecad62c231848ace2fbdc36cbe3dc04a96edd58c/301-expressroute-circuit-vnet-connection) when you work with the Resource Manager deployment mode. We're currently working on steps to accomplish this by using PowerShell.

## Get the status of an ExpressRoute circuit

You can retrieve this information at any time by using the *Get-AzureRmExpressRouteCircuit* cmdlet. Making the call with no parameters will list all circuits.

```
Get-AzureRmExpressRouteCircuit
```

The response will be similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get information on a specific ExpressRoute circuit by passing the resource group name and circuit name as a parameter to the call:

```
Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

The response will look similar to the following example:

```
Name : ExpressRouteARMCircuit ResourceGroupName : ExpressRouteResourceGroup Location : westus Id : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit Etag : W/"################################" ProvisioningState : Succeeded Sku : { "Name": "Standard\_MeteredData", "Tier": "Standard", "Family": "MeteredData" } CircuitProvisioningState : Enabled ServiceProviderProvisioningState : Provisioned ServiceProviderNotes : ServiceProviderProperties : { "ServiceProviderName": "Equinix", "PeeringLocation": "Silicon Valley", "BandwidthInMbps": 200 } ServiceKey : ************************************** Peerings :
```

You can get detailed descriptions of all parameters by running the following:

```
get-help get-azurededicatedcircuit -detailed
```

## Modify an ExpressRoute circuit

You can modify certain properties of an ExpressRoute circuit without impacting connectivity.

You can do the following, with no downtime:

- Enable or disable an ExpressRoute premium add-on for your ExpressRoute circuit.
- Increase the bandwidth of your ExpressRoute circuit.

For more information on limits and limitations, refer to the [ExpressRoute FAQ](expressroute-faqs.md) page.

### Enable the ExpressRoute premium add-on

You can enable the ExpressRoute premium add-on for your existing circuit by using the following PowerShell snippet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Name = "Premium" $ckt.sku.Name = "Premium\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The circuit will now have the ExpressRoute premium add-on features enabled. Note that Microsoft will begin billing you for the premium add-on capability as soon as the command has successfully run.

### Disable the ExpressRoute premium add-on

You can disable the ExpressRoute premium add-on for the existing circuit by using the following PowerShell cmdlet:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard" $ckt.sku.Name = "Standard\_MeteredData"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

The premium add-on is now disabled for the circuit.

Note that this operation can fail if you are using resources greater than what is permitted for the standard circuit.

- Before you downgrade from premium to standard, you must ensure that the number of virtual networks linked to the circuit is less than 10. If you don't do so, your update request fails and Microsoft will bill you at premium rates.
- You must unlink all virtual networks in other geopolitical regions. If you don't do so, your update request will fail and Microsoft will bill you at premium rates.
- Your route table must be less than 4,000 routes for private peering. If your route table size is greater than 4,000 routes, the BGP session drops and won't be reenabled until the number of advertised prefixes goes below 4,000.

### Update the ExpressRoute circuit bandwidth

For supported bandwidth options for your provider, check the [ExpressRoute FAQ](expressroute-faqs.md) page. You can pick any size greater than the size of your existing circuit. After you decide what size you need, use the following command to resize your circuit:

```
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Your circuit will be sized up on the Microsoft side. Then you must contact your connectivity provider to update configurations on their side to match this change. After you make this notification, Microsoft will begin billing you for the updated bandwidth option.

**Important**: You cannot reduce the bandwidth of an ExpressRoute circuit without disruption. Downgrading bandwidth requires you to deprovision the ExpressRoute circuit and then reprovision a new ExpressRoute circuit.

## Delete and deprovision an ExpressRoute circuit

You can delete your ExpressRoute circuit by running the following command:

```
Remove-AzureRmExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit" ```

Pour que cette opération réussisse, vous devez dissocier tous les réseaux virtuels du circuit ExpressRoute. Si cette opération échoue, vérifiez si certains de vos réseaux virtuels sont liés au circuit.

Si l’état d’approvisionnement du fournisseur de services du circuit ExpressRoute est activé, l’état passe d’un état activé à l’état *disabling* (désactivation). Vous devez contacter votre fournisseur de services pour annuler l’approvisionnement du circuit de son côté. Microsoft continuera à réserver des ressources et à vous facturer jusqu’à ce que le fournisseur de services termine l’annulation de l’approvisionnement et nous avertisse.

Si le fournisseur de services a annulé l’approvisionnement du circuit (l’état d’approvisionnement du fournisseur de services affiche la valeur *not provisioned* (non approvisionné)) avant que vous n’exécutiez l’applet de commande ci-dessus, Microsoft annulera l’approvisionnement du circuit et cessera la facturation.

## Étapes suivantes

Après avoir créé votre circuit, effectuez les opérations suivantes :

- [Créer et modifier le routage le routage pour votre circuit ExpressRoute](expressroute-howto-routing-arm.md)
- [Lier votre réseau virtuel à votre circuit ExpressRoute](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0302_2016-->