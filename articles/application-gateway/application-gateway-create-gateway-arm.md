<properties 
   pageTitle="Créer, démarrer ou supprimer une passerelle Application Gateway à l’aide d’Azure Resource Manager | Microsoft Azure"
   description="Cette page fournit des instructions pour la création, la configuration, le démarrage et la suppression d’une passerelle Azure Application Gateway à l’aide d’Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# Créer, démarrer ou supprimer une passerelle Application Gateway à l’aide d’Azure Resource Manager

Une passerelle Application Gateway est un équilibreur de charge de couche 7. Elle assure le basculement, l’exécution des requêtes HTTP de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


Cet article vous guide lors des étapes de création, configuration, démarrage et suppression d'une passerelle Application Gateway.


>[AZURE.IMPORTANT]Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Resource Manager et classique. Veillez à bien comprendre les [modèles et outils de déploiement](azure-classic-rm.md) avant d’utiliser une ressource Azure. Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article. Ce document décrit la création d’une passerelle Application Gateway à l’aide d’Azure Resource Manager. Pour utiliser la version classique, accédez à [Création d’un déploiement classique de passerelle Application Gateway à l’aide de PowerShell](application-gateway-create-gateway.md).



## Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell Azure à l'aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](http://azure.microsoft.com/downloads/).
2. Vous allez créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurerez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?
 

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur** : l’écouteur comporte un port frontal, un protocole (Http ou Https, sensibles à la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle *basic* est la distribution de charge par tourniquet (round robin).


 
## Créer une passerelle Application Gateway

La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.


La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créer une ressource de passerelle Application Gateway


## Créer un groupe de ressources pour Resource Manager

Assurez-vous que vous disposez de la version la plus récente d’Azure PowerShell. Pour plus d’informations, consultez [Utilisation de Windows Powershell avec Azure Resource Manager](powershell-azure-resource-manager.md).

### Étape 1

		Login-AzureRmAccount



### Étape 2 :

Vérifiez les abonnements associés au compte

		Get-AzureRmSubscription 

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3 

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».


>[AZURE.NOTE]Si vous devez configurer une sonde personnalisée pour la passerelle d’application, accédez à l’article [Création d’une passerelle d’application avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-ps.md). Consultez [sondes personnalisées et contrôle d’intégrité](application-gateway-probe-overview.md) pour plus d’informations.



## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

### Étape 1	
	
Attribue la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Étape 2 :	

Crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Étape 3
	
Attribuer une variable de sous-réseau pour les prochaines étapes de création d’une passerelle d’application

	$subnet=$vnet.Subnets[0]

## Créer une adresse IP publique pour la configuration frontale

Créer une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Créer un objet de configuration de passerelle Application Gateway

Avant de créer la passerelle d’application, vous devez configurer tous les éléments d’application de l’application. Les étapes suivantes créent les éléments de configuration nécessaires à une ressource de passerelle d’application

### Étape 1

Crée une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarrera, elle sélectionnera une adresse IP à partir du sous-réseau configuré et acheminera le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilisera une adresse IP unique.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Étape 2

Cette étape va configurer le pool d’adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacerez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Étape 3

Configure les paramètres de passerelle Application Gateway « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Étape 4

Configure le port IP frontal nommé « frontendport01 » dans ce cas précis pour le point de terminaison IP public.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### Étape 5

Crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP publique à cette configuration.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Étape 6

Crée l’écouteur nommé « listener01 » et associe le port frontal à la configuration IP frontale.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Étape 7 

Crée la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Étape 8

Configure la taille d’instance de la passerelle Application Gateway.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Créer une passerelle Application Gateway à l’aide de New-AzureRmApplicationGateway

Crée une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Supprimer une passerelle Application Gateway

Pour supprimer une passerelle Application Gateway, vous devez effectuer les opérations suivantes dans l'ordre :

1. Utilisez l’applet de commande `Stop-AzureRmApplicationGateway` pour arrêter la passerelle. 
2. Utilisez l’applet de commande `Remove-AzureRmApplicationGateway` pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande `Get-AzureRmApplicationGateway`.

### Étape 1

Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw » :
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2
	 
Utilisez `Stop-AzureRmApplicationGateway` pour arrêter la passerelle Application Gateway :

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Une fois la passerelle Application Gateway dans un état arrêté, utilisez l’applet de commande `Remove-AzureRmApplicationGateway` pour supprimer le service.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]Il est possible d’utiliser le commutateur « -force » pour supprimer le message de confirmation.


Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureRmApplicationGateway`. Cette étape n'est pas requise.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	
## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration de la passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->