---
title: "Configurer le tunneling forcé pour les connexions de site à site à l’aide du modèle de déploiement Resource Manager | Microsoft Docs"
description: "Comment rediriger ou « forcer » tout le trafic Internet vers votre emplacement local."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: cbe58db8-b598-4c9f-ac88-62c865eb8721
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: acfa4642ad26f819189bd871d83718b6f579e32d


---
# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configuration du tunneling forcé à l’aide du modèle de déploiement Azure Resource Manager
> [!div class="op_single_selector"]
> * [PowerShell - Classique](vpn-gateway-about-forced-tunneling.md)
> * [PowerShell - Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

Le tunneling forcé vous permet de rediriger ou de « forcer » tout le trafic Internet vers votre emplacement local via un tunnel VPN site à site pour l’inspection et l’audit. Il s’agit d’une condition de sécurité critique pour la plupart des stratégies informatiques d’entreprise.

Sans le tunneling forcé, le trafic Internet depuis vos machines virtuelles dans Azure se fera toujours à partir de l’infrastructure du réseau Azure directement vers Internet, sans vous laisser inspecter ou vérifier le trafic. L’accès Internet non autorisé peut entraîner la divulgation d’informations ou tout autre type de faille de sécurité.

Cet article vous guide dans la configuration du tunneling forcé pour les réseaux virtuels créés à l’aide du modèle de déploiement Resource Manager.

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Outils et modèles de déploiement pour le tunneling forcé**

Une connexion de tunneling forcé peut être configurée pour les modèles de déploiement Classic et Resource Manager. Pour plus d’informations, consultez le tableau suivant. Nous mettons à jour ce tableau à mesure que de nouveaux articles, de nouveaux modèles de déploiement et des outils supplémentaires sont disponibles pour cette configuration. Quand un article est disponible, ce tableau contient un lien vers celui-ci.

[!INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)]

## <a name="about-forced-tunneling"></a>À propos du tunneling forcé
Le diagramme suivant illustre le fonctionnement du tunneling forcé. 

![Tunneling forcé](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Dans l’exemple ci-dessus, le sous-réseau frontal n’utilise pas le tunneling forcé. Les charges de travail du sous-réseau frontal peuvent continuer à accepter et à répondre aux demandes des clients directement à partir d’Internet. Les sous-réseaux intermédiaire et principal utilisent le tunneling forcé. Toutes les connexions sortantes à partir de ces deux sous-réseaux vers Internet seront forcées ou redirigées vers un site local via l’un des tunnels VPN S2S.

Cela vous permet de restreindre et d’inspecter l’accès à Internet à partir de vos machines virtuelles ou des services cloud dans Azure, tout en continuant d’activer votre architecture de service multiniveaux requise. Vous avez également la possibilité d’appliquer le tunneling forcé à tous les réseaux virtuels s’il n’existe aucune charge de travail Internet dans vos réseaux virtuels.

## <a name="requirements-and-considerations"></a>Conditions requises et éléments à prendre en compte
Le tunneling forcé dans Azure est configuré via les itinéraires de réseau virtuel défini par l’utilisateur. La redirection du trafic vers un site local est exprimée comme un itinéraire par défaut vers la passerelle VPN Azure. Pour plus d’informations sur les réseaux virtuels et les itinéraires définis par l’utilisateur, consultez [Présentation des itinéraires définis par l’utilisateur et du transfert IP](../virtual-network/virtual-networks-udr-overview.md).

* Chaque sous-réseau du réseau virtuel dispose d’une table de routage système intégrée. La table de routage système comporte les trois groupes d’itinéraires suivants :
  
  * **Itinéraire de réseau virtuel local :** directement vers les machines virtuelles de destination dans le même réseau virtuel
  * **Itinéraires locaux :** vers la passerelle VPN Azure
  * **Itinéraire par défaut :** directement vers Internet. Les paquets destinés à des adresses IP privées non couvertes par les deux itinéraires précédents sont supprimés.
* Cette procédure utilise des itinéraires définis par l’utilisateur (UDR) pour une table de routage pour ajouter un itinéraire par défaut, puis associer la table de routage à vos sous-réseaux pour activer le tunneling forcé sur ces sous-réseaux.
* Le tunneling forcé doit être associé à un réseau virtuel équipé d’une passerelle VPN avec itinéraire. Vous devez définir un « site par défaut » parmi les sites locaux intersites connectés au réseau virtuel.
* Le tunneling forcé ExpressRoute n'est pas configuré de cette manière, mais il est activé par la publication d’un itinéraire par défaut via les sessions d'homologation BGP ExpressRoute. Pour plus d’informations, voir [Documentation ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Présentation de la configuration
La procédure suivante vous aide à créer un groupe de ressources et un réseau virtuel. Vous créerez ensuite une passerelle VPN et configurerez le tunneling forcé. Dans cette procédure, le réseau virtuel « MultiTier-VNet » compte 3 sous-réseaux : *Frontend*, *Midtier* et *Backend*, et 4 connexions intersites : *DefaultSiteHQ* et 3 *Branches*.

La procédure définit *DefaultSiteHQ* comme connexion de site par défaut pour le tunneling forcé et configure les sous-réseaux Midtier et Backend de manière à ce qu’ils utilisent le tunneling forcé.

## <a name="before-you-begin"></a>Avant de commencer
Vérifiez que vous disposez des éléments ci-dessous avant de commencer votre configuration.

* Un abonnement Azure. Si vous ne disposez pas déjà d’un abonnement Azure, vous pouvez activer vos [avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).
* Vous aurez besoin d’installer la dernière version des applets de commande PowerShell Azure Resource Manager (version 1.0 ou ultérieure). Pour plus d’informations sur l’installation des applets de commande PowerShell, consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs) .

## <a name="configure-forced-tunneling"></a>Configurer un tunneling forcé
1. Dans la console PowerShell, connectez-vous à votre compte Azure. Cette applet de commande vous invite à saisir vos informations d’identification de connexion pour votre compte Azure. Une fois que vous êtes connecté, l’applet de commande télécharge vos paramètres de compte pour qu’ils soient reconnus par Azure PowerShell.
   
        Login-AzureRmAccount 
2. Obtenez la liste de vos abonnements Azure.
   
        Get-AzureRmSubscription
3. Spécifiez l’abonnement à utiliser. 
   
        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
4. Créez un groupe de ressources
   
        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"
5. Créez un réseau virtuel et spécifiez vos sous-réseaux. 
   
        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
6. Créez les passerelles de réseau local.
   
        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
7. Créez la table d’itinéraires et la règle de routage.
   
        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt
8. Associez la table d’itinéraire vers le niveau intermédiaire et les sous-réseaux principaux.
   
        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
9. Créez la passerelle avec un site par défaut. Cette opération prend un certain temps, parfois 45 minutes voire plus, car vous créez et configurez la passerelle.<br> `-GatewayDefaultSite` est le paramètre d’applet de commande qui permet à la configuration de routage forcé de fonctionner. Configurez ce paramètre correctement. Il est disponible uniquement dans PowerShell 1.0 ou une version ultérieure.
   
        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false
10. Établissez des connexions VPN de site à site.
    
         $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
         $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
         $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
         $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
         $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
         New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
         Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"




<!--HONumber=Dec16_HO2-->


