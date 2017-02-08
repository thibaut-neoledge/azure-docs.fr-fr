---
title: "Infrastructure et connectivité à SAP HANA sur Azure (grandes instances) | Microsoft Docs"
description: "Configurez l’infrastructure de connectivité requise pour utiliser SAP HANA sur Azure (grandes instances)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: a3e99cf86cbe6ae18366b13047d8c2994ff398f4
ms.openlocfilehash: 9adf67b706f866fbb41c2b82970eb0e44aada5c6


---
# <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>Infrastructure et connectivité à SAP HANA sur Azure (grandes instances)

Une fois l’achat de SAP HANA sur Azure (grandes instances) finalisé entre vous et l’équipe Microsoft responsable des comptes d’entreprise, les informations suivantes sont requises :

- Nom du client
- Coordonnées professionnelles (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées techniques (y compris l’adresse électronique et le numéro de téléphone)
- Coordonnées de mise en réseau technique (y compris l’adresse électronique et le numéro de téléphone)
- Région de déploiement Azure (États-Unis de l’Ouest ou de l’Est depuis septembre 2016)
- Confirmer la référence SKU (configuration) de SAP HANA sur Azure (grandes instances)
- Pour chaque région Azure dans laquelle le déploiement est effectué :
  - Une plage d’adresses IP /29 pour les connexions P2P
  - Un bloc CIDR (utilisé pour le pool NAT des grandes instances HANA ; /24 recommandé)
- Pour chaque réseau virtuel Azure se connectant aux grandes instances HANA, quelle que soit la région Azure :
  - Une ou plusieurs plages d’adresses IP /28 ou /27 (pour le sous-réseau de passerelle de réseau virtuel du client)
  - Un ou plusieurs blocs CIDR (pour le sous-réseau locataire du réseau virtuel du client ; /24 recommandé)
- Données pour chaque système de grande instance HANA :
  - Nom d’hôte souhaité
  - Adresse IP souhaitée à partir du pool NAT
- Numéro d’abonnement Azure pour l’abonnement Azure auquel SAP HANA sur les grandes instances Azure HANA sera directement connecté
- Nom de SID SAP HANA pour l’instance SAP HANA (requis pour créer les volumes de disque liés à SAP HANA nécessaires)

Une fois ces informations fournies, Microsoft déploie SAP HANA sur Azure (grandes instances).

Les informations de configuration de la mise en réseau vous sont alors fournies :

- Connecter vos réseaux virtuels Azure au circuit ExpressRoute qui relie les réseaux virtuels Azure aux grandes instances HANA
  - Pour Azure Resource Manager :
     - Clé(s) d’autorisation
     - PeerID ExpressRoute
- Accès aux grandes instances HANA avec le circuit ExpressRoute établi et le réseau virtuel Azure

## <a name="creating-an-azure-vnet"></a>Création d’un réseau virtuel Azure

Ce réseau virtuel Azure doit être créé à l’aide du modèle de déploiement Azure Resource Manager. L’ancien modèle de déploiement Azure, communément appelé ASM, n’est pas pris en charge pour cette solution.

Le réseau virtuel Azure créé doit au moins disposer d’un sous-réseau locataire et d’un sous-réseau de passerelle. Ceux-ci doivent se voir affecter les plages d’adresses IP tel que spécifié, et doivent être envoyés à Microsoft.

> [!IMPORTANT] 
> Seuls des blocs d’adresses locataires et de passerelle doivent être affectés au réseau virtuel dans l’abonnement Azure. Les blocs d’adresses P2P et de pool NAT doivent être séparés des espaces d’adresses de réseau virtuel et de sous-réseau tels qu’ils existent en dehors de l’abonnement Azure.

Plusieurs sous-réseaux locataires peuvent être utilisés (même avec des plages d’adresses non contiguës) mais, comme indiqué précédemment, ces plages d’adresses doivent être envoyées à Microsoft au préalable.

Vous pouvez utiliser n’importe quelle norme d’affectation de noms de votre choix pour ces sous-réseaux locataires. Toutefois, **il doit toujours y avoir un, et un seul, sous-réseau de passerelle pour chaque réseau virtuel** qui se connecte au circuit ExpressRoute SAP HANA sur Azure (grandes instances), et **ce sous-réseau de passerelle doit toujours être nommé &quot;GatewaySubnet&quot;** pour assurer le positionnement correct de la passerelle ExpressRoute.

> [!WARNING] 
> Il est essentiel que le sous-réseau de passerelle soit toujours nommé &quot;GatewaySubnet&quot;.

Le réseau virtuel peut être créé à l’aide du portail Azure, de PowerShell, d’un modèle Azure ou de l’interface de ligne de commande Azure (voir [Créer un réseau virtuel à l’aide du portail Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)).

## <a name="creating-a-gateway-subnet"></a>Création d’un sous-réseau de passerelle

Une fois le réseau virtuel Azure créé, vous devez créer une passerelle ExpressRoute sur le réseau virtuel pour lier le réseau virtuel au circuit ExpressRoute qui se connecte au locataire du client sur la grande instance.

> [!NOTE] 
> Cette étape peut prendre jusqu'à 30 minutes, dans la mesure où la nouvelle passerelle est créée dans l’abonnement Azure désigné, puis connectée au réseau virtuel Azure spécifié.

S’il existe déjà une passerelle, vérifiez s’il s’agit d’une passerelle ExpressRoute. Si ce n’est pas le cas, la passerelle doit être supprimée et recréée comme passerelle ExpressRoute. Si une passerelle ExpressRoute est déjà établie, étant donné que le réseau virtuel Azure est déjà connecté au circuit ExpressRoute pour la connectivité locale, passez à la section Liaison des réseaux virtuels ci-dessous.

- Utilisez soit le (nouveau) [portail Azure](https://portal.azure.com/), soit PowerShell pour créer une passerelle VPN ExpressRoute connectée à votre réseau virtuel.
  - Si vous utilisez le portail Azure, ajoutez une nouvelle **passerelle de réseau virtuel** , puis sélectionnez **ExpressRoute** comme type de passerelle.
  - Si vous avez choisi PowerShell, téléchargez et utilisez la dernière version du [kit de développement logiciel (SDK) Azure PowerShell](https://azure.microsoft.com/downloads/) pour garantir une expérience optimale. Les commandes suivantes créent une passerelle ExpressRoute. Le texte précédé par un _$_ correspond à des variables définies par l’utilisateur, qui doivent être mises à jour avec vos propres informations.

```
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA Large Instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzureRmVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzureRmPublicIpAddress -Name $myPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Dans cet exemple, la référence SKU de passerelle HighPerformance a été utilisée. Vous pouvez opter pour HighPerformance ou pour UltraPerformance, les seules références SKU de passerelle prises en charge pour SAP HANA sur Azure (grandes instances).

## <a name="linking-vnets"></a>Liaison de réseaux virtuels

Maintenant que le réseau virtuel Azure dispose d’une passerelle ExpressRoute, utilisez les informations d’autorisation fournies par Microsoft pour connecter la passerelle ExpressRoute au circuit ExpressRoute SAP HANA sur Azure (grandes instances) créé pour cette connexion. Cette action ne peut être effectuée qu’à l’aide de PowerShell (elle n’est pour le moment pas prise en charge par le portail Azure).

- Procédez comme suit pour chaque passerelle de réseau virtuel, en utilisant un paramètre AuthGUID différent pour chaque connexion. Les deux premières entrées ci-dessous proviennent des informations fournies par Microsoft. En outre, le paramètre AuthGUID est spécifique à chaque réseau virtuel et à sa passerelle.

```
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01-5Gb"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway Information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzureRmVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzureRmVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID
```

Vous devrez peut-être exécuter plusieurs fois cette étape si vous souhaitez connecter la passerelle à plusieurs circuits ExpressRoute qui sont associés à votre abonnement.

## <a name="adding-more-ip-addresses-or-subnets"></a>Ajout d’adresses IP ou sous-réseaux supplémentaires

Utilisez le portail Azure, PowerShell ou l’interface de ligne de commande pour ajouter des adresses IP ou sous-réseaux supplémentaires.

Si vous n’avez pas encore déclaré la plage d’espace d’adressage IP supplémentaire avec SAP HANA sur Azure Service Management, ouvrez une demande de support Azure pour qu’elle soit ajoutée. Après avoir reçu la confirmation, effectuez les étapes suivantes.

Pour créer un sous-réseau supplémentaire à partir du portail Azure, consultez l’article [Créer un réseau virtuel à l’aide du portail Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), et pour le créer à partir de PowerShell, consultez [Créer un réseau virtuel à l’aide de PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="adding-vnets"></a>Ajout de réseaux virtuels

Après la connexion initiale d’un ou plusieurs réseaux virtuels Azure, vous voudrez peut-être ajouter d’autres réseaux virtuels qui accèdent à SAP HANA sur Azure (grandes instances). Tout d’abord, envoyez une demande de support Azure, dans laquelle vous incluez à la fois les informations propres au déploiement Azure et les plages d’espace d’adressage IP pour le ou les sous-réseaux locataires et le ou les sous-réseaux de passerelle des réseaux virtuels Azure supplémentaires. SAP HANA sur Azure Service Management fournit alors les informations nécessaires à la connexion des réseaux virtuels supplémentaires et d’ExpressRoute.

Procédure d’ajout d’un nouveau réseau virtuel Azure :

1. Effectuez la première étape du processus d’intégration, voir la section _Création d’un réseau virtuel Azure_ ci-dessus.
2. Effectuez la deuxième étape du processus d’intégration, voir la section _Création d’un sous-réseau de passerelle_ ci-dessus.
3. Ouvrez une demande de support Azure contenant les informations sur le nouveau réseau virtuel et demandez une nouvelle clé d’autorisation pour connecter vos réseaux virtuels supplémentaires au circuit ExpressRoute de grande instance HANA.
4. Une fois informé que l’autorisation est accordée, utilisez les informations d’autorisation fournies par Microsoft pour finaliser la troisième étape du processus d’intégration, voir la section _Liaison de réseaux virtuels_ ci-dessus.

## <a name="increasing-expressroute-circuit-bandwidth"></a>Augmentation de la bande passante d’un circuit ExpressRoute

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’augmenter la bande passante du circuit ExpressRoute de SAP HANA sur Azure (grandes instances), créez une demande de support Azure. (Vous pouvez demander une augmentation maximale de 10 Gbits/s pour une seule bande passante de circuit.) Vous recevrez ensuite une notification une fois l’opération terminée ; aucune action supplémentaire n’est nécessaire pour activer cette vitesse supérieure dans Azure.

## <a name="adding-an-additional-expressroute-circuit"></a>Ajout d’un circuit ExpressRoute supplémentaire

Consultez SAP HANA sur Azure Service Management. S’il vous est recommandé d’ajouter un circuit ExpressRoute supplémentaire, envoyez une demande de support Azure pour créer un nouveau circuit ExpressRoute (et pour obtenir les informations d’autorisation permettant de s’y connecter). L’espace d’adressage qui sera utilisé sur les réseaux virtuels doit être défini avant d’effectuer la demande, afin que SAP HANA sur Azure Service Management puisse fournir l’autorisation.

Une fois le nouveau circuit créé et la configuration de SAP HANA sur Azure Service Management terminée, vous recevrez une notification avec les informations nécessaires pour continuer. Suivez les étapes ci-dessus pour créer et connecter le nouveau réseau virtuel à ce circuit supplémentaire. Vous ne pourrez pas connecter les réseaux virtuels Azure à ce circuit supplémentaire s’ils sont déjà connectés à un autre circuit ExpressRoute.

## <a name="deleting-a-subnet"></a>Suppression d’un sous-réseau

Pour supprimer un sous-réseau de réseau virtuel, vous pouvez utiliser le portail Azure, PowerShell ou l’interface de ligne de commande. Si vous supprimez un espace d’adressage, SAP HANA sur Azure Service Management doit être averti de la modification de l’espace d’adressage pour le supprimer des plages avec lesquelles SAP HANA sur Azure (grandes instances) est autorisé à communiquer.

Bien qu’il n’existe pas encore de guide Azure.com spécifiquement dédié à la suppression de sous-réseaux, le processus de suppression des sous-réseaux est l’inverse du processus d’ajout. Consultez l’article du portail Azure [Créer un réseau virtuel à l’aide du portail Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations sur la création de sous-réseaux.

## <a name="deleting-a-vnet"></a>Suppression d’un réseau virtuel

Utilisez le portail Azure, PowerShell ou l’interface de ligne de commande pour supprimer un réseau virtuel. SAP HANA sur Azure Service Management supprime les autorisations existantes sur le circuit ExpressRoute de SAP HANA sur Azure (grandes instances) et supprime les plages d’adresses IP (les plages du client et de la passerelle) pour la communication avec les grandes instances HANA.

Une fois que le réseau virtuel a été supprimé, ouvrez une demande de support Azure pour fournir les plages d’adresses IP à supprimer.

Bien qu’il n’existe pas encore de guide Azure.com spécifiquement dédié à la suppression de réseaux virtuels, le processus de suppression des réseaux virtuels est l’inverse du processus d’ajout décrit ci-dessus. Consultez les articles [Créer un réseau virtuel à l’aide du portail Azure](../../virtual-network/virtual-networks-create-vnet-arm-pportal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) et [Créer un réseau virtuel à l’aide de PowerShell](../../virtual-network/virtual-networks-create-vnet-arm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations sur la création de réseaux virtuels.

Pour vous assurer que tout est supprimé, supprimez les éléments suivants :

- **Pour Azure Resource Manager :** la connexion ExpressRoute, la passerelle de réseau virtuel, l’adresse IP publique de la passerelle de réseau virtuel et le réseau virtuel
- **Pour les machines virtuelles classiques :** la passerelle de réseau virtuel et le réseau virtuel

## <a name="deleting-an-expressroute-circuit"></a>Suppression d’un circuit ExpressRoute

Pour supprimer un circuit supplémentaire ExpressRoute de SAP HANA sur Azure (grandes instances), ouvrez une demande de support Azure avec SAP HANA sur Azure Service Management et demandez à ce que le circuit soit supprimé. Dans l’abonnement Azure, vous pouvez supprimer ou conserver le réseau virtuel, en fonction des besoins. Toutefois, vous devez supprimer la connexion (pour Azure Resource Manager) ou supprimer la liaison de la connexion (pour les machines virtuelles classiques) entre le circuit ExpressRoute HANA grandes instances et la passerelle de réseau virtuel liée.

Si vous souhaitez également supprimer un réseau virtuel, suivez les instructions du paragraphe Suppression d’un réseau virtuel dans la section précédente.





<!--HONumber=Dec16_HO2-->


