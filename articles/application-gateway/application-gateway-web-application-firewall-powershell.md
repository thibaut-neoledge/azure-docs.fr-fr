---
title: "Configurer un pare-feu d’applications web : passerelle Application Gateway Azure | Microsoft Docs"
description: "Cet article explique comment utiliser un pare-feu d’applications web sur une passerelle d’application nouvelle ou existante."
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 52b7728c3fc702e37f5c5fe3d6544117a11464e8
ms.lasthandoff: 03/30/2017


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurer un pare-feu d’application web sur une passerelle Application Gateway nouvelle ou existante

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Le pare-feu d’applications web (WAF, Web Application Firewall) d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session.

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. L’application offre de nombreuses fonctionnalités Application Delivery Controller (ADC), notamment : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez Vue d’ensemble de la passerelle Application Gateway

L’article suivant montre comment [ajouter un pare-feu d’applications web à une passerelle d’application existante](#add-web-application-firewall-to-an-existing-application-gateway) et [créer une passerelle d’application qui utilise le pare-feu d’applications web](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu la rubrique [Création d’une passerelle Application Gateway avec PowerShell](application-gateway-create-gateway-arm.md), vous connaissez les paramètres de référence (SKU) à configurer lors de la création d’une passerelle d’application. WAF inclut des paramètres supplémentaires à définir lors de la configuration de la référence SKU sur une passerelle d’application. Aucune autre modification n’est nécessaire sur la passerelle d’application elle-même.

**SKU** - Une passerelle Application Gateway standard sans WAF prend en charge les tailles **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**. Avec l’introduction de WAF, deux autres SKU sont disponibles : **WAF\_Medium** et **WAF\_Large**. WAF n’est pas pris en charge sur les petites passerelles d’application.

**Niveau** - Les valeurs disponibles sont **Standard** ou **WAF**. Lorsque vous utilisez un pare-feu d’applications web, vous devez choisir **WAF**.

**Mode** - Ce paramètre indique le mode de WAF. Les valeurs autorisées sont **Détection** et **Prévention**. Lorsque WAF est configuré en mode de détection, toutes les menaces sont stockées dans un fichier journal. En mode de prévention, les événements sont toujours consignés, mais l’attaquant reçoit une erreur d’autorisation de type 403 de la part de la passerelle d’application.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle d’application existante

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1 :

Connectez-vous à votre compte Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Étape 2 :

Sélectionnez l’abonnement à utiliser pour ce scénario.

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Étape 3

Récupérez la passerelle à laquelle vous ajoutez un pare-feu d’applications web.

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>Étape 4

Configurez la référence SKU du pare-feu d’applications web. Les tailles disponibles sont **WAF\_Large** et **WAF\_Medium**. Lorsque le pare-feu d’application web est utilisé, le niveau doit être **WAF**, la capacité doit être confirmée lors de la définition de la référence (SKU).

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>Étape 5

Configurez les paramètres WAF comme indiqué dans l’exemple suivant :

Pour le paramètre **WafMode** , les valeurs disponibles sont prévention et détection.

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>Étape 6

Mettez à jour la passerelle d’application avec les paramètres définis à l’étape précédente.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

Cette commande met à jour la passerelle d’application avec le pare-feu d’applications web. Il est recommandé de consulter la rubrique [Diagnostics Application Gateway](application-gateway-diagnostics.md) pour comprendre comment afficher les journaux de votre passerelle d’application. En raison des critères de sécurité inhérents à WAF, les journaux doivent être régulièrement examinés pour comprendre la politique de sécurité appliquée à vos applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle Application Gateway avec le pare-feu d’applications web

Les étapes suivantes vous guident lors du processus de création d’une passerelle Application Gateway avec un pare-feu d’applications web et ce, du début à la fin.

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1 :

Connexion à Azure

```powershell
Login-AzureRmAccount
```

Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.

### <a name="step-2"></a>Étape 2 :

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utilisent le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md) .

### <a name="step-5"></a>Étape 5

