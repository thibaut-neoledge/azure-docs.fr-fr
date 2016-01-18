<properties 
   pageTitle="Créer une sonde personnalisée pour la passerelle d’Application en utilisant PowerShell dans le Gestionnaire de ressources | Microsoft Azure"
   description="Apprendre à créer une sonde personnalisée pour la passerelle d’application à l’aide de PowerShell dans le Gestionnaire de ressources"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Créer une sonde personnalisée pour Application Gateway avec PowerShell pour Azure Resource Manager

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Étape 1 

Utilisez la connexion AzureRmAccount pour vous authentifier

	Login-AzureRmAccount

### Étape 2

Vérifiez les abonnements associés au compte

		get-AzureRmSubscription 

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3 

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

Les étapes suivantes créent un réseau virtuel et un sous-réseau pour la passerelle d’application

### Étape 1	
	

Attribue la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Étape 2	

Crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Étape 3

Attribue une variable de sous-réseau pour les prochaines étapes de création d’une passerelle d’application
		
	$subnet=$vnet.Subnets[0]

## Créer une adresse IP publique pour la configuration frontale


Crée une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
 

## Créer un objet de configuration de passerelle Application Gateway avec sonde personnalisée 

Avant de créer la passerelle d’application de l’application, vous devez installer tous les éléments de configuration. Les étapes suivantes créent les éléments de configuration nécessaires à une ressource de passerelle d’application


### Étape 1

Crée une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarrera, elle sélectionnera une adresse IP à partir du sous-réseau configuré et acheminera le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilisera une adresse IP unique.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Étape 2


Cette étape va configurer le pool d’adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacerez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Étape 3


La sonde personnalisée est configurée pendant cette opération.

Les paramètres utilisés sont :

- **-Intervalle** : configure les vérifications d’intervalle de sonde en secondes 
- **-Délai d’expiration** : définit le délai d’expiration d’un contrôle de réponse HTTP
- **-Nom d’hôte et -chemin d’accès** : chemin d’accès complet à l’URL appelé par Application Gateway pour déterminer l’état de l’instance. Par exemple : voici un site web http://contoso.com/. la sonde personnalisée peut être configurée pour « http://contoso.com/path/custompath.htm » afin que les contrôles de sonde renvoient une réponse HTTP réussie. 
- **-Seuil de défaillance sur le plan de l’intégrité** : le nombre d’échecs de réponses HTTP nécessaires pour marquer l’instance de serveur principal comme *défectueuse*



	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Étape 4

Configure les paramètres de passerelle d’application « poolsetting01 » du trafic du pool principal. Cette étape comporte également une configuration du délai d’expiration de la réponse du pool principal à une requête de passerelle d’application. Lorsqu’une réponse du serveur principal atteint une limite d’expiration, la passerelle d’application annule la demande. Cette réponse est différente du délai d’expiration d’analyse qui est simplement une réponse à du serveur principal des contrôles d’analyse.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Étape 5

Configure le port IP frontal nommé « frontendport01 » dans ce cas précis pour le point de terminaison IP public.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Étape 6

Crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP publique à cette configuration.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Étape 7

Crée l’écouteur nommé « listener01 » et associe le port frontal à la configuration IP frontale.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Étape 8 

Crée la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Étape 9

Configure la taille d’instance de la passerelle Application Gateway.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Crée une passerelle Application Gateway avec New-AzureRmApplicationGateway

Crée une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Ajoute une sonde à une passerelle d’application existante

Quatre étapes permettent d’ajouter une sonde personnalisée à une passerelle d’application existante.

### Étape 1 

Charger la ressource de passerelle d’application dans une variable PowerShell avec `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2

Ajouter une sonde à la configuration de passerelle existante.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

   
Dans l’exemple, la sonde personnalisée est configurée pour vérifier l’URL du chemin d’accès contoso.com/path/custompath.htm toutes les 30 secondes. Un seuil de délai d’expiration de 120 secondes est configuré avec un maximum de 8 demandes d’analyse ayant échoué.

### Étape 3

Ajouter la sonde au pool principal en définissant la configuration et le délai d’expiration avec `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Étape 4

Enregistrer la configuration sur la passerelle d’application avec `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Supprimer une sonde d’une passerelle d’application existante

Voici les opérations qui permettent de supprimer une sonde personnalisée d’une passerelle application.

### Étape 1 

Charger la ressource de passerelle d’application dans une variable PowerShell avec `Get-AzureRmApplicationGateway`

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Étape 2 

Supprimer la configuration de sonde de la passerelle d’application avec `Remove-AzureRmApplicationGatewayProbeConfig`

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Étape 3 

Mettre à jour la configuration du pool principal pour supprimer la sonde et le paramètre de délai en utilisant `-Set-AzureRmApplicationGatewayBackendHttpSettings`


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Étape 4
	
Enregistrer la configuration sur la passerelle d’application avec `Set-AzureRmApplicationGateway`

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0107_2016-->