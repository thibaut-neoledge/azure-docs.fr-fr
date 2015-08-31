<properties
   pageTitle="Création, démarrage ou suppression d'une passerelle Application Gateway | Microsoft Azure"
	description="Créez, configurez, démarrez et supprimez une passerelle Application Gateway Azure"
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
	ms.date="06/30/2015"
	ms.author="joaoma"/>

# Création, démarrage ou suppression d'une passerelle Application Gateway

Dans cette version, vous pouvez créer une passerelle Application Gateway à l'aide d'appels d'API REST ou PowerShell. La prise en charge du portail Azure et de l’interface de ligne de commande sera fournie dans une prochaine version. Cet article vous guide lors des étapes de création, configuration, démarrage et suppression d’une passerelle Application Gateway.

## Avant de commencer

1. Installez la dernière version des applets de commande Azure PowerShell utilisant Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](http://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide.
3. Vérifiez que vous disposez de serveurs principaux dans le réseau virtuel ou avec une adresse IP/VIP affectée.


Pour créer une passerelle Application Gateway, exécutez les étapes suivantes dans l’ordre indiqué.

1. [Créer une passerelle Application Gateway](#create-a-new-application-gateway)
2. [Configurer la passerelle](#configure-the-gateway)
3. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
4. [Démarrer la passerelle](#start-the-gateway)
4. [Vérifier l'état de la passerelle](#verify-the-gateway-status)

Si vous souhaitez supprimer une passerelle Application Gateway, consultez [Supprimer une passerelle Application Gateway](#delete-an-application-gateway).

## Créer une passerelle Application Gateway

Pour créer la passerelle, utilisez l’applet de commande `New-AzureApplicationGateway` en remplaçant les valeurs par les vôtres. Notez que la facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

L’exemple suivant montre l’applet de commande sur la première ligne, suivie de la sortie.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

Pour valider la création de la passerelle, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`.

Dans l’exemple, *Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Les autres valeurs disponibles sont Small et Large. *Vip* et *DnsName* sont vides, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :


## Configurer la passerelle

La configuration d’une passerelle Application Gateway se compose de plusieurs valeurs. Les valeurs peuvent être liées ensemble pour construire la configuration.

Les valeurs sont :

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique.
- **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l'un des serveurs principaux.
- **Écouteur :** l’écouteur a un port frontal, un protocole (Http ou Https, avec respect de la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL).
- **Règle** : la règle lie l’écouteur et le pool de serveurs principaux et définit le pool de serveurs principaux vers lequel le trafic doit être dirigé lorsqu’il atteint un écouteur spécifique. Actuellement, seule la règle *basic* est prise en charge. La règle *basic* est la distribution de charge par tourniquet (round robin).

Vous pouvez construire votre configuration en créant un objet de configuration ou en utilisant un fichier XML de configuration. Pour construire votre configuration à l’aide d’un fichier XML de configuration, utilisez l’exemple suivant.

 **Exemple de configuration XML**

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

## Définir la configuration de la passerelle

Ensuite, vous allez définir la passerelle Application Gateway. Vous pouvez utiliser l’applet de commande `Set-AzureApplicationGatewayConfig` avec un objet de configuration ou avec un fichier XML de configuration.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Démarrer la passerelle

Une fois la passerelle configurée, utilisez l’applet de commande `Start-AzureApplicationGateway` pour la démarrer. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


> [AZURE.NOTE]L’exécution de l’applet de commande `Start-AzureApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.



	PS C:\> Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Vérifier l'état de la passerelle

Utilisez l’applet de commande `Get-AzureApplicationGateway` pour vérifier l’état de la passerelle. Si *Start-AzureApplicationGateway* a réussi à l’étape précédente, l’état doit être *En cours d’exécution* et Vip et DnsName doivent avoir des entrées valides.

L’exemple suivant montre une passerelle Application Gateway en cours d’exécution et prête à prendre le trafic destiné à `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway AppGwTest

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

	PS C:\> Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Une fois la passerelle Application Gateway dans un état arrêté, utilisez l’applet de commande `Remove-AzureApplicationGateway` pour supprimer le service.


	PS C:\> Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Pour vérifier que le service a été supprimé, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`. Cette étape n'est pas requise.


	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Étapes suivantes

Si vous souhaitez configurer le déchargement SSL, consultez [Configuration de la passerelle Application Gateway pour le déchargement SSL](application-gateway-ssl.md).

Si vous souhaitez configurer une passerelle Application Gateway à utiliser avec l’équilibreur de charge interne, consultez [Création d’une passerelle Application Gateway avec un équilibrage de charge interne (ILB)](application-gateway-ilb.md).

Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO8-->