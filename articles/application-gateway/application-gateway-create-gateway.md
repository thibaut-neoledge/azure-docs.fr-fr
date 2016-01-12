<properties
   pageTitle="Création, démarrage ou suppression d'une passerelle Application Gateway | Microsoft Azure"
   description="Cette page fournit des instructions pour créer, configurer, démarrer et supprimer une passerelle Application Gateway Azure"
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
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# Création, démarrage ou suppression d'une passerelle Application Gateway

Une passerelle Application Gateway est un équilibreur de charge de couche 7. Elle assure le basculement, l’exécution des requêtes HTTP de routage des performances entre serveurs locaux ou dans le cloud. Une passerelle Application Gateway offre les fonctionnalités de livraison d’applications suivantes : équilibrage de charge HTTP, affinité de session basée sur les cookies et déchargement SSL.

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template](application-gateway-create-gateway-arm-template.md)


<BR>

Cet article vous guide lors des étapes de création, configuration, démarrage et suppression d’une passerelle Application Gateway.


## Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell utilisant Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [Page de téléchargement](http://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide. Assurez-vous qu’aucun ordinateur virtuel ou déploiement cloud n’utilise le sous-réseau. La passerelle Application Gateway doit être seule sur un sous-réseau virtuel.
3. Les serveurs que vous configurerez pour utiliser la passerelle Application Gateway doivent exister ou vous devez créer leurs points de terminaison sur le réseau virtuel ou avec une adresse IP/VIP publique affectée.

## Quels sont les éléments nécessaires pour créer une passerelle Application Gateway ?


Quand vous utilisez la commande **New-AzureApplicationGateway** pour créer la passerelle Application Gateway, aucune configuration n’est définie à ce stade et vous devrez configurer la ressource nouvellement créée à l’aide de XML ou de l’objet de configuration.


Les valeurs sont :

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique.
- **Paramètres du pool de serveurs principaux :** chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l’un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
- **Règle** : la règle lie l'écouteur et le pool de serveurs principaux et définit le pool de serveurs principaux vers lequel le trafic doit être dirigé quand il atteint un écouteur spécifique. Actuellement, seule la règle *de base* est prise en charge. La règle *basic* est la distribution de charge par tourniquet (round robin).



## Créer une passerelle Application Gateway

Vous devez suivre un ordre d’étapes spécifique pour créer une passerelle Application Gateway :

1. Créer une ressource de passerelle Application Gateway
2. Créer le fichier XML de configuration ou l’objet de configuration
3. Valider la configuration de la ressource Application Gateway nouvellement créée.

>[AZURE.NOTE] Si vous devez configurer une sonde personnalisée pour la passerelle d’application, accédez à l’article [Création d’une passerelle d’application avec des sondes personnalisées à l’aide de PowerShell](application-gateway-create-probe-classic-ps.md). Consultez [sondes personnalisées et contrôle d’intégrité](application-gateway-probe-overview.md) pour plus d’informations.


### Créer une ressource de passerelle Application Gateway

Pour créer la passerelle, utilisez l’applet de commande `New-AzureApplicationGateway` en remplaçant les valeurs par les vôtres. Notez que la facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

L’exemple suivant illustre la création d’une passerelle Application Gateway à l’aide d’un réseau virtuel appelé « testvnet1 » et d’un sous-réseau appelé « subnet-1 » :


	New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs.


