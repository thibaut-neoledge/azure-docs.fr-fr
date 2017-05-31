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
ms.date: 05/03/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: c23e7404f9eee6f1246cafc72c6733546cc82934
ms.contentlocale: fr-fr
ms.lasthandoff: 05/04/2017


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurer un pare-feu d’application web sur une passerelle Application Gateway nouvelle ou existante

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-web-application-firewall-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Découvrez comment créer une passerelle d’application avec un pare-feu d’applications web activé ou comment ajouter des pare-feu d’applications web à une passerelle d’application existante.

Le pare-feu d’applications web (WAF, Web Application Firewall) d’Azure Application Gateway protège les applications web des attaques basées sur le web courantes comme l’injection de code SQL, les attaques de script de site à site et les piratages de session.

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. L’application offre de nombreuses fonctionnalités Application Delivery Controller (ADC), notamment : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez Vue d’ensemble de la passerelle Application Gateway

L’article suivant montre comment [ajouter un pare-feu d’applications web à une passerelle d’application existante](#add-web-application-firewall-to-an-existing-application-gateway) et [créer une passerelle d’application qui utilise le pare-feu d’applications web](#create-an-application-gateway-with-web-application-firewall).

![image du scénario][scenario]

## <a name="waf-configuration-differences"></a>Différences de configuration WAF

Si vous avez lu la rubrique [Création d’une passerelle Application Gateway avec PowerShell](application-gateway-create-gateway-arm.md), vous connaissez les paramètres de référence (SKU) à configurer lors de la création d’une passerelle d’application. WAF inclut des paramètres supplémentaires à définir lors de la configuration de la référence SKU sur une passerelle d’application. Aucune autre modification n’est nécessaire sur la passerelle d’application elle-même.

| **Paramètre** | **Détails**
|---|---|
|**Référence (SKU)** |Une passerelle d’application normale sans WAF prend en charge les tailles **Standard\_Small**, **Standard\_Medium** et **Standard\_Large**. Avec l’introduction de WAF, deux autres SKU sont disponibles : **WAF\_Medium** et **WAF\_Large**. WAF n’est pas pris en charge sur les petites passerelles d’application.|
|**Niveau** | Les valeurs disponibles sont **Standard** ou **WAF**. Lorsque vous utilisez un pare-feu d’applications web, vous devez choisir **WAF**.|
|**Mode** | Ce paramètre indique le mode de WAF. Les valeurs autorisées sont **Détection** et **Prévention**. Lorsque WAF est configuré en mode de détection, toutes les menaces sont stockées dans un fichier journal. En mode de prévention, les événements sont toujours consignés, mais l’attaquant reçoit une erreur d’autorisation de type 403 de la part de la passerelle d’application.|

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Ajout d’un pare-feu d’applications web à une passerelle d’application existante

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Connectez-vous à votre compte Azure.

    ```powershell
    Login-AzureRmAccount
    ```

2. Sélectionnez l’abonnement à utiliser pour ce scénario.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. Récupérez la passerelle à laquelle vous ajoutez un pare-feu d’applications web.

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

1. Configurez la référence SKU du pare-feu d’applications web. Les tailles disponibles sont **WAF\_Large** et **WAF\_Medium**. Lorsque le pare-feu d’application web est utilisé, le niveau doit être **WAF**, la capacité doit être confirmée lors de la définition de la référence (SKU).

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

1. Configurez les paramètres WAF comme indiqué dans l’exemple suivant :

   Pour **FirewallMode**, les valeurs disponibles sont Prévention et Détection.

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

1. Mettez à jour la passerelle d’application avec les paramètres définis à l’étape précédente.

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

Cette commande met à jour la passerelle d’application avec le pare-feu d’applications web. Il est recommandé de consulter la rubrique [Diagnostics Application Gateway](application-gateway-diagnostics.md) pour comprendre comment afficher les journaux de votre passerelle d’application. En raison des critères de sécurité inhérents à WAF, les journaux doivent être régulièrement examinés pour comprendre la politique de sécurité appliquée à vos applications web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Créer une passerelle Application Gateway avec le pare-feu d’applications web

Les étapes suivantes vous guident lors du processus de création d’une passerelle Application Gateway avec un pare-feu d’applications web et ce, du début à la fin.

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

1. Connectez-vous à Azure en exécutant `Login-AzureRmAccount`. Vous êtes invité à vous authentifier avec vos informations d’identification.

1. Vérifiez les abonnements pour le compte en exécutant `Get-AzureRmSubscription`.

1. Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources pour la passerelle d’application.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utilisent le même groupe de ressources.

Dans l’exemple précédent, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md) .

### <a name="configure-virtual-network"></a>Configurer un réseau virtuel

Application Gateway nécessite son propre sous-réseau. Dans cette étape, vous créez un réseau virtuel avec un espace d’adressage de 10.0.0.0/16 et deux sous-réseaux, un pour la passerelle d’application et un pour les membres du pool principal.

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for Application Gateway instances. With a smaller subnet, you may not be able to add more instance of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the backend pool members subnet
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previous created subnets
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-public-ip-address"></a>Configurer une adresse IP publique

Pour pouvoir traiter les demandes externes, la passerelle d’application nécessite une adresse IP publique. Cette adresse IP publique ne doit pas avoir un `DomainNameLabel` défini pour être utilisé par la passerelle d’application.

```powershell
# Create a public IP address for use with the application gateway. Defining the domainnamelabel during creation is not supported for use with application gateway
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Configurer la passerelle Application Gateway

```powershell
# Create a IP configuration. This configures what subnet the Application Gateway uses. When Application Gateway starts, it picks up an IP address from the subnet configured and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a backend pool to hold the addresses or NICs for the application that application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authenication certificate that will be used to communicate with the backend servers
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Conifugre the backend HTTP settings to be used to define how traffic is routed to the backend pool. The authenication certificate used in the previous step is added to the backend http settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a frontend port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a frontend IP configuration to associate the public IP address with the application gateway
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end ip configuration, port, and ssl certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

#Create a load balancer routing rule that configures the load balancer behavior. In this example, a basic round robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

#Configure the waf configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway utilizing all the previously created configuration objects
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

