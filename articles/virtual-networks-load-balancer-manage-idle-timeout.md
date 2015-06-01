<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Gérer : délai d'inactivité de l'équilibrage de charge" 
   description="Fonctionnalités de gestion du délai d'inactivité de l'équilibrage de charge Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="02/20/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   /> 
   
# Gérer un réseau virtuel : délai d'inactivité TCP de l'équilibrage de charge

Le **délai d'inactivité TCP** permet à un développeur de spécifier un seuil d'inactivité garanti dans le cadre de sessions client-serveur impliquant l'équilibrage de charge Azure.  Une valeur de délai d'inactivité TCP de 4 minutes (valeur par défaut pour l'équilibrage de charge Azure) signifie que si une période d'inactivité dure plus de 4 minutes au cours d'une session client-serveur impliquant l'équilibrage de charge Azure, la connexion est fermée.

Quand une connexion client-serveur est fermée, l'application cliente obtient un message d'erreur de ce type : " La connexion sous-jacente a été fermée : le serveur a fermé une connexion qui devait être maintenue active ".

[TCP Keep-Alive](http://tools.ietf.org/html/rfc1122#page-101) est une pratique courante qui permet de maintenir une connexion pendant une longue période d'inactivité [(exemple MSDN)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Quand TCP Keep-Alive est utilisé, des paquets simples sont envoyés périodiquement par un client (généralement avec une fréquence inférieure au seuil de délai d'inactivité du serveur).  Le serveur considère ces transmissions comme une preuve d'activité de la connexion, même quand il n'y a aucune autre activité. Par conséquent, la valeur de délai d'inactivité n'est jamais atteinte et la connexion peut être maintenue sur une longue période.

Bien que TCP Keep-Alive fonctionne correctement, les applications mobiles n'ont généralement pas accès à cette option, car elle consomme les ressources d'alimentation limitées des appareils mobiles. Une application mobile qui utilise TCP Keep-Alive épuise la batterie de l'appareil plus rapidement, car elle utilise en permanence l'alimentation pour un usage réseau.

Pour supporter les scénarios d'appareil mobile, l'équilibrage de charge Azure prend en charge un délai d'inactivité TCP configurable. Les développeurs peuvent fixer ce délai à une durée comprise entre 4 et 30 minutes pour les connexions entrantes (le délai d'inactivité TCP configurable ne s'applique pas aux connexions sortantes). Les clients peuvent ainsi conserver une session beaucoup longue avec un serveur en cas de longues périodes d'inactivité.  Une application d'appareil mobile peut quand même choisir d'exploiter la technique TCP Keep-Alive pour maintenir des connexions dont la durée d'inactivité prévue est supérieure à 30 minutes, mais ce délai d'inactivité TCP plus long permet aux applications d'émettre des demandes TCP Keep-Alive beaucoup moins fréquentes qu'auparavant, ce qui réduit de manière significative la surcharge sur les ressources d'alimentation de l'appareil mobile.

## Implémentation

Le délai d'inactivité TCP peut être configuré pour les éléments suivants : 

* [Adresses IP publiques de niveau d'instance](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [Jeux de points de terminaison soumise à l'équilibrage de charge](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Points de terminaison sur une machine virtuelle](http://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/)
* [Rôles web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Rôles de travail](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Étapes suivantes
* TBD

## Exemples PowerShell
Téléchargez [la dernière version d'Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) pour de meilleurs résultats.

### Configurer le délai d'expiration TCP pour votre adresse IP publique de niveau d'instance à 15 minutes

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

Le paramètre IdleTimeoutInMinutes est facultatif. S'il n'est pas défini, le délai d'expiration par défaut est de 4 minutes. Sa valeur peut maintenant être définie entre 4 et 30 minutes.

### Définir le délai d'inactivité pendant la création d'un point de terminaison Azure sur un ordinateur virtuel

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Récupérer votre configuration du délai d'inactivité

    PS C:> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    
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

Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le délai d'expiration TCP doit être défini sur le jeu de points de terminaison d'équilibrage de charge :

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Exemples de service cloud

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET pour mettre à jour votre service cloud.

Les paramètres de point de terminaison des services cloud sont définis dans .csdef. Par conséquent, pour mettre à jour le délai d'expiration TCP pour un déploiement de services cloud, une mise à niveau du déploiement s'impose, sauf si le délai d'expiration TCP n'est spécifié que pour une adresse IP publique. Les paramètres d'adresse IP publique sont définis dans le fichier .cscfg et peuvent être mis à jour via une mise à jour et une mise à niveau du déploiement.

Les modifications apportées aux paramètres de point de terminaison dans .csdef sont les suivantes :

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

Les développeurs peuvent configurer la distribution d'équilibrage de charge à l'aide de l'API de gestion de service.  Veillez à ajouter l'en-tête x-ms-version et de le définir sur la version 2014-06-01 ou une version ultérieure.

### Mettre à jour la configuration des points de terminaison d'entrée d'équilibrage de charge spécifiés sur toutes les machines virtuelles d'un déploiement

#### Demande

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

La valeur de LoadBalancerDistribution peut être sourceIP pour une affinité à 2 tuples, sourceIPProtocol pour une affinité à 3 tuples ou none (aucune affinité, c'est-à-dire, 5 tuples)

#### Response

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

<!--HONumber=47-->
