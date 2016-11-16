---
title: "Créer, démarrer ou supprimer une passerelle Application Gateway à l’aide d’Azure Resource Manager | Docs"
description: "Cette page fournit des instructions pour la création, la configuration, le démarrage et la suppression d’une passerelle Azure Application Gateway à l’aide d’Azure Resource Manager"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/10/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d4da5dd65b39011ed0abb8bbbd47542d66e73ad6


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Créer, démarrer ou supprimer une passerelle Application Gateway à l’aide d’Azure Resource Manager
> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-gateway-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell](application-gateway-create-gateway.md)
> * [Modèle Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interface de ligne de commande Azure](application-gateway-create-gateway-cli.md)
> 
> 

La passerelle Azure Application Gateway est un équilibreur de charge de couche 7. Elle assure l’exécution des requêtes HTTP de basculement et de routage des performances entre serveurs locaux ou dans le cloud. Application Gateway offre de nombreuses fonctionnalités de contrôleur de livraison d’applications (ADC) : équilibrage de charge HTTP, affinité de session basée sur les cookies, déchargement SSL (Secure Sockets Layer), sondes d’intégrité personnalisées, prise en charge de plusieurs sites, etc. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md)

Cet article vous guide lors des étapes de création, de configuration, de démarrage et de suppression d’une passerelle Application Gateway.

> [!IMPORTANT]
> Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Resource Manager et classique. Attention à bien comprendre les [modèles et outils de déploiement](../azure-classic-rm.md) avant d’utiliser une ressource Azure. Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article. Ce document traite de la création d’une passerelle Application Gateway à l’aide d’Azure Resource Manager. Pour utiliser la version classique, accédez à [Création d’un déploiement classique de passerelle Application Gateway à l’aide de PowerShell](application-gateway-create-gateway.md).
> 
> 

## <a name="before-you-begin"></a>Avant de commencer
1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Si vous disposez d’un réseau virtuel, sélectionnez un sous-réseau vide existant ou créez un sous-réseau de votre réseau virtuel existant uniquement pour une utilisation par la passerelle Application Gateway. Vous ne pouvez pas déployer la passerelle d’application sur un autre réseau virtuel constitué par les ressources que vous avez l’intention de déployer derrière la passerelle d’application.
3. Les serveurs que vous configurez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## <a name="what-is-required-to-create-an-application-gateway"></a>Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?
* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique.

## <a name="create-an-application-gateway"></a>Créer une passerelle Application Gateway
La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.

Pour créer une passerelle d’application, procédez comme suit :

## <a name="create-a-resource-group-for-resource-manager"></a>Créer un groupe de ressources pour Resource Manager
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
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Étape 4
Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utilisent le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md) .
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway
L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager.

### <a name="step-1"></a>Étape 1
Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Étape 2
Créez un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l'aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Étape 3
Attribuez une variable de sous-réseau pour les prochaines étapes de création d'une passerelle d'application.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-frontend-configuration"></a>Création d'une adresse IP publique pour la configuration frontale
Créez une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object"></a>Créer un objet de configuration de passerelle Application Gateway
Avant de créer la passerelle d’application, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1 :
Créez une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Au démarrage, la passerelle Application Gateway sélectionne une adresse IP du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Étape 2 :
Configurez le pool d'adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacez les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
```

### <a name="step-3"></a>Étape 3
Configurez les paramètres de passerelle Application Gateway « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Étape 4
Configurez le port IP frontal nommé « frontendport01 » pour le point de terminaison IP public.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Étape 5
Créez la configuration IP frontale nommée « fipconfig01 » et associez l’adresse IP publique à cette configuration.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Étape 6
Créez l’écouteur nommé « listener01 » et associez le port frontal à la configuration IP frontale.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Étape 7
Créez la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Étape 8
Configurez la taille d’instance de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard_Small, Standard_Medium et Standard_Large.
> 
> 

## <a name="create-an-application-gateway-by-using-newazurermapplicationgateway"></a>Création d'une passerelle Application Gateway avec New-AzureRmApplicationGateway
Créez une passerelle Application Gateway avec tous les éléments de configuration de la procédure précédente. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

### <a name="step-9"></a>Étape 9
Récupérez les informations relatives au DNS et à l’adresse IP virtuelle de la passerelle Application Gateway à partir de la ressource IP publique attachée à la passerelle Application Gateway.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-an-application-gateway"></a>Supprimer une passerelle Application Gateway
Pour supprimer une passerelle Application Gateway, procédez comme suit :

### <a name="step-1"></a>Étape 1 :
Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw ».

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Étape 2 :
Utilisez **Stop-AzureRmApplicationGateway** pour arrêter la passerelle Application Gateway.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

Une fois la passerelle Application Gateway arrêtée, utilisez l’applet de commande **Remove-AzureRmApplicationGateway** pour supprimer le service.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> Il est possible d’utiliser le commutateur **-force** pour supprimer le message de confirmation de suppression.
> 
> 

Pour vérifier que la passerelle a bien été supprimée, vous pouvez utiliser l’applet de commande **Get-AzureRmApplicationGateway** . Cette étape n'est pas requise.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Obtenir le nom DNS d’une passerelle Application Gateway
Une fois la passerelle créée, l’étape suivante consiste à configurer le serveur frontal pour la communication. Lorsque vous utilisez une adresse IP publique, la passerelle Application Gateway requiert un nom DNS attribué dynamiquement, ce qui n’est pas convivial. Pour s’assurer que les utilisateurs finaux peuvent atteindre la passerelle Application Gateway, un enregistrement CNAME peut être utilisé pour pointer vers le point de terminaison public de la passerelle Application Gateway. [Configuration d’un nom de domaine personnalisé pour Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Pour ce faire, récupérez les détails de la passerelle Application Gateway et de son nom IP/DNS associé à l’aide de l’élément PublicIPAddress attaché à la passerelle Application Gateway. Le nom DNS de la passerelle Application Gateway doit être utilisé pour créer un enregistrement CNAME qui pointe les deux applications web sur ce nom DNS. L’utilisation de A-records n’est pas recommandée étant donné que l’adresse IP virtuelle peut changer lors du redémarrage de la passerelle Application Gateway.

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
Si vous souhaitez configurer le déchargement SSL, consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous voulez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

* [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)




<!--HONumber=Nov16_HO2-->


