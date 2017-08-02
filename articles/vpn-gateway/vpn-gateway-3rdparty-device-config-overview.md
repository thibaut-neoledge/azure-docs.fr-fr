---
title: "À propos de la configuration de périphériques VPN tiers pour se connecter à des passerelles VPN Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des configurations de périphériques VPN tiers pour une connexion à des passerelles VPN Azure."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: d941879aee6042b38b7f5569cd4e31cb78b4ad33
ms.openlocfilehash: 72dab85bb882b05d72cef26bef70437695b70416
ms.contentlocale: fr-fr
ms.lasthandoff: 07/10/2017


---
# <a name="overview-of-3rd-party-vpn-device-configurations"></a>Vue d’ensemble des configurations de périphériques VPN tiers
Cet article fournit une vue d’ensemble des configurations de périphériques VPN locaux pour une connexion à des passerelles VPN Azure. L’exemple de configuration de réseau virtuel et de passerelle VPN Azure permet de se connecter à différents périphériques VPN locaux avec les mêmes paramètres.

## <a name="device-requirements"></a>Configuration requise du périphérique
Les passerelles VPN Azure utilisent des suites de protocoles IPsec/IKE standard pour les tunnels VPN site à site (S2S). Reportez-vous à la page [À propos des périphériques VPN](vpn-gateway-about-vpn-devices.md) pour connaître les paramètres détaillés du protocole IPsec/IKE et les algorithmes de chiffrement par défaut des passerelles VPN Azure. Vous pouvez éventuellement spécifier la combinaison exacte d’algorithmes de chiffrement et les forces de clé souhaitée d’une connexion spécifique, comme décrit dans la rubrique [À propos des exigences de chiffrement](vpn-gateway-about-compliance-crypto.md).

## <a name ="singletunnel"></a>Tunnel VPN unique
La première topologie présente un tunnel VPN S2S unique entre une passerelle VPN Azure et votre périphérique VPN local. Vous pouvez éventuellement configurer le protocole BGP sur le tunnel VPN.

![tunnel unique](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Reportez-vous à la page [Création d’une connexion de site à site](vpn-gateway-howto-site-to-site-resource-manager-portal.md) pour obtenir des instructions détaillées, étape par étape. Les sections suivantes répertorient les paramètres nécessaires et fournissent un exemple de script PowerShell pour vous aider à démarrer.

### <a name="network-and-vpn-gateway-information"></a>Informations sur le réseau et la passerelle VPN
Cette section répertorie les paramètres de l’exemple ci-dessus.

| **Paramètre**                | **Valeur**                    |
| ---                          | ---                          |
| Préfixes d’adresse du réseau virtuel        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP de la passerelle VPN Azure         | IP de la passerelle VPN Azure         |
| Préfixes d’adresse locale | 10.51.0.0/16<br>10.52.0.0/16 |
| IP du périphérique VPN local    | IP du périphérique VPN local    |
| * ASN BGP du réseau virtuel                | 65010                        |
| * IP d’homologue BGP Azure           | 10.12.255.30                 |
| * ASN BGP local         | 65050                        |
| * IP d’homologue BGP local     | 10.52.255.254                |

* (*) Paramètres facultatifs pour BGP uniquement

### <a name="sample-powershell-script"></a>Exemple de script PowerShell
L’article [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) contient des instructions détaillées à ce propos. Cette section fournit un exemple de script pour vous aider à démarrer.

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subcription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name ="policybased"></a> [Facultatif] Utilisez une stratégie IPsec/IKE personnalisée avec l’option « UsePolicyBasedTrafficSelectors »
Si vos périphériques VPN ne prennent pas en charge les sélecteurs de trafic « universel » (configuration basée sur le routage/basée sur une interface virtuelle de tunnel), vous devrez créer une stratégie IPsec/IKE personnalisée et configurer l’option « UsePolicyBasedTrafficSelectors » comme indiqué dans [cet article](vpn-gateway-connect-multiple-policybased-rm-ps.md).

> [!IMPORTANT]
> Vous devez créer une stratégie IPsec/IKE afin d’activer l’option « UsePolicyBasedTrafficSelectors » sur la connexion.

L’exemple de script ci-dessous crée une stratégie IPsec/IKE avec les paramètres et les algorithmes suivants :
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, SA Lifetime 7200 seconds & 20480000KB (20GB)

Ensuite, il applique la stratégie et active l’option « UsePolicyBasedTrafficSelectors » sur la connexion.

```powershell
$ipsecpolicy5 = New-AzureRmIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name ="bgp"></a>[Facultatif] Utiliser le protocole BGP sur la connexion VPN S2S
Vous pouvez éventuellement utiliser le protocole BGP sur la connexion. Consultez l’article [Configurer BGP sur des passerelles VPN](vpn-gateway-bgp-resource-manager-ps.md). Deux différences sont à noter :

Les préfixes d’adresse locale peuvent être une adresse d’hôte unique, l’adresse IP de l’homologue BGP local :

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

Vous devez définir « -EnableBGP » sur $True lors de la création de la connexion :

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

## <a name="next-steps"></a>Étapes suivantes
Pour connaître les étapes de configuration des connexions en mode actif-actif entre des réseaux locaux ou entre deux réseaux virtuels, consultez la page [Configuring Active-Active VPN Gateways for Cross-Premises and VNet-to-VNet Connections](vpn-gateway-activeactive-rm-powershell.md) (Configuration des passerelles VPN actif-actif pour des connexions entre des réseaux locaux et entre deux réseaux virtuels).


