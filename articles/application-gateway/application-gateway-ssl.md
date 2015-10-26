<properties 
   pageTitle="Configuration d'une passerelle Application Gateway pour le déchargement SSL | Microsoft Azure"
   description="Cet article fournit des instructions pour configurer le déchargement SSL sur une passerelle Application Gateway Azure."
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
   ms.date="09/25/2015"
   ms.author="joaoma"/>

# Configuration d'une passerelle Application Gateway pour le déchargement SSL

Une passerelle Application Gateway peut être configurée pour terminer la session SSL au niveau de la passerelle, ce qui permet d'éviter le déchiffrement SSL coûteux sur la batterie de serveurs web. Le déchargement SSL simplifie également l'installation du serveur frontal et la gestion de l'application.

## Avant de commencer

1. Installez la version la plus récente des applets de commande PowerShell Azure à l'aide de Web Platform Installer. Vous pouvez télécharger et installer la dernière version à partir de la section **Windows PowerShell** de la [page de téléchargement](http://azure.microsoft.com/downloads/).
2. Vérifiez que vous disposez d'un réseau virtuel qui fonctionne avec un sous-réseau valide.
3. Vérifiez que vous disposez de serveurs principaux dans le réseau virtuel ou avec une adresse IP/VIP affectée.

Pour configurer le déchargement SSL sur une passerelle Application Gateway, procédez comme suit dans l'ordre indiqué.

