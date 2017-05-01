---
title: "Créer une passerelle Application Gateway avec des règles de routage d’URL | Microsoft Docs"
description: "Cette page fournit des instructions pour créer et configurer une passerelle Application Gateway Azure avec les règles de routage d’URL"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 303cb9950f46916fbdd58762acd1608c925c1328
ms.openlocfilehash: 76dfd1c2b2f17e6bc798f4313c4dc4817d2136a4
ms.lasthandoff: 04/04/2017


---
# <a name="create-an-application-gateway-using-path-based-routing"></a>Créer une passerelle Application Gateway à l’aide du routage basé sur le chemin

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)

Le routage basé sur le chemin d’URL vous permet d’associer des routes basées sur le chemin d’URL de la requête HTTP. Il vérifie s’il existe une route vers un pool principal configuré pour les listes d’URL dans la passerelle Application Gateway et envoie le trafic réseau vers le pool principal défini. Une utilisation courante du routage basé sur l’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

Le routage basé sur l’URL introduit un nouveau type de règle pour la passerelle Application Gateway. La passerelle Application Gateway comporte deux types de règles : une règle de base et PathBasedRouting. Le type de règle de base fournit le service de tourniquet (round robin) pour les pools principaux alors que PathBasedRouting, en plus de la distribution de tourniquet, prend également en compte le modèle de chemin de l’URL de demande lors du choix du pool principal.

## <a name="scenario"></a>Scénario

Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com avec deux pools de serveurs principaux : un pool de serveurs vidéo et un pool de serveurs d’images.

Les demandes pour http://contoso.com/image* sont routées vers le pool de serveurs d’images (pool1) et celles pour http://contoso.com/video* sont routées vers le pool de serveurs vidéo (pool2). Si aucun des modèles de chemin ne correspond, un pool de serveurs par défaut (pool1) est sélectionné.

![itinéraire d’URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs ajoutés au pool principal pour utiliser la passerelle Application Gateway doivent exister, ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## <a name="what-is-required-to-create-an-application-gateway"></a>Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?

* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway

La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.

Procédure de création d’une passerelle Application Gateway :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créez une ressource Application Gateway.

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Étape 1 :

Connexion à Azure

```powershell
Login-AzureRmAccount
```

Vous êtes invité à vous authentifier à l’aide de vos informations d’identification.<BR>

### <a name="step-2"></a>Étape 2 :

Vérifiez les abonnements associés au compte.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

Vous pouvez également créer des balises pour un groupe de ressources pour la passerelle Application Gateway :

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md) .
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager. Cet exemple crée un réseau virtuel pour Application Gateway. Application Gateway requiert son propre sous-réseau. C’est la raison pour laquelle le sous-réseau créé pour Application Gateway est plus petit que l’espace d’adressage du réseau virtuel. Ainsi, d’autres ressources, y compris sans s’y limiter les serveurs web, sont configurées dans le même réseau virtuel.

### <a name="step-1"></a>Étape 1

Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.  Cette étape crée l’objet de configuration du sous-réseau pour Application Gateway, qui est utilisé dans l’exemple suivant.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Étape 2

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appgw-rg** pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24. Cette étape termine la configuration du réseau virtuel avec un seul sous-réseau de résidence d’Application Gateway.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Étape 3 :

Affectez la variable de sous-réseau pour les étapes suivantes, transmise à l’applet de commande `New-AzureRMApplicationGateway` dans une étape ultérieure.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appgw-rg** pour la région « West US ». Application Gateway peut utiliser une adresse IP publique, une adresse IP interne ou les deux pour recevoir les demandes d’équilibrage de charge.  Cet exemple utilise uniquement une adresse IP publique. Dans l’exemple suivant, aucun nom DNS n’est configuré pour la création de l’adresse IP publique.  Application Gateway ne prend pas en charge de noms DNS personnalisés sur des adresses IP publiques.  Si un nom personnalisé est requis pour le point de terminaison public, un enregistrement CNAME doit être créé pour pointer vers le nom DNS généré automatiquement pour l’adresse IP publique.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## <a name="create-application-gateway-configuration"></a>Créer une configuration de passerelle Application Gateway

Tous les éléments de configuration doivent être installés avant de créer la passerelle Application Gateway. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Étape 2

