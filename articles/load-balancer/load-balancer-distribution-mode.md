<properties 
   pageTitle="Configuration d’un mode de distribution d’équilibrage de charge | Microsoft Azure"
   description="Procédure de configuration du mode de distribution d’équilibrage de charge Azure pour prendre en charge l'affinité d’IP source"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/05/2016"
   ms.author="joaoma" />


# Mode de distribution d'équilibrage de charge (affinité d'IP source)

Nous vous avons présenté un nouveau mode de distribution appelé « affinité d’IP source » (également connu sous le nom d’« affinité de session » ou d’« affinité d’IP client »). L’équilibrage de charge Azure peut être configuré pour utiliser 2 tuples (IP source, IP de destination) ou 3 tuples (IP Source, adresse de destination, protocole) pour mapper le trafic vers les serveurs disponibles. En utilisant l'affinité d’IP Source, les connexions établies à partir du même ordinateur client vont au même point de terminaison DIP.

![équilibrage de charge basé sur le hachage](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

L’affinité d’IP source permet de résoudre une incompatibilité entre l’équilibrage de charge Azure et la passerelle des services Bureau à distance. Vous pouvez maintenant générer une batterie de passerelles des services Bureau à distance dans un seul service cloud. Le téléchargement de média constitue un autre scénario d’utilisation où le téléchargement réel des données se produit via l’UDP, mais où le plan de contrôle s'effectue via le TCP :

- Un client établit d’abord une session TCP avec l'adresse publique d'équilibrage de charge et est ensuite dirigé vers une adresse DIP spécifique. Ce canal est laissé actif pour surveiller l'état de connexion
- Une nouvelle session UDP à partir du même ordinateur client est initialisée au niveau du même point de terminaison public de l’équilibrage de charge, le but ici étant que cette connexion soit également dirigée vers le même point de terminaison DIP que la connexion TCP précédente afin que le téléchargement de média puisse être exécuté à haut débit tout en conservant un canal de contrôle via le TCP.
 
Notez que si le jeu d'équilibrage de charge (suppression ou ajout d'une machine virtuelle) est modifié, la distribution des demandes du client est recalculée. Vous ne pouvez pas dépendre de nouvelles connexions à partir de sessions clientes existantes qui se retrouvent sur le même serveur. En outre, le mode de distribution d'affinité d’IP source peut entraîner une distribution inégale du trafic. Les clients qui s’exécutent derrière des serveurs proxy peuvent être vu comme une application cliente unique.

L’algorithme de distribution utilise un hachage à 5 tuples (IP source, port source, IP de destination, port de destination, type de protocole) pour mapper le trafic vers des serveurs disponibles. Il fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session TCP ou UDP sont dirigés vers la même instance IP (DIP) de centre de données derrière le point de terminaison d’équilibrage de charge. Lorsque le client ferme et rouvre la connexion ou démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison DIP.

![équilibrage de charge basé sur le hachage](./media/load-balancer-distribution-mode/load-balancer-distribution.png)


## Configuration des paramètres d'affinité d’IP source pour l'équilibrage de charge
 
Pour les machines virtuelles, vous pouvez utiliser Powershell pour modifier les paramètres de délai d'attente :
 
Ajouter un point de terminaison Azure à une machine virtuelle et définir le mode de distribution d'équilibrage de charge

	Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

>[AZURE.NOTE] LoadBalancerDistribution peut être défini avec la valeur sourceIP pour un équilibrage de charge à 2 tuples (IP source, IP de destination), sourceIPProtocol pour un équilibrage de charge à 3 tuples (IP source, IP de destination, protocole) ou none si vous préférez le comportement par défaut de l’équilibrage de charge à 5 tuples.


Récupérer la configuration du mode de distribution d'équilibrage de charge d'un point de terminaison

	PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
	LoadBalancerDistribution : sourceIP
 
Si l'élément LoadBalancerDistribution n'est pas présent, l'équilibrage de charge Azure utilise alors l'algorithme par défaut à 5 tuples.

 
### Définir le mode de distribution sur un jeu de points de terminaison d'équilibrage de charge

Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le mode de distribution doit être défini sur le jeu de points de terminaison d'équilibrage de charge :

	Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocol TCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### Configuration du service cloud pour modifier le mode de distribution

Vous pouvez exploiter le Kit de développement logiciel (SDK) Azure pour .NET 2.5 (publication en novembre) pour mettre à jour vos paramètres de point de terminaison de service cloud pour les services cloud réalisés dans .csdef. Pour mettre à jour le mode de distribution d'équilibrage de charge pour un déploiement de services cloud, une mise à niveau du déploiement s'impose. Voici un exemple de modifications apportées aux paramètres de point de terminaison dans .csdef :

	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
  	<Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
  	</Endpoints>
	</WorkerRole>
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


## Exemple d’API

Vous pouvez configurer la distribution de l’équilibreur de charge à l’aide de l’API de gestion des services. Vérifiez que l’en-tête `x-ms-version` est définie sur la version `2014-09-01` ou ultérieure.
 
Mettre à jour la configuration du jeu d'équilibrage de la charge spécifié dans un déploiement

Exemple de demande

	POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 

	x-ms-version: 2014-09-01 

	Content-Type: application/xml 

	<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance"> 
	<InputEndpoint> 
	<LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName> 
	<LocalPort> local-port-number </LocalPort> 
	<Port> external-port-number </Port> 
	<LoadBalancerProbe> 
	<Port> port-assigned-to-probe </Port> 
	<Protocol> probe-protocol </Protocol> 
	<IntervalInSeconds> interval-of-probe </IntervalInSeconds> 
	<TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds> 
	</LoadBalancerProbe> 
	<Protocol> endpoint-protocol </Protocol> 
	<EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn> 
	<IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes> 
	<LoadBalancerDistribution>sourceIP</LoadBalancerDistribution> 
	</InputEndpoint> 
	</LoadBalancedEndpointList>

La valeur de LoadBalancerDistribution peut être sourceIP pour une affinité à 2 tuples, sourceIPProtocol pour une affinité à 3 tuples ou none (aucune affinité, c'est-à-dire, 5 tuples)

	Response

	HTTP/1.1 202 Accepted 
	Cache-Control: no-cache 
	Content-Length: 0 
	Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
	x-ms-servedbyregion: ussouth2 
	x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
	Date: Thu, 16 Oct 2014 22:49:21 GMT

## Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0427_2016-->