**Pour valider** la création de la passerelle, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`.




	Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]La valeur par défaut du paramètre *InstanceCount* est 2, et la valeur maximale est 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Vous pouvez choisir Small, Medium ou Large.


 Les paramètres *Vip* et *DnsName* sont sans valeur, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.

## Configurer la passerelle Application Gateway

Vous pouvez configurer la passerelle Application Gateway à l’aide d’un objet de configuration ou de XML

## Configurer la passerelle Application Gateway à l’aide de XML

Dans l’exemple ci-dessous, vous allez utiliser un fichier XML pour configurer tous les paramètres de la passerelle Application Gateway et les valider dans la ressource Application Gateway.

### Étape 1  

Copiez le texte suivant dans le Bloc-notes.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Modifiez les valeurs entre parenthèses pour les éléments de configuration. Enregistrez le fichier avec l’extension .xml.

>[AZURE.IMPORTANT] L’élément de protocole Http ou Https respecte la casse.

L’exemple suivant montre comment utiliser un fichier de configuration pour configurer une passerelle Application Gateway pour équilibrer la charge du trafic Http sur le port public 80 et envoyer le trafic réseau au port 80 principal entre deux adresses IP.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>


### Étape 2

Ensuite, définissez la passerelle Application Gateway. Vous allez utiliser l’applet de commande `Set-AzureApplicationGatewayConfig` avec un fichier XML de configuration.


	Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configurer la passerelle Application Gateway à l’aide d’un objet de configuration

L’exemple suivant montre comment configurer la passerelle Application Gateway à l’aide d’objets de configuration. Vous devez configurer tous les éléments de configuration individuellement, puis les ajouter à un objet de configuration de passerelle Application Gateway. Après avoir créé l’objet de configuration, vous utilisez la commande `Set-AzureApplicationGateway` pour valider la configuration dans la ressource Application Gateway créée précédemment.

>[AZURE.NOTE]Avant d’affecter une valeur à chaque objet de configuration, vous devez déclarer le type d’objet dans lequel PowerShell le stockera. La première ligne de création de chaque élément définit le Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(object name) qui sera utilisé.

### Étape 1

Créez tous les éléments de configuration.

Créez une IP frontale comme indiqué dans l'exemple suivant.

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Créez un port frontal comme indiqué dans l'exemple suivant.

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

Créez un pool de serveurs principaux

 Définissez les adresses IP qui seront ajoutées au pool de serveurs principal, comme indiqué dans l’exmple suivant.


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 À l’aide de l’objet $server, ajoutez les valeurs à l’objet de pool principal ($pool)

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Créez le paramètre de pool de serveurs principal

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Créez l’écouteur

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Créez la règle

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Étape 2

Affectez tous les éléments de configuration à un objet de configuration Application Gateway ($appgwconfig) :

Ajoutez une adresse IP frontale à la configuration

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Ajoutez un port frontal à la configuration

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Ajoutez un pool de serveurs principal à la configuration

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Ajoutez le paramètre de pool principal à la configuration

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Ajoutez un écouteur à la configuration

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Ajoutez une règle à la configuration

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Étape 3

Validez l’objet de configuration dans la ressource Application Gateway à l’aide de `Set-AzureApplicationGatewayConfig`.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Démarrer la passerelle

Une fois la passerelle configurée, utilisez l’applet de commande `Start-AzureApplicationGateway` pour la démarrer. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


> [AZURE.NOTE] L’exécution de l’applet de commande `Start-AzureApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.



	Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Vérifier l'état de la passerelle

Utilisez l’applet de commande `Get-AzureApplicationGateway` pour vérifier l’état de la passerelle. Si l’applet de commande *Start-AzureApplicationGateway* a réussi à l’étape précédente, la passerelle doit présenter l’état *Running*, et les champs Vip et DnsName doivent comporter des entrées valides.

L’exemple suivant montre une passerelle Application Gateway en cours d’exécution et prête à prendre le trafic destiné à `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	Vip           : 138.91.170.26
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Supprimer une passerelle Application Gateway

Pour supprimer une passerelle Application Gateway :

1. Utilisez l’applet de commande `Stop-AzureApplicationGateway` pour arrêter la passerelle.
2. Utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer la passerelle.
3. Vérifiez que la passerelle a été supprimée à l’aide de l’applet de commande `Get-AzureApplicationGateway`.

L’exemple suivant montre l’applet de commande `Stop-AzureApplicationGateway` sur la première ligne, suivie de la sortie.

	Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois la passerelle Application Gateway arrêtée, utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer le service.


	Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`. Cette étape n'est pas requise.


	Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration de la passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->