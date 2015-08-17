<properties 
   pageTitle="Gérer : mode de distribution d’équilibrage de charge (affinité d’IP source)"
   description="Fonctionnalités de gestion pour le mode de distribution d'équilibrage de charge Azure" 
   services="virtual-network" 
   documentationCenter="" 
   authors="telmosampaio" 
   manager="carolz" 
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/27/2015"
   ms.author="telmos"
   />
   
# Gérer un réseau virtuel : mode de distribution d’équilibrage de charge (affinité d’IP source)
L’**affinité d’IP source** (également appelée **affinité de session** ou **affinité d’IP du client**), l’un des modes de distribution d’équilibrage de charge Azure, lie les connexions entre un client unique et un serveur unique hébergé sur Azure, au lieu de répartir dynamiquement chaque connexion client entre différents serveurs hébergés sur Azure (comportement d’équilibrage de charge par défaut).

Avec l'affinité d'IP source, l'équilibrage de charge Azure peut être configuré pour utiliser une combinaison à 2 tuples (IP Source, IP de destination) ou une combinaison à 3 tuples (IP source IP, IP de destination, protocole) pour mapper le trafic au pool de serveurs disponibles hébergés sur Azure. Quand vous utilisez l'affinité d'IP source, les connexions initiées depuis le même ordinateur client sont gérées par un seul point de terminaison DIP (un seul serveur hébergé sur Azure).

## Origine du service

L’affinité d’IP source permet de résoudre une ancienne [incompatibilité entre l’équilibrage de charge Azure et la Passerelle des services Bureau à distance (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Implémentation

L'affinité d'IP source peut être configurée pour les éléments suivants :

* [Points de terminaison sur une machine virtuelle](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Jeux de points de terminaison soumis à l’équilibrage de charge](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Rôles web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Rôles de travail](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Scénarios
1. Cluster de passerelle des services Bureau à distance utilisant un seul service cloud
2. Téléchargement de médias (c'est-à-dire, UDP pour les données, TCP pour le contrôle)
  * Le client initie une session TCP à l'adresse IP publique soumise à l'équilibrage de charge et hébergée sur Azure.
  * La demande du client est dirigée vers une adresse DIP par l'équilibrage de charge ; ce canal reste actif pour surveiller l'intégrité de la connexion.
  * Le client initie une session UDP à la même adresse IP publique soumise à l'équilibrage de charge et hébergée sur Azure.
  * L'équilibrage de charge Azure dirige la demande vers le même point de terminaison DIP que la connexion TCP.
  * Le client télécharge le média avec un débit UDP plus élevé tout en conservant le canal de contrôle sur TCP pour des raisons de fiabilité.
  
## Mises en garde
* Si le jeu d'équilibrage de la charge est modifié (c'est-à-dire, si une machine virtuelle est ajoutée ou supprimée), la distribution du canal client est recalculée ; les nouvelles connexions à partir des clients existants peuvent être gérées par un autre serveur que celui utilisé à l'origine.
* L'utilisation de l'affinité d'IP source peut occasionner une distribution inégale du trafic entre les serveurs hébergés sur Azure.
* Les clients qui acheminent leur trafic via un proxy peuvent être considérés vus comme un client unique par l'équilibrage de charge Azure.

## Exemples PowerShell
Pour optimiser les résultats, téléchargez la [dernière version d’Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases).

### Ajouter un point de terminaison Azure à une machine virtuelle et définir le mode de distribution d'équilibrage de charge

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€“LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 Ã¢â‚¬â€œLoadBalancerDistribution Ã¢â‚¬Å“sourceIPÃ¢â‚¬ï¿½| Update-AzureVM  

LoadBalancerDistribution peut être défini avec la valeur sourceIP pour un équilibrage de charge à 2 tuples (IP source, IP de destination), sourceIPProtocol pour un équilibrage de charge à 3 tuples (IP source, IP de destination, protocole) ou none si vous préférez le comportement par défaut (équilibrage de charge à 5 tuples).

### Récupérer la configuration du mode de distribution d'équilibrage de charge d'un point de terminaison
    PS C:\> Get-AzureVM â€“ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint
    
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

Si l'élément LoadBalancerDistribution n'est pas présent, l'équilibrage de charge Azure utilise l'algorithme par défaut à 5 tuples.

### Définir le mode de distribution sur un jeu de points de terminaison d'équilibrage de charge

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 Ã¢â‚¬â€œLoadBalancerDistribution "sourceIP"
    
Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le mode de distribution défini doit être le jeu de points de terminaison d'équilibrage de charge.

## Exemples de service cloud

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET pour mettre à jour votre service cloud.

Les paramètres de point de terminaison des services cloud sont définis dans .csdef. Pour mettre à jour le mode de distribution d'équilibrage de charge pour un déploiement de services cloud, une mise à niveau du déploiement s'impose.

Voici un exemple de modifications apportées aux paramètres de point de terminaison dans .csdef :

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
    
## Exemples d'API

Les développeurs peuvent configurer la distribution d'équilibrage de charge à l'aide de l'API de gestion de service. Veillez à ajouter l'en-tête x-ms-version et de le définir sur la version 01/09/2014 ou une version ultérieure.

### Mettre à jour la configuration du jeu d'équilibrage de la charge spécifié dans un déploiement

#### Demande

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

#### Réponse

    HTTP/1.1 202 Accepted 
    Cache-Control: no-cache 
    Content-Length: 0 
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
    x-ms-servedbyregion: ussouth2 
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
    Date: Thu, 16 Oct 2014 22:49:21 GMT
 

<!---HONumber=August15_HO6-->