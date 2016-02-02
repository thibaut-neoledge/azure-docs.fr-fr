<properties
   pageTitle="Configuration d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager | Microsoft Azure"
   description="Cette page fournit des instructions pour la création d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager"
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

# Configuration d’une passerelle Application Gateway pour le déchargement SSL à l’aide d’Azure Resource Manager

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

 Il est possible de configurer une passerelle Azure Application Gateway pour terminer la session SSL (Secure Sockets Layer) au niveau de la passerelle afin d’éviter les tâches de déchiffrement SSL coûteuses au niveau de la batterie de serveurs web. Le déchargement SSL simplifie aussi la configuration de serveur principal et la gestion de l’application web.


## Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell à l’aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](https://azure.microsoft.com/downloads/).
2. Vous allez créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurerez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?


- **Pool de serveurs principaux :** liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou doivent correspondre à une adresse IP/VIP publique.
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres tels que le port, le protocole et une affinité basée sur des cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
- **Règle :** la règle lie l’écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.

**Notes de configuration supplémentaires :**

Pour configurer des certificats SSL, le protocole dans **HttpListener** doit passer à *Https* (sensible à la casse). L’élément **SslCertificate** doit être ajouté à **HttpListener** avec la valeur de variable configurée pour le certificat SSL. Le port du serveur frontal doit être mis à jour sur 443.

**Pour activer l’affinité basée sur les cookies** : une passerelle Application Gateway peut être configurée pour garantir qu’une requête d’une session client est toujours dirigée vers la même machine virtuelle dans la batterie de serveurs web. Ceci se fait par l’injection d’un cookie de session qui permet à la passerelle de diriger le trafic de manière appropriée. Pour activer l’affinité basée sur les cookies, définissez **CookieBasedAffinity** sur *Activé* dans l’élément **BackendHttpSettings**.


## Créer une passerelle Application Gateway

La différence entre l’utilisation du modèle de déploiement Azure Classic et celle d’Azure Resource Manager réside dans l’ordre de création de la passerelle Application Gateway et des éléments à configurer.

Avec Resource Manager, tous les éléments constitutifs d’une passerelle Application Gateway sont configurés individuellement, puis regroupés pour créer une ressource Application Gateway.


La procédure de création d’une passerelle Application Gateway comporte les étapes suivantes :

1. Créer un groupe de ressources pour Resource Manager
2. Créer un réseau virtuel, un sous-réseau et une adresse IP publique pour la passerelle Application Gateway
3. Créer un objet de configuration de passerelle Application Gateway
4. Créer une ressource Application Gateway


## Créer un groupe de ressources pour Resource Manager

Veillez à passer en mode PowerShell pour utiliser les applets de commande d’Azure Resource Manager. Pour plus d’informations, voir l’article [Utilisation de Windows Powershell avec Azure Resource Manager](powershell-azure-resource-manager.md).

### Étape 1 :

		PS C:\> Login-AzureRmAccount



### Étape 2 :

Vérifiez les abonnements associés au compte.

		PS C:\> get-AzureRmSubscription

Vous devez indiquer vos informations d’identification.<BR>

### Étape 3

Parmi vos abonnements Azure, choisissez celui que vous souhaitez utiliser.<BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Étape 4

Créez un groupe de ressources (ignorez cette étape si vous utilisez un groupe de ressources existant)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Azure Resource Manager requiert que tous les groupes de ressources spécifient un emplacement. Ce dernier est utilisé comme emplacement par défaut des ressources de ce groupe. Assurez-vous que toutes les commandes pour la création d'une passerelle Application Gateway utiliseront le même groupe de ressources.

Dans l'exemple ci-dessus, nous avons créé un groupe de ressources appelé « appgw-RG », ainsi que l'emplacement « West US ».

## Créer un réseau virtuel et un sous-réseau pour la passerelle Application Gateway

L’exemple ci-après indique comment créer un réseau virtuel à l’aide de Resource Manager :

### Étape 1 :

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Attribue la plage d’adresses 10.0.0.0/24 à une variable subnet à utiliser pour créer un réseau virtuel.

### Étape 2 :
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crée un réseau virtuel nommé « appgwvnet » dans le groupe de ressources « appw-rg » pour la région « West US » à l’aide du préfixe 10.0.0.0/16 avec le sous-réseau 10.0.0.0/24.

### Étape 3

	$subnet=$vnet.Subnets[0]

Assigne l’objet de sous-réseau à la variable $subnet pour les étapes suivantes.

## Créer une adresse IP publique pour la configuration frontale

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crée une ressource IP publique « publicIP01 » dans le groupe de ressources « appw-rg » pour la région « West US ».


## Créer un objet de configuration de passerelle Application Gateway

### Étape 1 :

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crée une configuration IP de passerelle Application Gateway nommée « gatewayIP01 ». Lorsque la passerelle Application Gateway démarrera, elle sélectionnera une adresse IP à partir du sous-réseau configuré et acheminera le trafic réseau vers les adresses IP du pool IP principal. Gardez à l'esprit que chaque instance utilisera une adresse IP unique.

### Étape 2 :

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Configure le pool d’adresses IP principal nommé « pool01 » avec les adresses IP « 134.170.185.46, 134.170.188.221, 134.170.185.50 ». Il s’agit des adresses IP qui recevront le trafic réseau provenant du point de terminaison IP frontal. Remplacer les adresses IP de l’exemple ci-dessus par les adresses IP des points de terminaison de votre application web.

### Étape 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Configure le paramètre de passerelle Application Gateway « poolsetting01 » pour le trafic réseau à charge équilibrée dans le pool principal.

### Étape 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Configure le port IP frontal nommé « frontendport01 » pour le point de terminaison IP public.

### Étape 5

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Configure le certificat utilisé pour la connexion SSL. Le certificat doit être au format .pfx et le mot de passe contenir entre 4 et 12 caractères.

### Étape 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crée la configuration IP frontale nommée « fipconfig01 » et associe l’adresse IP publique à cette configuration.

### Étape 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Crée l’écouteur nommé « listener01 » et associe le port frontal à la configuration IP et au certificat du serveur frontal.

### Étape 8

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crée la règle d’acheminement d’équilibrage de charge nommée « rule01 » qui configure le comportement d’équilibrage de charge.

### Étape 9

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configure la taille d’instance de la passerelle Application Gateway.

>[AZURE.NOTE]  La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir entre Standard\_Small, Standard\_Medium et Standard\_Large.

## Créer une passerelle Application Gateway avec New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Crée une passerelle Application Gateway avec tous les éléments de configuration à partir de la procédure ci-dessus. Dans notre exemple, la passerelle Application Gateway est appelée « appgwtest ».

## Étapes suivantes

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne (ILB), consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0128_2016-->