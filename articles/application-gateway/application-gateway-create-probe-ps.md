<properties
   pageTitle="Création d'une sonde personnalisée pour la passerelle Application Gateway avec PowerShell dans Resource Manager | Microsoft Azure"
   description="Apprenez à créer une sonde personnalisée pour la passerelle Application Gateway à l'aide de PowerShell dans Resource Manager"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
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
   ms.date="09/06/2016"
   ms.author="gwallace" />

# Création d'une sonde personnalisée pour Azure Application Gateway avec PowerShell pour Azure Resource Manager

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-probe-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Étape 1 :

Utilisez la connexion AzureRmAccount pour vous authentifier.

	Login-AzureRmAccount

### Étape 2 :

Vérifiez les abonnements associés au compte.

	Get-AzureRmSubscription

### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Celui-ci est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l’emplacement « West US ».

## Création d'un réseau virtuel et d'un sous-réseau pour la passerelle Application Gateway

Les étapes suivantes créent un réseau virtuel et un sous-réseau pour la passerelle d'application.

### Étape 1 :


Attribuez la plage d'adresses 10.0.0.0/24 à une variable de sous-réseau à utiliser pour créer un réseau virtuel.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### Étape 2 :

Créez un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appwrg » pour la région « West US » à l'aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Étape 3

Attribuez une variable de sous-réseau pour les prochaines étapes de création d'une passerelle d'application.

	$subnet = $vnet.Subnets[0]

## Création d'une adresse IP publique pour la configuration frontale


Créez une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Création d'un objet de configuration de passerelle Application Gateway avec une sonde personnalisée

Avant de créer la passerelle Application Gateway, vous devez installer tous les éléments de configuration. Les étapes suivantes permettent de créer les éléments de configuration nécessaires à une ressource Application Gateway.

### Étape 1 :

Créez une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarre, elle sélectionne une adresse IP à partir du sous-réseau configuré et achemine le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilise une adresse IP unique.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Étape 2 :


Configurez le pool d'adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Ces valeurs correspondent aux adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Étape 3


La sonde personnalisée est configurée pendant cette opération.

Les paramètres utilisés sont :

- **Intervalle** : configure les vérifications d’intervalle de sonde en secondes.
- **Délai d’expiration** : définit le délai d’expiration d’un contrôle de réponse HTTP.
- **-Nom d’hôte et chemin d’accès** : chemin d’accès complet à l’URL qui est appelé par la passerelle Application Gateway pour déterminer l’état de l’instance. Par exemple : avec un site web http://contoso.com/, la sonde personnalisée peut être configurée pour « http://contoso.com/path/custompath.htm » afin que les contrôles de sonde renvoient une réponse HTTP réussie.
- **Seuil de défaillance sur le plan de l’intégrité** : le nombre d’échecs de réponses HTTP nécessaires pour marquer l’instance de serveur principal comme *défectueuse*.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### Étape 4

Configurez le paramètre de passerelle Application Gateway « poolsetting01 » pour le trafic du pool principal. Cette étape comporte également une configuration du délai d'expiration de la réponse du pool principal à une requête de passerelle Application Gateway. Lorsqu'une réponse du serveur principal atteint une limite d'expiration, la passerelle Application Gateway annule la demande. Cette valeur est différente du délai d’expiration d’analyse qui s’applique uniquement à la réponse du serveur principal aux contrôles d’analyse.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### Étape 5

Configurez le port IP frontal nommé « frontendport01 » pour le point de terminaison IP public.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Étape 6

Créez la configuration IP frontale nommée « fipconfig01 » et associez l'adresse IP publique à cette configuration.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Étape 7

Créez l'écouteur nommé « listener01 » et associez le port frontal à la configuration IP frontale.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Étape 8

Créez la règle d'acheminement d'équilibrage de charge nommée « rule01 » qui configure le comportement d'équilibrage de charge.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Étape 9

Configurez la taille d'instance de la passerelle Application Gateway.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Création d'une passerelle Application Gateway avec New-AzureRmApplicationGateway

Créez une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Ajoute une sonde à une passerelle d’application existante

Quatre étapes permettent d’ajouter une sonde personnalisée à une passerelle d’application existante.

### Étape 1 :

Charger la ressource de passerelle Application Gateway dans une variable PowerShell avec **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2 :

Ajouter une sonde à la configuration de passerelle existante.

	$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Dans l’exemple, la sonde personnalisée est configurée pour vérifier l’URL du chemin d’accès contoso.com/path/custompath.htm toutes les 30 secondes. Un seuil de délai d'expiration de 120 secondes est configuré avec un maximum de 8 demandes d'analyse ayant échoué.

### Étape 3

Ajouter la sonde à la configuration du paramètre du pool du serveur principal et le délai d'attente à l’aide de **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Étape 4

Enregistrer la configuration dans la passerelle Application Gateway à l’aide de **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## Supprimer une sonde d’une passerelle d’application existante

Voici les opérations qui permettent de supprimer une sonde personnalisée d’une passerelle application.

### Étape 1 :

Charger la ressource de passerelle Application Gateway dans une variable PowerShell avec **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Étape 2 :

Supprimer la configuration de sonde de la passerelle Application Gateway à l’aide de **Remove-AzureRmApplicationGatewayProbeConfig**.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Étape 3

Mettre à jour le paramètre de pool du serveur principal pour supprimer la sonde et le délai d'attente à l'aide de **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Étape 4

Enregistrer la configuration dans la passerelle Application Gateway à l’aide de **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## Étapes suivantes

Apprenez à configurer le déchargement SSL en consultant [Configurer le déchargement SSL](application-gateway-ssl-arm.md).

<!---HONumber=AcomDC_0907_2016-->