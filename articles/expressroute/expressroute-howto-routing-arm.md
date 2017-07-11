---
title: "Configuration de l’acheminement (homologation) d’un circuit ExpressRoute - Resource Manager, PowerShell et Azure | Microsoft Docs"
description: "Cet article vous guide tout au long des étapes de création et d’approvisionnement de l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l&quot;état, mettre à jour ou supprimer des homologations pour votre circuit."
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 0a036d51-77ae-4fee-9ddb-35f040fbdcdf
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: ganesr;cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: edffa76f7ee37c7d8ca7c8c35c0a286342437b0b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/08/2017


---
<a id="create-and-modify-peering-for-an-expressroute-circuit-using-powershell" class="xliff"></a>

# Créer et modifier l’homologation d’un circuit ExpressRoute à l’aide de PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [Classic - PowerShell](expressroute-howto-routing-classic.md)
> * [Vidéo - Homologation privée](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

Cet article vous guide tout au long des étapes de création et de gestion d'une configuration de routage d'un circuit ExpressRoute à l'aide de PowerShell et du modèle de déploiement Azure Resource Manager.  Les étapes ci-dessous vous montrent également comment vérifier l’état, mettre à jour ou supprimer et annuler l’approvisionnement des homologations d’un circuit ExpressRoute. 

<a id="configuration-prerequisites" class="xliff"></a>

## Conditions préalables à la configuration

* Installez la dernière version des applets de commande PowerShell Azure Resource Manager. Pour plus d’informations, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview). 
* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être dans un état approvisionné et activé pour être en mesure d'exécuter les applets de commande décrites ci-dessous.

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement IPVPN, à l’image de MPLS), votre fournisseur de connectivité configure et gère le routage pour vous.

> [!IMPORTANT]
> Nous n'annonçons pas d’homologations configurées par les fournisseurs de services via le portail de gestion des services. Nous travaillons sur la prochaine activation de cette fonctionnalité. Contactez votre fournisseur de services avant de configurer des homologations BGP.
> 
> 

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. 

<a id="azure-private-peering" class="xliff"></a>

## Homologation privée Azure

Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d'homologation privée Azure pour un circuit ExpressRoute. 

<a id="to-create-azure-private-peering" class="xliff"></a>

### Pour créer une homologation privée Azure

1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM
  Install-AzureRM
  ```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network 
  ```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité.

  Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous. 
3. Vérifiez que le circuit ExpressRoute est approvisionné.

  Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé. Voir l’exemple suivant :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse sera similaire à l’exemple suivant :

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
4. Configurez l'homologation privée Azure pour le circuit.

  Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :

  * Un sous-réseau /30 pour le lien principal. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
  * Un sous-réseau /30 pour le lien secondaire. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation. Veillez à ne pas utiliser le numéro 65515.
  * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.

  Vous pouvez exécuter l’applet de commande suivante pour configurer l’homologation privée Azure pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Vous pouvez utiliser l'applet de commande suivante si vous choisissez d'utiliser un hachage MD5 :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200  -SharedKey "A1B2C3D4"
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
  > 
  >

<a id="to-view-azure-private-peering-details" class="xliff"></a>

### Pour afficher les détails d’une homologation privée Azure

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante :

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -Circuit $ckt
```

<a id="to-update-azure-private-peering-configuration" class="xliff"></a>

### Pour mettre à jour la configuration d'homologation privée Azure

Vous pouvez mettre à jour toute partie de la configuration à l'aide de l’applet de commande suivante. Dans l'exemple ci-dessous, l'ID VLAN du circuit est mis à jour de 100 à 500 :

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt -PeeringType AzurePrivatePeering -PeerASN 100 -PrimaryPeerAddressPrefix "10.0.0.0/30" -SecondaryPeerAddressPrefix "10.0.0.4/30" -VlanId 200

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

<a id="to-delete-azure-private-peering" class="xliff"></a>

### Pour supprimer une homologation privée Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante :

> [!WARNING]
> Vous devez vous assurer que tous les réseaux virtuels sont dissociés du circuit ExpressRoute avant d'exécuter cette applet de commande. 
> 
> 

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

<a id="azure-public-peering" class="xliff"></a>

## Homologation publique Azure

Cette section fournit des instructions sur la façon de créer, d’obtenir, de mettre à jour et de supprimer la configuration d'homologation publique Azure pour un circuit ExpressRoute.

<a id="to-create-azure-public-peering" class="xliff"></a>

### Pour créer une homologation publique Azure

1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network
```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité.

  Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.
3. Vérifiez que le circuit ExpressRoute est approvisionné.

  Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé. Voir l’exemple suivant :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse sera similaire à l’exemple suivant :

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
4. Configurez l’homologation publique Azure pour le circuit.

Assurez-vous de disposer des informations suivantes avant de continuer :

 * Un sous-réseau /30 pour le lien principal. Ce doit être un préfixe IPv4 public valide.
 * Un sous-réseau /30 pour le lien secondaire. Ce doit être un préfixe IPv4 public valide.
 * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
 * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
 * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.