Configurez les pools d’adresses IP principaux nommés **pool01** et **pool2** avec les adresses IP pour **pool1** et **pool2**. Ces adresses IP sont les adresses IP des ressources qui hébergent l’application web devant être protégée par Application Gateway. L’intégrité des membres du pool principal est validée par des sondes de base ou personnalisées.  Le trafic est alors routé vers ces membres lorsque les demandes arrivent dans Application Gateway. Les pools principaux peuvent être utilisés par plusieurs règles au sein d’Application Gateway, ce qui signifie qu’un pool principal peut être utilisé pour plusieurs applications web résidant sur le même hôte.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Dans cet exemple, il existe deux pools principaux pour acheminer le trafic réseau selon le chemin d’URL. Un pool reçoit le trafic du chemin d’URL « /video » et l’autre pool reçoit le trafic du chemin « /image ». Remplacez les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application. 

### <a name="step-3"></a>Étape 3

Configurez les paramètres de passerelle Application Gateway **poolsetting01** et **poolsetting02** pour le trafic réseau à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour les pools principaux. Chaque pool principal peut avoir son propre paramètre de pool principal.  Les paramètres HTTP du serveur principal sont utilisés par des règles pour router le trafic vers les membres appropriés du pool principal. Ils déterminent le protocole et le port utilisés lors de l’envoi du trafic vers les membres du pool principal. Les sessions basées sur les cookies sont également déterminées par les paramètres HTTP du serveur principal.  Si elle est activée, l’affinité de session basée sur les cookies envoie le trafic vers le même serveur principal que les requêtes précédentes pour chaque paquet.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Étape 4

Configurez l’adresse IP frontale avec un point de terminaison IP public. L’objet de configuration de l’adresse IP frontale est utilisé par un écouteur pour associer l’adresse IP vers l’extérieur avec l’écouteur.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Étape 5

Configurez le port frontal pour une passerelle Application Gateway. L’objet de configuration du port frontal est utilisé par un écouteur pour définir le port écouté par Application Gateway pour connaître le trafic sur l’écouteur.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Étape 6

Configurez l’écouteur. Cette étape configure l’écouteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant. L’exemple suivant utilise la configuration de l’adresse IP frontale configurée précédemment, la configuration de port frontal et un protocole (http ou https) et configure l’écouteur. Dans cet exemple, l’écouteur écoute le trafic HTTP sur le port 80 sur l’adresse IP publique créée précédemment.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Étape 7

Configurez les chemins de règles d’URL pour les pools principaux. Cette étape configure le chemin relatif utilisé par la passerelle Application Gateway pour définir le mappage entre le chemin d’URL et le pool principal qui est affecté pour gérer le trafic entrant.

> [!IMPORTANT]
> Chaque chemin d’accès doit commencer par le signe / et le seul endroit où un astérisque (\*) est autorisé est à la fin. /xyz, /xyz ou /xyz/ sont des exemples valides. La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe ? ou #. De plus, ces caractères ne sont pas autorisés. 

L’exemple suivant crée deux règles : une pour le chemin « /image » qui achemine le trafic vers le pool principal « pool1 » et une autre pour le chemin « /video » qui achemine le trafic vers le pool principal « pool2 ». Ces règles garantissent que le trafic de chaque jeu d’URL est routé vers le serveur principal. Par exemple, http://contoso.com/image/figure1.jpg accède à pool1 et http://contoso.com/video/example.mp4 à pool2.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

Si le chemin ne correspond à aucune des règles de chemins prédéfinies, la configuration de mappage des chemins de règles configure également un pool d’adresses principal par défaut. Par exemple, http://contoso.com/shoppingcart/test.html accède à pool1, car il est défini en tant que pool par défaut pour le trafic sans correspondance.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Étape 8

Créez un paramètre de règle. Cette étape configure la passerelle Application Gateway pour utiliser le routage basé sur le chemin d’URL. La variable `$urlPathMap` définie à l’étape précédente est maintenant utilisée pour créer la règle de chemin d’accès. Dans cette étape, nous associons la règle avec un écouteur et le mappage de chemin d’accès d’URL créé précédemment.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Étape 9

Configurez le nombre d’instances et taille de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle Application Gateway avec tous les objets de configuration à partir de la procédure précédente.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway

Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle Application Gateway. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour configurer l’enregistrement CNAME d’adresses IP frontales, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

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

Pour en savoir plus sur le déchargement SSL (Secure Sockets Layer), consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl-arm.md).


