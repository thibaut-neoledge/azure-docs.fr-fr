---
title: "Configurer le déchargement SSL - Passerelle Azure Application Gateway - PowerShell | Microsoft Docs"
description: "Cette page fournit des instructions pour la création d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 21a45f8ac5b4cb5fd0f5513fb43f7ca263977393
ms.contentlocale: fr-fr
ms.lasthandoff: 08/04/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configuration d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-ssl-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-ssl-arm.md)
> * [Azure Classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Il est possible de configurer Azure Application Gateway de façon à mettre fin à la session SSL (Secure Sockets Layer) sur la passerelle pour éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## <a name="what-is-required-to-create-an-application-gateway"></a>Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?

* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.

**Notes de configuration supplémentaires :**

Pour configurer des certificats SSL, le protocole dans **HttpListener** doit passer à *Https* (sensible à la casse). L’élément **SslCertificate** doit être ajouté à **HttpListener** avec la valeur de variable configurée pour le certificat SSL. Le port du serveur frontal doit être mis à jour sur 443.

**Pour activer l’affinité basée sur les cookies**: une passerelle Application Gateway peut être configurée pour garantir qu’une requête d’une session client est toujours dirigée vers la même machine virtuelle dans la batterie de serveurs web. Ce scénario est réalisé par l’injection d’un cookie de session, qui permet à la passerelle de diriger le trafic de manière appropriée. Pour activer l’affinité basée sur les cookies, définissez **CookieBasedAffinity** sur *Activé* dans l’élément **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

La différence entre l’utilisation du modèle de déploiement Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les composants d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer une ressource Application Gateway.

La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créer une ressource de passerelle d’application

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Veillez à passer en mode PowerShell pour utiliser les applets de commande d’Azure Resource Manager. Pour plus d’informations, voir l’article [Utilisation de Windows Powershell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Étape 2

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

Vous êtes invité à saisir vos informations d’identification.

### <a name="step-3"></a>Étape 3 :

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce paramètre est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utilisent le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé **appgw-RG**, ainsi que l’emplacement **West US**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

### <a name="step-1"></a>Étape 1

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Cet exemple attribue la plage d’adresses 10.0.0.0/24 à une variable subnet à utiliser pour créer un réseau virtuel.

### <a name="step-2"></a>Étape 2 :

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

Cet exemple crée un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appw-rg** pour la région États-Unis de l’Ouest à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

### <a name="step-3"></a>Étape 3

```powershell
$subnet = $vnet.Subnets[0]
```

Cet exemple assigne l’objet de sous-réseau à la variable $subnet pour les étapes suivantes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Cet exemple crée une ressource IP publique **publicIP01** dans le groupe de ressources **appw-rg** pour la région États-Unis de l’Ouest.

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle Application Gateway

### <a name="step-1"></a>Étape 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Cet exemple crée une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

### <a name="step-2"></a>Étape 2 :

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
```

Cet exemple configure le pool d’adresses IP principal nommé **pool01** avec les adresses IP **134.170.185.46**, **134.170.188.221** et **134.170.185.50**. Ces valeurs correspondent aux adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacez les adresses IP de l’exemple précédent par les adresses IP des points de terminaison de votre application web.

### <a name="step-3"></a>Étape 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
```

Cet exemple configure le paramètre de passerelle Application Gateway **poolsetting01** pour le trafic réseau à charge équilibrée dans le pool principal.

### <a name="step-4"></a>Étape 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
```

Cet exemple configure le port IP frontal nommé **frontendport01** pour le point de terminaison IP public.

### <a name="step-5"></a>Étape 5

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
```

Cet exemple configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe contenir entre 4 et 12 caractères.

### <a name="step-6"></a>Étape 6

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

Cet exemple crée la configuration IP frontale nommée **fipconfig01** et associe l’adresse IP publique à cette configuration.

### <a name="step-7"></a>Étape 7

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

Cet exemple crée l’écouteur nommé **listener01** et associe le port frontal à la configuration IP et au certificat du serveur frontal.

### <a name="step-8"></a>Étape 8

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Cet exemple crée la règle d’acheminement d’équilibrage de charge nommée **rule01** qui configure le comportement d’équilibrage de charge.

### <a name="step-9"></a>Étape 9

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Cet exemple configure la taille d’instance de la passerelle Application Gateway.

> [!NOTE]
> La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Créer une passerelle Application Gateway avec New-AzureApplicationGateway

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert
```

Cet exemple crée une passerelle Application Gateway avec tous les éléments de configuration de la procédure précédente. Dans notre exemple, la passerelle Application Gateway est appelée **appgwtest**.

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

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne (ILB), consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)


