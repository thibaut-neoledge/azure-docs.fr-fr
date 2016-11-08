---
title: Créer une passerelle Application Gateway avec les règles de routage d’URL | Microsoft Docs
description: Cette page fournit des instructions pour créer et configurer une passerelle Application Gateway Azure avec les règles de routage d’URL
documentationcenter: na
services: application-gateway
author: georgewallace
manager: jdial
editor: tysonn

ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: gwallace

---
# Créer une passerelle Application Gateway à l’aide du routage basé sur le chemin
> [!div class="op_single_selector"]
> * [Portail Azure](application-gateway-create-url-route-portal.md)
> * [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> 
> 

Le routage basé sur le chemin d’URL vous permet d’associer des itinéraires basés sur le chemin d’accès de l’adresse URL de la requête Http. Il vérifie s’il existe un itinéraire vers un pool principal configuré pour les listes d’URL dans la passerelle Application Gateway et envoie le trafic réseau vers le pool principal défini. Une utilisation courante du routage basé sur l’URL consiste à équilibrer la charge des demandes pour différents types de contenu entre différents pools de serveurs principaux.

Le routage basé sur l’URL introduit un nouveau type de règle pour la passerelle Application Gateway. La passerelle Application Gateway comporte deux types de règles : une règle de base et PathBasedRouting. Le type de règle de base fournit le service de tourniquet (round robin) pour les pools principaux alors que PathBasedRouting, en plus de la distribution de tourniquet, prend également en compte le modèle de chemin de l’URL de demande lors du choix du pool principal.

> [!IMPORTANT]
> PathPattern : liste de modèles de chemin à utiliser pour la correspondance. Chaque modèle doit commencer par le signe / et le seul endroit où un astérisque "\*" est autorisé est à la fin. /xyz, /xyz* ou /xyz/* sont des exemples valides. La chaîne transmise à l’outil de correspondance de chemin n’inclut pas de texte après le premier signe ? ou #. De plus, ces caractères ne sont pas autorisés.
> 
> 

## Scénario
Dans l’exemple suivant, la passerelle Application Gateway gère le trafic pour contoso.com avec deux pools de serveurs principaux : un pool de serveurs vidéo et un pool de serveurs d’images.

Les demandes pour http://contoso.com/image* sont acheminées vers le pool de serveurs d’images (pool1) et celles pour http://contoso.com/video* sont acheminées vers le pool de serveurs vidéo (pool2). Un pool de serveurs par défaut (pool1) est sélectionné si aucun des modèles de chemin ne correspond.

![itinéraire d’URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Avant de commencer
1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vous créez un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs ajoutés au pool principal pour utiliser la passerelle Application Gateway doivent exister, ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?
* **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
* **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
* **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
* **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
* **Règle :** la règle lie l’écouteur et le pool de serveurs principaux, et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique.

## Créer une passerelle Application Gateway
La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.

Procédure de création d’une passerelle Application Gateway :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créez une ressource Application Gateway.

## Créer un groupe de ressources pour Resource Manager
Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec Azure Resource Manager](../powershell-azure-resource-manager.md).

### Étape 1
Connexion à Azure

    Login-AzureRmAccount

Vous êtes invité à saisir vos informations d’identification.<BR>

### Étape 2
Vérifiez les abonnements associés au compte.

    Get-AzureRmSubscription

### Étape 3
Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Étape 4
Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"

Vous pouvez également créer des balises pour un groupe de ressources pour la passerelle Application Gateway :

    $resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

> [!NOTE]
> Si vous devez configurer une sonde personnalisée pour votre passerelle Application Gateway, consultez [Création d’une passerelle Application Gateway avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Pour plus d’informations, découvrez les [sondes personnalisées et l’analyse du fonctionnement](application-gateway-probe-overview.md).
> 
> 

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway
L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager.

### Étape 1 :
Attribuez la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Étape 2 :
Créez un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l'aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Étape 3
Attribuez une variable de sous-réseau pour les prochaines étapes, qui permet de créer une passerelle Application Gateway.

    $subnet=$vnet.Subnets[0]

## Création d'une adresse IP publique pour la configuration frontale
Créez une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Une adresse IP est affectée à la passerelle Application Gateway au démarrage du service.

## Créer une configuration de passerelle Application Gateway
Tous les éléments de configuration doivent être installés avant de créer la passerelle Application Gateway. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### Étape 1 :
Créez une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Étape 2
Configurez les pools d’adresses IP principaux nommés « pool01 » et « pool2 » avec les adresses IP « 134.170.185.46, 134.170.188.221,134.170.185.50 » pour « pool1 » et « 134.170.186.46, 134.170.189.221,134.170.186.50 » pour « pool2 ».

    $pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

    $pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

Dans cet exemple, il existe deux pools principaux pour acheminer le trafic réseau selon le chemin d’URL. Un pool reçoit le trafic du chemin d’URL « /video » et l’autre pool reçoit le trafic du chemin « /image ». Remplacez les adresses IP précédentes pour ajouter vos propres points de terminaison d’adresse IP d’application.

### Étape 3 :
Configurez les paramètres de passerelle Application Gateway « poolsetting01 » et « poolsetting02 » pour le trafic réseau à charge équilibrée dans le pool principal. Dans cet exemple, vous configurez différents paramètres pour les pools principaux. Chaque pool principal peut avoir son propre paramètre de pool principal.

    $poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

    $poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Étape 4
Configurez l’adresse IP frontale avec un point de terminaison IP public.

    $fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Étape 5
Configurez le port frontal pour une passerelle Application Gateway.

    $fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Étape 6
Configurez l’écouteur. Cette étape configure l’écouteur pour l’adresse IP publique et le port utilisé pour recevoir le trafic réseau entrant.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Étape 7
Configurez les chemins de règles d’URL pour les pools principaux. Cette étape configure le chemin relatif utilisé par la passerelle Application Gateway pour définir le mappage entre le chemin d’URL et le pool principal qui est affecté pour gérer le trafic entrant.

L’exemple suivant crée deux règles : une pour le chemin « /image » qui achemine le trafic vers le pool principal « pool1 » et une autre pour le chemin « /video » qui achemine le trafic vers le pool principal « pool2 ».

    $imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

    $videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

La configuration de mappage des chemins de règles configure également un pool d’adresses principal par défaut si le chemin ne correspond à aucune des règles de chemins prédéfinies.

    $urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Étape 8
Créez un paramètre de règle. Cette étape configure la passerelle Application Gateway pour utiliser le routage basé sur le chemin d’URL.

    $rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap

### Étape 9
Configurez le nombre d’instances et taille de la passerelle Application Gateway.

    $sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Créer une passerelle Application Gateway
Créez une passerelle Application Gateway avec tous les objets de configuration à partir de la procédure précédente.

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Obtenir une passerelle Application Gateway
    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG

## Étapes suivantes
Pour en savoir plus sur le déchargement SSL (Secure Sockets Layer), consultez [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl-arm.md).

<!----HONumber=AcomDC_0824_2016-->