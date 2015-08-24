<properties 
   pageTitle="Création et configuration d'une passerelle Application Gateway avec équilibrage de charge interne (ILB) dans un réseau virtuel | Microsoft Azure"
   description="Cette page fournit des instructions pour configurer une passerelle Application Gateway Azure avec un point de terminaison d'équilibrage de charge interne"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/30/2015"
   ms.author="joaoma"/>

# Création d'une passerelle Application Gateway avec un équilibrage de charge interne (ILB)

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-ilb.md)
- [Resource Manager Powershell steps](application-gateway-ilb-arm.md)


Vous pouvez configurer une passerelle Application Gateway avec une adresse IP virtuelle côté Internet ou avec un point de terminaison interne non exposé à Internet, également appelé point de terminaison d'équilibrage de charge interne (ILB). La configuration de la passerelle avec un équilibrage de charge interne est utile pour les applications métier internes non exposées à Internet. C'est également utile pour les services/niveaux au sein d'une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais la distribution de charge par tourniquet, l'adhérence de session ou la terminaison SSL sont tout de mêmes requises. Cet article vous guidera au cours des étapes de configuration d'une passerelle Application Gateway avec un équilibrage de charge interne.

## Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell Azure à l'aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page Téléchargements](http://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide.
3. Vérifiez que vous disposez de serveurs principaux dans le réseau virtuel ou avec une adresse IP/VIP affectée.


Pour créer une passerelle Application Gateway, exécutez les étapes suivantes dans l'ordre indiqué.

1. [Créer une passerelle Application Gateway](#create-a-new-application-gateway)
2. [Configurer la passerelle](#configure-the-gateway)
3. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
4. [Démarrer la passerelle](#start-the-gateway)
4. [Vérifier la passerelle](#verify-the-gateway-status)



## Créer une passerelle Application Gateway :

**Pour créer la passerelle**, utilisez l’applet de commande `New-AzureApplicationGateway` en remplaçant les valeurs par les vôtres. Notez que la facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Pour valider** la création de la passerelle, vous pouvez utiliser l’applet de commande `Get-AzureApplicationGateway`.

Dans l’exemple, *Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut du paramètre *InstanceCount* est de 2, avec une valeur maximale de 10. La valeur par défaut du paramètre *GatewaySize* est Medium. Les autres valeurs disponibles sont Small et Large. *Vip* et *DnsName* sont vides, car la passerelle n’a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 4:39:39 PM - Begin Operation:
	Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
	Operation: Get-AzureApplicationGateway
	Name: AppGwTest	
	Description: 
	VnetName: testvnet1 
	Subnets: {Subnet-1} 
	InstanceCount: 2 
	GatewaySize: Medium 
	State: Stopped 
	VirtualIPs: 
	DnsName:


## Configurer la passerelle

La configuration d'une passerelle Application Gateway se compose de plusieurs valeurs. Les valeurs peuvent être liées ensemble pour construire la configuration.
 
Les valeurs sont :

- **Pool de serveurs principaux** : liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux** : chaque pool comporte des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal** : il s’agit du port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l'un des serveurs principaux.
- **Écouteur** : l’écouteur comporte un port frontal, un protocole (Http ou Https, sensibles à la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle** : la règle lie l’écouteur et le pool de serveurs principaux et définit le pool de serveurs principaux vers lequel le trafic doit être dirigé lorsqu’il atteint un écouteur spécifique. Actuellement, seule la règle *basic* est prise en charge. La règle *basic* est la distribution de charge par tourniquet (round robin).

Vous pouvez construire votre configuration en créant un objet de configuration ou en utilisant un fichier XML de configuration. Pour construire votre configuration à l'aide d'un fichier XML de configuration, utilisez l'exemple ci-dessous.



Notez les points suivants :


- L’élément *FrontendIPConfigurations* décrit les détails de l’équilibrage de charge interne pertinents pour la configuration de la passerelle Application Gateway avec un équilibrage de charge interne. 

- Le paramètre *Type* de l’adresse IP frontale doit présenter la valeur « Private ».

- Le paramètre *StaticIPAddress* doit être défini sur l’adresse IP interne sur laquelle la passerelle devra recevoir le trafic. Notez que l’élément *StaticIPAddress* est facultatif. S'il n'est pas défini, une adresse IP interne disponible du sous-réseau déployé est choisie.

- La valeur de l’élément *Name* spécifié dans *FrontendIPConfiguration* doit être utilisée dans l’élément *FrontendIP* de HTTPListener pour faire référence à FrontendIPConfiguration.

 **Exemple de configuration XML**

 

		<?xml version="1.0" encoding="utf-8"?>
		<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
			<FrontendIPConfigurations>
				<FrontendIPConfiguration>
					<Name>fip1</Name> 
					<Type>Private</Type> 
					<StaticIPAddress>10.0.0.10</StaticIPAddress> 
				</FrontendIPConfiguration>
			</FrontendIPConfigurations>
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
					<FrontendIP>fip1</FrontendIP>
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


**Remarque :** l’exécution de l’applet de commande `Start-AzureApplicationGateway` peut prendre jusqu’à 15 à 20 minutes.
   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Vérifier l'état de la passerelle

Utilisez l’applet de commande `Get-AzureApplicationGateway` pour vérifier l’état de la passerelle. Si l’applet de commande *Start-AzureApplicationGateway* a réussi à l’étape précédente, la passerelle doit présenter l’état *Running*, et les champs Vip et DnsName doivent comporter des entrées valides. Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie. Dans cet exemple, la passerelle est en cours d'exécution et prête à prendre le trafic.

**Remarque :** la passerelle Application Gateway est configurée pour accepter le trafic sur le point de terminaison de l’équilibrage de charge interne configuré de 10.0.0.10 dans cet exemple.

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
	VirtualIPs    : {10.0.0.10}
	DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## Étapes suivantes


Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO7-->