1. [Créer une passerelle Application Gateway](#create-a-new-application-gateway)
2. [Télécharger des certificats SSL](#upload-ssl-certificates) 
3. [Configurer la passerelle](#configure-the-gateway)
4. [Définir la configuration de la passerelle](#set-the-gateway-configuration)
5. [Démarrer la passerelle](#start-the-gateway)
6. [Vérifier l'état de la passerelle](#verify-the-gateway-status)


## Créer une passerelle Application Gateway

**Pour créer la passerelle**, utilisez l'applet de commande `New-AzureApplicationGateway`, en remplaçant les valeurs par les vôtres. Notez que la facturation de la passerelle ne démarre pas à ce stade. La facturation commence à une étape ultérieure, lorsque la passerelle a démarré correctement.

Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie.

	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Pour valider** que la passerelle a été créée, vous pouvez utiliser l'applet de commande `Get-AzureApplicationGateway`.


Dans l'exemple, *Description*, *InstanceCount* et *GatewaySize* sont des paramètres facultatifs. La valeur par défaut pour *InstanceCount* est 2, avec une valeur maximale de 10. La valeur par défaut pour *GatewaySize* est Medium. Les autres valeurs disponibles sont Small et Large. *Vip* et *DnsName* s'affichent sans valeur car la passerelle n'a pas encore démarré. Ces valeurs seront créées une fois la passerelle en cours d'exécution.

Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie.

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


## Télécharger des certificats SSL 

Utilisez `Add-AzureApplicationGatewaySslCertificate` pour télécharger le certificat de serveur au format *pfx* sur la passerelle Application Gateway. Le nom du certificat est choisi par l'utilisateur et doit être unique au sein de la passerelle Application Gateway. Ce certificat est identifié par ce nom dans toutes les opérations de gestion de certificat sur la passerelle Application Gateway.

Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie. Remplacez les valeurs de l'exemple par les vôtres.

	PS C:\> Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file> 
	
	VERBOSE: 5:05:23 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:06:29 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   21fdc5a0-3bf7-2c12-ad98-192e0dd078ef

Ensuite, validez le téléchargement du certificat. Utilisez le `Get-AzureApplicationGatewayCertificate`.

Cet exemple montre l'applet de commande sur la première ligne, suivie de la sortie.

	PS C:\> Get-AzureApplicationGatewaySslCertificate AppGwTest 

	VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate 
	VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
	Name           : SslCert 
	SubjectName    : CN=gwcert.app.test.contoso.com 
	Thumbprint     : AF5ADD77E160A01A6......EE48D1A 
	ThumbprintAlgo : sha1RSA
	State..........: Provisioned


## Configurer la passerelle

La configuration d'une passerelle Application Gateway se compose de plusieurs valeurs. Les valeurs peuvent être liées ensemble pour construire la configuration.

Les valeurs sont :
 
- **Pool de serveurs principaux :** la liste des adresses IP des serveurs principaux. Les adresses IP répertoriées doivent appartenir au sous-réseau de réseau virtuel ou elles doivent être une adresse IP/VIP publique. 
- **Paramètres du pool de serveurs principaux :** chaque pool a des paramètres comme le port, le protocole et une affinité basée sur les cookies. Ces paramètres sont liés à un pool et sont appliqués à tous les serveurs du pool.
- **Port frontal :** ce port est le port public ouvert sur la passerelle Application Gateway. Le trafic atteint ce port, puis il est redirigé vers l'un des serveurs principaux.
- **Écouteur :** l'écouteur a un port frontal, un protocole (Http ou Https, sensibles à la casse) et le nom du certificat SSL (en cas de configuration du déchargement SSL). 
- **Règle :** la règle lie l'écouteur et le pool de serveurs principaux et définit vers quel pool de serveurs principaux le trafic doit être dirigé lorsqu'il atteint un écouteur spécifique. Actuellement, seule la règle de *base* est prise en charge. La règle de *base* est la distribution de charge par tourniquet.

**Notes de configuration supplémentaires :**

Pour configurer des certificats SSL, le protocole dans **HttpListener** doit passer à *Https* (sensible à la casse). L'élément **SslCert** doit être ajouté à **HttpListener** avec la valeur définie sur le même nom que celui utilisé dans le téléchargement des certificats SSL dans la section ci-dessus. Le port du serveur frontal doit être mis à jour sur 443.

**Pour activer l'affinité basée sur les cookies** : une passerelle Application Gateway peut être configurée pour s'assurer qu'une requête d'une session client est toujours dirigée vers le même ordinateur virtuel dans la batterie de serveurs web. Ceci se fait par l'injection d'un cookie de session qui permet à la passerelle diriger le trafic de manière appropriée. Pour activer l'affinité basée sur les cookies, définissez **CookieBasedAffinity** sur *Activé* dans l'élément **BackendHttpSettings**.



Vous pouvez construire votre configuration en créant un objet de configuration ou en utilisant un fichier XML de configuration. Pour construire votre configuration à l'aide d'un fichier XML de configuration, utilisez l'exemple ci-dessous.

**Exemple de configuration XML**


	    <?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendIPConfigurations />
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>443</Port>
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
	            <Protocol>Https</Protocol>
	            <SslCert>GWCert</SslCert>
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

Ensuite, vous allez définir la passerelle Application Gateway. Vous pouvez utiliser l'applet de commande `Set-AzureApplicationGatewayConfig` avec un objet de configuration ou avec un fichier XML de configuration.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Démarrer la passerelle

Une fois la passerelle configurée, utilisez l'applet de commande `Start-AzureApplicationGateway` pour démarrer la passerelle. La facturation pour une passerelle Application Gateway commence une fois la passerelle démarrée avec succès.


**Remarque :** l'applet de commande `Start-AzureApplicationGateway` peut prendre jusqu'à 15 à 20 minutes pour se terminer.

   
	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b


## Vérifier l'état de la passerelle

Utilisez l'applet de commande `Get-AzureApplicationGateway` pour vérifier l'état de la passerelle. Si *Start-AzureApplicationGateway* a réussi à l'étape précédente, l'état doit être *en cours d'exécution*, et Vip et DnsName doivent disposer d'entrées valides.

Cet exemple montre une passerelle Application Gateway en cours d'exécution et est prête à prendre le trafic.

	PS C:\> Get-AzureApplicationGateway AppGwTest 

	Name          : AppGwTest2
	Description   : 
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	VirtualIPs    : {23.96.22.241}
	DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## Étapes suivantes


Si vous souhaitez plus d'informations sur les options d'équilibrage de charge en général, consultez :

- [Équilibrage de charge Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Oct15_HO3-->