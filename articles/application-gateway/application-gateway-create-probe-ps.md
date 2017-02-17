---
title: "Créer une sonde personnalisée - Passerelle Azure Application Gateway - PowerShell | Microsoft Docs"
description: "Apprenez à créer une sonde personnalisée pour la passerelle Application Gateway à l&quot;aide de PowerShell dans Resource Manager"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 794797d9c42ec7f2fc351bab109147e45ce06070


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Création d'une sonde personnalisée pour Azure Application Gateway avec PowerShell pour Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-probe-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du [modèle de déploiement classique](application-gateway-create-probe-classic-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Étape 1

Utilisez `Login-AzureRmAccount` pour l’authentification.

```powershell
Login-AzureRmAccount
```

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

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé **appgw-RG**, ainsi que l’emplacement **West US**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

Les étapes suivantes créent un réseau virtuel et un sous-réseau pour la passerelle d'application.

### <a name="step-1"></a>Étape 1 :

Attribuez la plage d'adresses 10.0.0.0/24 à une variable de sous-réseau à utiliser pour créer un réseau virtuel.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Étape 2 :

Créez un réseau virtuel nommé **appgwvnet** dans le groupe de ressources **appgw-rg** pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Étape 3

Attribuez une variable de sous-réseau pour les prochaines étapes de création d'une passerelle d'application.

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Création d'une adresse IP publique pour la configuration frontale

Créez une ressource IP publique **publicIP01** dans le groupe de ressources **appgw-rg** pour la région « West US ».

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Création d'un objet de configuration de passerelle Application Gateway avec une sonde personnalisée

Avant de créer la passerelle Application Gateway, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### <a name="step-1"></a>Étape 1

Créez une configuration IP de passerelle Application Gateway nommée **gatewayIP01**. Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Étape 2 :

Configurez le pool d’adresses IP principal nommé **pool01** avec les adresses IP **134.170.185.46, 134.170.188.221 et 134.170.185.50**. Ces valeurs correspondent aux adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>Étape 3

La sonde personnalisée est configurée pendant cette opération.

Les paramètres utilisés sont :

* **Intervalle** : configure les vérifications d’intervalle de sonde en secondes.
* **Délai d’expiration** : définit le délai d’expiration d’un contrôle de réponse HTTP.
* **Nom d’hôte et chemin d’accès** : chemin d’accès complet à l’URL qui est appelé par la passerelle Application Gateway pour déterminer l’état de l’instance. Par exemple : avec un site web **http://contoso.com/**, la sonde personnalisée peut être configurée pour **http://contoso.com/path/custompath.htm** afin que les contrôles de sonde renvoient une réponse HTTP réussie.
* **Seuil de défaillance sur le plan de l’intégrité** : le nombre d’échecs de réponses HTTP nécessaires pour marquer l’instance de serveur principal comme **défectueuse**.

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>Étape 4

Configurez le paramètre de passerelle Application Gateway **poolsetting01** pour le trafic du pool principal. Cette étape comporte également une configuration du délai d'expiration de la réponse du pool principal à une requête de passerelle Application Gateway. Lorsqu'une réponse du serveur principal atteint une limite d'expiration, la passerelle Application Gateway annule la demande. Cette valeur est différente du délai d’expiration d’analyse qui s’applique uniquement à la réponse du serveur principal aux contrôles d’analyse.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>Étape 5

Configurez le port IP frontal nommé **frontendport01** pour le point de terminaison IP public.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>Étape 6

Créez la configuration IP frontale nommée **fipconfig01** et associez l’adresse IP publique à cette configuration.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>Étape 7

Créez l’écouteur nommé **listener01** et associez le port frontal à la configuration IP frontale.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>Étape 8

Créez la règle d’acheminement d’équilibrage de charge nommée **rule01** qui configure le comportement d’équilibrage de charge.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>Étape 9

Configurez la taille d'instance de la passerelle Application Gateway.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> La valeur par défaut du paramètre **InstanceCount** est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre **GatewaySize** est Medium. Vous pouvez choisir entre **Standard_Small**, **Standard_Medium** et **Standard_Large**. 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Création d'une passerelle Application Gateway avec New-AzureRmApplicationGateway

Créez une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Ajoute une sonde à une passerelle d’application existante

Quatre étapes permettent d’ajouter une sonde personnalisée à une passerelle d’application existante.

### <a name="step-1"></a>Étape 1

Charger la ressource de passerelle Application Gateway dans une variable PowerShell avec `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Étape 2 :

Ajouter une sonde à la configuration de passerelle existante.

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

Dans l’exemple, la sonde personnalisée est configurée pour vérifier l’URL du chemin d’accès contoso.com/path/custompath.htm toutes les 30 secondes. Un seuil de délai d'expiration de 120 secondes est configuré avec un maximum de 8 demandes d'analyse ayant échoué.

### <a name="step-3"></a>Étape 3

Ajouter la sonde au pool principal en définissant la configuration et le délai d’expiration avec `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>Étape 4

Enregistrer la configuration sur la passerelle Application Gateway avec `Set-AzureRmApplicationGateway`.

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Supprimer une sonde d’une passerelle d’application existante

Voici les opérations qui permettent de supprimer une sonde personnalisée d’une passerelle application.

### <a name="step-1"></a>Étape 1

Charger la ressource de passerelle Application Gateway dans une variable PowerShell avec `Get-AzureRmApplicationGateway`.

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Étape 2 :

Supprimer la configuration de sonde de la passerelle Application Gateway avec `Remove-AzureRmApplicationGatewayProbeConfig`.

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>Étape 3

Mettre à jour la configuration du pool principal pour supprimer la sonde et le paramètre de délai en utilisant `Set-AzureRmApplicationGatewayBackendHttpSettings`.

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Étape 4

Enregistrer la configuration sur la passerelle Application Gateway avec `Set-AzureRmApplicationGateway`. 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
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

Apprenez à configurer le déchargement SSL en consultant [Configurer le déchargement SSL](application-gateway-ssl-arm.md)




<!--HONumber=Jan17_HO4-->