Vous pouvez exécuter l’applet de commande suivante pour configurer l’homologation publique Azure pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  Vous pouvez utiliser l’exemple suivant si vous choisissez d'utiliser un hachage MD5 :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

  > [!IMPORTANT]
  > Veillez à spécifier votre numéro AS comme ASN d’homologation et non pas comme ASN client.
  > 
  >

<a id="to-view-azure-public-peering-details" class="xliff"></a>

### Pour afficher les détails d’une homologation publique Azure

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante :

```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

<a id="to-update-azure-public-peering-configuration" class="xliff"></a>

### Pour mettre à jour la configuration d'homologation publique Azure

Vous pouvez mettre à jour n’importe quelle partie de la configuration à l’aide de l’applet de commande suivante :

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

L'ID VLAN du circuit est mis à jour de 200 à 600 dans l’exemple ci-dessus.

<a id="to-delete-azure-public-peering" class="xliff"></a>

### Pour supprimer une homologation publique Azure

Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante :

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

<a id="microsoft-peering" class="xliff"></a>

## Homologation Microsoft

Cette section fournit des instructions sur la façon de créer, d’obtenir, de mettre à jour et de supprimer la configuration d'homologation Microsoft pour un circuit ExpressRoute. 

<a id="to-create-microsoft-peering" class="xliff"></a>

### Pour créer une homologation Microsoft
1. Importez le module PowerShell pour ExpressRoute.

  Vous devez installer la dernière version du programme d'installation PowerShell à partir de [PowerShell Gallery](http://www.powershellgallery.com/) et importer les modules Azure Resource Manager dans la session PowerShell pour utiliser les applets de commande ExpressRoute. Vous devrez exécuter PowerShell en tant qu'administrateur.

  ```powershell
  Install-Module AzureRM

  Install-AzureRM
  ```

  Importez tous les modules AzureRM.* dans la plage de version sémantique connue.

  ```powershell
  Import-AzureRM
  ```

  Vous pouvez également importer un seul module sélectionné dans la plage de version sémantique connue.

  ```powershell
  Import-Module AzureRM.Network
  ```

  Connectez-vous à votre compte.

  ```powershell
  Login-AzureRmAccount
  ```

  Sélectionnez l’abonnement pour lequel vous souhaitez créer le circuit ExpressRoute.

  ```powershell
Select-AzureRmSubscription -SubscriptionId "<subscription ID>"
  ```
2. Créez un circuit ExpressRoute.

  Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le approvisionner par votre fournisseur de connectivité.

  Si votre fournisseur de connectivité propose des services gérés de couche 3, vous pouvez lui demander d’activer l'homologation privée Azure pour vous. Dans ce cas, vous n'aurez pas besoin de suivre les instructions indiquées dans les sections suivantes. Toutefois, si votre fournisseur de connectivité ne gère pas le routage pour vous, après avoir créé votre circuit, suivez les instructions ci-dessous.
3. Vérifiez que le circuit ExpressRoute est approvisionné.

  Vous devez tout d'abord vérifier que le circuit ExpressRoute est approvisionné et activé. Voir l’exemple suivant :

  ```powershell
  Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```

  La réponse sera similaire à l’exemple suivant :

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
4. Configurez l’homologation Microsoft pour le circuit.

  Assurez-vous de disposer des informations suivantes avant de poursuivre :

  * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
  * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
  * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
  * Préfixes publiés : vous devez fournir une liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Vous pouvez envoyer une liste séparée par des virgules si vous prévoyez d'envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
  * ASN client : si vous publiez des préfixes non enregistrés dans le numéro AS d’homologation, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés. **Cette étape est facultative**.
  * Nom du registre de routage : vous pouvez spécifier les registres RIR/IRR par rapport auxquels le numéro AS et les préfixes sont enregistrés.
  * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative.**

  Vous pouvez exécuter l'applet de commande suivante afin de configurer l’homologation Microsoft pour votre circuit :

  ```powershell
  Add-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "123.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

<a id="to-get-microsoft-peering-details" class="xliff"></a>

### Pour obtenir des détails sur l’homologation Microsoft

Vous pouvez obtenir des détails sur la configuration à l’aide de l’applet de commande suivante :

```powershell
$ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt
```

<a id="to-update-microsoft-peering-configuration" class="xliff"></a>

### Pour mettre à jour la configuration d’homologation Microsoft

Vous pouvez mettre à jour n’importe quelle partie de la configuration à l’aide de l’applet de commande suivante :

```powershell
Set-AzureRmExpressRouteCircuitPeeringConfig  -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt -PeeringType MicrosoftPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 300 -MicrosoftConfigAdvertisedPublicPrefixes "124.1.0.0/24" -MicrosoftConfigCustomerAsn 23 -MicrosoftConfigRoutingRegistryName "ARIN"

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

<a id="to-delete-microsoft-peering" class="xliff"></a>

### Pour supprimer une homologation Microsoft

Vous pouvez supprimer votre configuration d’homologation en exécutant l’applet de commande suivante :

```powershell
Remove-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
```

<a id="next-steps" class="xliff"></a>

## Étapes suivantes

Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).