Affectez une plage d’adresses au sous-réseau utilisé pour la passerelle Application Gateway elle-même.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> Le sous-réseau d’une application doit avoir un masque 28 bits minimum. Cette valeur offre 10 adresses disponibles dans le sous-réseau pour les instances de la passerelle Application Gateway. Avec un sous-réseau plus petit, vous risquez de ne plus pouvoir ajouter d’autres instances de votre passerelle d’application à l’avenir.


### <a name="step-6"></a>Étape 6

Affectez une plage d’adresses au pool d’adresses principales.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>Étape 7

Créez un réseau virtuel avec les sous-réseaux précédents dans le groupe de ressources créé à l’étape : [Création du groupe de ressources](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>Étape 8

Récupérez les ressources de réseau virtuel et les ressources de sous-réseau à utiliser dans les étapes suivantes :

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>Étape 9

Créez une ressource IP publique à utiliser pour la passerelle d’application. Cette adresse IP publique est utilisée dans une des étapes suivantes :

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> La passerelle Application Gateway ne prend pas en charge l’utilisation d’une adresse IP publique créée avec un nom de domaine défini. Seule une adresse IP publique avec un nom de domaine créé dynamiquement est prise en charge. Si vous avez besoin d’un nom DNS convivial pour la passerelle Application Gateway, il est recommandé d’utiliser un enregistrement cname comme alias.


### <a name="step-10"></a>Étape 10

Avant de créer la passerelle d’application, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

Créez une configuration IP de passerelle application : ce paramètre détermine quel sous-réseau utilise la passerelle Application Gateway. Au démarrage, la passerelle Application Gateway sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>Étape 11

Configurez le pool d’adresses IP principales avec les adresses IP des serveurs web principaux. Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacez les adresses IP suivantes pour ajouter vos propres points de terminaison d’adresse IP d’application.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>Étape 12

Téléchargez le certificat à utiliser sur les ressources du pool principal pour lequel le chiffrement SSL est activé.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>Étape 13

Configurez les paramètres http principaux de la passerelle d’application. Affectez le certificat téléchargé à l’étape précédente aux paramètres http.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>Étape 14

Configurez le port IP frontal pour le point de terminaison IP public. Ce port est le port auquel les utilisateurs finaux se connectent.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>Étape 15

Créez une configuration IP frontale : ce paramètre mappe une adresse IP privée ou publique au composant frontal de la passerelle d’application. L’étape suivante associe l’adresse IP publique à l’étape précédente à la configuration IP frontale.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>Étape 16

Configurez le certificat pour la passerelle d’application. Ce certificat sert à chiffrer et à chiffrer à nouveau le trafic sur la passerelle d’application.

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>Étape 17

Créez l’écouteur HTTP pour la passerelle d’application. Affectez la configuration IP frontale, le port et le certificat SSL à utiliser.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>Étape 18

Créez une règle d'acheminement d'équilibrage de charge nommée qui configure le comportement d'équilibrage de charge. Dans cet exemple, une simple règle de type tourniquet (round robin) est créée.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>Étape 19

Configurez la taille d'instance de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> Vous pouvez choisir entre **WAF\_Medium** et **WAF\_Large**. Lorsque vous utilisez WAF, le niveau est toujours **WAF**. La capacité est un nombre compris entre 1 et 10.

### <a name="step-20"></a>Étape 20

Configurez le mode WAF. Les valeurs acceptables sont **Prévention** et **Détection**.

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>Étape 21

Créez une passerelle Application Gateway avec tous les éléments de configuration de la procédure précédente. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> Les passerelles d’application créées avec la configuration de pare-feu d’application web de base sont définies avec la solution CRS 3.0 dédiée aux protections.

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle d’application, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle d’application. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Étapes suivantes

Apprenez à configurer la journalisation des diagnostics et à consigner les événements détectés ou bloqués par le pare-feu d’application web en consultant la rubrique [Diagnostics de la passerelle Application Gateway](application-gateway-diagnostics.md).

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

