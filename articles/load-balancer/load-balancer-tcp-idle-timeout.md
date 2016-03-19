<properties 
   pageTitle="Configuration du délai d’inactivité TCP de l’équilibrage de charge | Microsoft Azure"
   description="Configuration du délai d’inactivité TCP de l’équilibrage de charge"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />

# Modification des paramètres de délai d’inactivité TCP pour l’équilibrage de charge

Dans sa configuration par défaut, l’équilibrage de charge Azure a un paramètre de « délai d’inactivité » de 4 minutes.

Cela signifie que si la période d’inactivité sur vos sessions TCP ou HTTP est supérieure à la valeur du délai d’attente, il n’existe aucune garantie que la connexion entre le client et votre service soit maintenue.

Lorsque la connexion est fermée, votre application cliente obtient un message d’erreur de ce type : « La connexion sous-jacente a été fermée : le serveur a fermé une connexion qui devait être maintenue active ».

Une pratique courante pour maintenir la connexion active pendant une période plus longue consiste à utiliser TCP Keep-alive (vous trouverez des exemples .NET [ici](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)).

Les paquets sont envoyés lorsqu’aucune activité n’est détectée sur la connexion. En conservant une activité réseau continue, la valeur de délai d’inactivité n’est jamais atteinte et la connexion est maintenue pendant une longue période.

L’idée consiste à configurer TCP Keep-alive avec un intervalle plus court que le paramètre d’expiration par défaut pour éviter que la connexion soit interrompue ou d’augmenter la valeur du délai d’inactivité pour que la session de connexion TCP reste connectée.

Bien que TCP Keep-alive fonctionne bien pour les scénarios où la batterie n’est pas une contrainte, ce n’est généralement pas une option valide pour les applications mobiles. L’utilisation de TCP Keep-alive depuis une application mobile décharge la batterie de l’appareil plus rapidement.

Pour prendre en charge ces scénarios, nous avons ajouté la prise en charge d’un délai d’inactivité configurable. Vous pouvez maintenant le définir sur une durée comprise entre 4 et 30 minutes. Ce paramètre fonctionne uniquement pour les connexions entrantes.

![tcptimeout](./media/load-balancer-tcp-idle-timeout/image1.png)


## Modification des paramètres de délai d’inactivité dans les machines virtuelles et les services cloud

- Configurer le délai d’expiration TCP sur un point de terminaison sur une machine virtuelle via PowerShell ou l’API de gestion des services
- Configurez le délai d’expiration TCP pour vos jeux de points de terminaison d’équilibrage de charge via PowerShell ou l’API de gestion des services.
- Configurer le délai d’expiration TCP pour votre adresse IP publique de niveau d’instance
- Configurez le délai d’expiration TCP pour vos rôles Web/de travail via le modèle de service.
 

>[AZURE.NOTE]N’oubliez pas que certaines commandes existent uniquement dans le dernier package Azure PowerShell. Si la commande Powershell n’existe pas, téléchargez le dernier package de PowerShell.

 
### Configurez le délai d’expiration TCP pour votre adresse IP publique de niveau d’instance sur 15 minutes.

	Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

Le paramètre IdleTimeoutInMinutes est facultatif. S'il n'est pas défini, le délai d'expiration par défaut est de 4 minutes.

>[AZURE.NOTE]La plage de délai d’expiration acceptable est comprise entre 4 et 30 minutes.
 
### Définir le délai d'inactivité pendant la création d'un point de terminaison Azure sur un ordinateur virtuel

Pour modifier le paramètre de délai d’attente pour un point de terminaison

	Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
 
Récupérer votre configuration du délai d'inactivité

	PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
	VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
	LBSetName : MyLoadBalancedSet
	LocalPort : 80
	Name : HTTP
	Port : 80
	Protocol : tcp
	Vip : 65.52.xxx.xxx
	ProbePath :
	ProbePort : 80
	ProbeProtocol : tcp
	ProbeIntervalInSeconds : 15
	ProbeTimeoutInSeconds : 31
	EnableDirectServerReturn : False
	Acl : {}
	InternalLoadBalancerName :
	IdleTimeoutInMinutes : 15
 
### Définir le délai d'expiration TCP sur un jeu de points de terminaison d'équilibrage de charge

Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le délai d'expiration TCP doit être défini sur le jeu de points de terminaison d'équilibrage de charge :

	Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
 
### Modification des paramètres de délai d’expiration pour les services cloud

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET 2.4 pour mettre à jour votre service cloud.

Les paramètres de point de terminaison des services cloud sont définis dans .csdef. Pour mettre à jour le délai d’expiration TCP pour un déploiement de services cloud, une mise à niveau du déploiement s’impose, sauf si le délai d'expiration TCP n'est spécifié que pour une adresse IP publique. Les paramètres d'adresse IP publique sont définis dans le fichier .cscfg et peuvent être mis à jour via une mise à jour et une mise à niveau du déploiement.

Les modifications apportées aux paramètres de point de terminaison dans .csdef sont les suivantes :

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
	  </Endpoints>
	</WorkerRole>

Les modifications de .cscfg pour le paramètre de délai d’attente sur des adresses IP publiques sont :

	<NetworkConfiguration>
 	 <VirtualNetworkSite name="VNet"/>
 	 <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
 	 </AddressAssignments>
	</NetworkConfiguration>

## Exemple d’API REST

Vous pouvez configurer le délai d’inactivité TCP à l’aide de l’API de gestion des services. Assurez-vous d’ajouter l’en-tête x-ms-version, défini sur la version 2014-06-01 ou ultérieure.
 
Mettre à jour la configuration des points de terminaison d'entrée d'équilibrage de charge spécifiés sur toutes les machines virtuelles d'un déploiement
	
	Request

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

	Response

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
	<InputEndpoint>
	<LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
	<LocalPort>local-port-number</LocalPort>
	<Port>external-port-number</Port>
	<LoadBalancerProbe>
	<Path>path-of-probe</Path>
	<Port>port-assigned-to-probe</Port>
	<Protocol>probe-protocol</Protocol>
	<IntervalInSeconds>interval-of-probe</IntervalInSeconds>
	<TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
	</LoadBalancerProbe>
	<LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
	<Protocol>endpoint-protocol</Protocol>
	<IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
	<EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
	<EndpointACL>
	<Rules>
	<Rule>
	<Order>priority-of-the-rule</Order>
	<Action>permit-rule</Action>
	<RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
	<Description>description-of-the-rule</Description>
	</Rule>
	</Rules>
	</EndpointACL>
	</InputEndpoint>
	</LoadBalancedEndpointList>

## Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge accessible sur Internet](load-balancer-internet-getstarted.md)

[Configuration d’un mode de distribution d’équilibrage de charge](load-balancer-distribution-mode.md)

 

<!---HONumber=AcomDC_1125_2015-->