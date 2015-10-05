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
   ms.date="09/21/2015"
   ms.author="joaoma"/>


# Créer, démarrer ou supprimer une passerelle Application Gateway à l’aide d’Azure Resource Manager

Une passerelle Application Gateway est une solution d’équilibrage de charge de couche 7. Elle assure le basculement, l’exécution des requêtes HTTP de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL.


> [AZURE.SELECTOR]
- [Azure Classic Powershell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template steps](application-gateway-create-gateway-arm-template.md)


<BR>


Cet article vous guide lors des étapes de création, configuration, démarrage et suppression d'une passerelle Application Gateway.


>[AZURE.IMPORTANT]Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Resource Manager et classique. Avant d’utiliser une ressource Azure, veillez à bien comprendre les [Modèles et outils de déploiement](azure-classic-rm.md). Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article. Ce document décrit la création d’une passerelle Application Gateway à l’aide d’Azure Resource Manager. Pour utiliser la version classique, accédez à [Création d’un déploiement classique de passerelle Application Gateway à l’aide de PowerShell](application-gateway-create-gateway.md).



## Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell Azure à l'aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [Page de téléchargement](http://azure.microsoft.com/downloads/).
2. Vous allez créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurerez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?
 

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle** : la règle lie l’écouteur et le pool de serveurs principaux, et définit le pool de serveurs principaux vers lequel le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle *de base* est la distribution de charge par tourniquet (round robin).


 
## Créer une passerelle Application Gateway

La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.


La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créer une ressource de passerelle Application Gateway


## Créer un groupe de ressources pour Resource Manager

Veillez à passer en mode PowerShell pour utiliser les applets de commande ARM. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec le Gestionnaire des ressources](powershell-azure-resource-manager.md).

### Étape 1

    Switch-AzureMode -Name AzureResourceManager

### Étape 2

Connectez-vous à votre compte Azure.


    Add-AzureAccount

Vous devez indiquer vos informations d’identification.


### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.

    Select-AzureSubscription -SubscriptionName "MySubscription"

Pour afficher la liste des abonnements disponibles, utilisez l'applet de commande « Get-AzureSubscription ».


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

### Étape 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Attribue la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

### Étape 2	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appw-rg » pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.
	
## Créer une adresse IP publique pour la configuration frontale

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crée une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».


## Créer un objet de configuration de passerelle Application Gateway

### Étape 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crée une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarrera, elle sélectionnera une adresse IP à partir du sous-réseau configuré et acheminera le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilisera une adresse IP unique.
 
### Étape 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Cette étape va configurer le pool d’adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacerez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

### Étape 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configure les paramètres de passerelle Application Gateway « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

### Étape 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configure le port IP frontal nommé « frontendport01 » dans ce cas précis pour le point de terminaison IP public.

### Étape 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP publique à cette configuration.

### Étape 6

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crée l’écouteur nommé « listener01 » et associe le port frontal à la configuration IP frontale.

### Étape 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crée la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

### Étape 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configure la taille d’instance de la passerelle Application Gateway.

>[AZURE.NOTE]La valeur par défaut du paramètre *InstanceCount* est 2, et la valeur maximale est 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Créer une passerelle Application Gateway avec New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crée une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».


## Démarrer la passerelle Application Gateway

Une fois la passerelle configurée, utilisez l’applet de commande `Start-AzureApplicationGateway` pour la démarrer. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


**Remarque :** l’exécution de l’applet de commande `Start-AzureApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.

Dans l’exemple ci-dessous, la passerelle Application Gateway est appelée « appgwtest », et le groupe de ressources est nommé « app-rg » :


### Étape 1

Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw » :
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Étape 2
	 
Utilisez `Start-AzureApplicationGateway` pour démarrer la passerelle Application Gateway :

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## Vérifier l’état de la passerelle Application Gateway

Utilisez l’applet de commande `Get-AzureApplicationGateway` pour vérifier l’état de la passerelle. Si l’exécution de l’applet de commande *Start-AzureApplicationGateway* a réussi à l’étape précédente, l’état doit être *En cours d’exécution* et Vip et DnsName doivent contenir des entrées valides.

Cet exemple présente une passerelle Application Gateway en cours d’exécution et prête à prendre en charge le trafic destiné à `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
		Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## Supprimer une passerelle Application Gateway

Pour supprimer une passerelle Application Gateway, vous devez effectuer les opérations suivantes dans l'ordre :

1. Utilisez l’applet de commande `Stop-AzureApplicationGateway` pour arrêter la passerelle. 
2. Utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande `Get-AzureApplicationGateway`.


### Étape 1

Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw » :
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2
	 
Utilisez `Stop-AzureApplicationGateway` pour arrêter la passerelle Application Gateway :

	Stop-AzureApplicationGateway -ApplicationGateway $getgw  


Une fois la passerelle Application Gateway arrêtée, utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer le service.


	Remove-AzureApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]Il est possible d’utiliser le commutateur « -force » pour supprimer le message de confirmation.
>

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`. Cette étape n'est pas requise.


	Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	


## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, voir [Configuration d’une passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibrage de charge interne, voir [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Sept15_HO4-->