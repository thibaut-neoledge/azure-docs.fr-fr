<properties 
   pageTitle="Créer et configurer une passerelle Application Gateway avec un équilibrage de charge interne (ILB) à l’aide d’Azure Resource Manager | Microsoft Azure"
   description="Cette page fournit des instructions pour la création, la configuration, le démarrage et la suppression d’une passerelle Application Gateway Azure avec un équilibrage de charge interne (ILB) pour Azure Resource Manager"
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


# Créer une passerelle Application Gateway avec un équilibrage de charge interne (ILB) à l’aide d’Azure Resource Manager

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)

Vous pouvez configurer une passerelle Application Gateway avec une adresse IP virtuelle côté Internet ou avec un point de terminaison interne non exposé à Internet, également appelé point de terminaison d'équilibrage de charge interne (ILB). La configuration de la passerelle avec un équilibrage de charge interne est utile pour les applications métier internes non exposées à Internet. C'est également utile pour les services/niveaux au sein d'une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais la distribution de charge par tourniquet, l'adhérence de session ou la terminaison SSL sont tout de mêmes requises. Cet article vous guidera au cours des étapes de configuration d'une passerelle Application Gateway avec un équilibrage de charge interne.

## Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell Azure à l'aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](http://azure.microsoft.com/downloads/).
2. Vous allez créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurerez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?
 

- **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux :** chaque pool a des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.


 
## Créer une passerelle Application Gateway

La différence entre l’utilisation d’Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer. Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer la ressource Application Gateway.


La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créer une ressource de passerelle Application Gateway


## Créer un groupe de ressources pour Resource Manager

Veillez à passer en mode PowerShell pour utiliser les applets de commande ARM. Pour plus d’informations, voir l’article [Utilisation de Windows Powershell avec Azure Resource Manager](powershell-azure-resource-manager.md).

### Étape 1

		PS C:\> Login-AzureRmAccount

### Étape 2 :

Vérifiez les abonnements associés au compte

		PS C:\> get-AzureRmSubscription 

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3 

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d’une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l’exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-rg », ainsi que l’emplacement « West US ».

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

### Étape 1	
	
	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Attribue la plage d’adresses 10.0.0.0/24 à la variable subnet à utiliser pour créer un réseau virtuel.

### Étape 2
	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appw-rg » pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.
	
### Étape 3

	$subnet=$vnet.subnets[0]

Assigne l'objet de sous-réseau à la variable $subnet pour les étapes suivantes.
 

## Créer un objet de configuration de passerelle Application Gateway

### Étape 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crée une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarrera, elle sélectionnera une adresse IP à partir du sous-réseau configuré et acheminera le trafic réseau vers les adresses IP du pool IP principal. Gardez à l’esprit que chaque instance utilisera une adresse IP unique.
 
### Étape 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.0.10,10.0.0.11,10.0.0.12

Cette étape va configurer le pool d’adresses IP principal nommé « pool01 » avec les adresses IP « 10.0.0.10, 10.0.0.11, 10.0.0.12 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Vous remplacerez les adresses IP ci-dessus afin d’ajouter vos propres points de terminaison d’adresse IP d’application.

### Étape 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configure les paramètres de passerelle Application Gateway « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

### Étape 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configure le port d’adresses IP frontal nommé « frontendport01 » pour l’équilibrage de charge interne.

### Étape 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Crée la configuration IP frontale nommée « fipconfig01 » et associe une adresse IP privée à partir du sous-réseau du réseau virtuel actuel.

### Étape 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crée l’écouteur nommé « listener01 » et associe le port frontal à la configuration IP frontale.

### Étape 7 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crée la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

### Étape 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configure la taille d’instance de la passerelle Application Gateway.

>[AZURE.NOTE]La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Créer une passerelle Application Gateway avec New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crée une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».



## Démarrer la passerelle

Une fois la passerelle configurée, utilisez l’applet de commande `Start-AzureRmApplicationGateway` pour démarrer la passerelle. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


**Remarque :** l’exécution de l’applet de commande `Start-AzureRmApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.

Dans l’exemple ci-dessous, la passerelle Application Gateway est appelée « appgwtest », et le groupe de ressources est nommé « appgw-rg » :


### Étape 1

Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw » :
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2
	 
Utilisez `Start-AzureRmApplicationGateway` pour démarrer la passerelle Application Gateway :

	PS C:\> Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureRmApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Vérifier l’état de la passerelle Application Gateway

Utilisez l’applet de commande `Get-AzureRmApplicationGateway` pour vérifier l’état de la passerelle. Si *Start-AzureApplicationGateway* a bien été exécuté à l’étape précédente, l’état doit être *en cours d’exécution*.


## Supprimer une passerelle Application Gateway

Pour supprimer une passerelle Application Gateway, vous devez effectuer les opérations suivantes dans l'ordre :

1. Utilisez l’applet de commande `Stop-AzureRmApplicationGateway` pour arrêter la passerelle. 
2. Utilisez l’applet de commande `Remove-AzureRmApplicationGateway` pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande `Get-AzureApplicationGateway`.

Cet exemple montre l'applet de commande `Stop-AzureRmApplicationGateway` sur la première ligne, suivie de la sortie.

### Étape 1

Obtenez l’objet de passerelle Application Gateway et associez-le à une variable « $getgw » :
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Étape 2
	 
Utilisez `Stop-AzureRmApplicationGateway` pour arrêter la passerelle Application Gateway :

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois la passerelle Application Gateway arrêtée, utilisez l’applet de commande `Remove-AzureRmApplicationGateway` pour supprimer le service.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]Il est possible d’utiliser le commutateur « -force » pour supprimer le message de confirmation.
>

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureRmApplicationGateway`. Cette étape n'est pas requise.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration de la passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1203_2015-->