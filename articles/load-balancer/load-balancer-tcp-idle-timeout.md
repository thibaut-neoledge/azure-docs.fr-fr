---
title: "Configuration du délai d’inactivité TCP de l’équilibrage de charge | Microsoft Docs"
description: "Configuration du délai d’inactivité TCP de l’équilibrage de charge"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: cf1eafc7bca5bddeb32f1e1e05e660d6877ed805
ms.openlocfilehash: 7b8a292bd27792844eb6f620f7564e5091e3d8bc

---

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configuration des paramètres de délai d’inactivité et d’expiration TCP pour Azure Load Balancer

Dans sa configuration par défaut, l’équilibrage de charge Azure a un paramètre de délai d’inactivité de 4 minutes. Si une période d’inactivité est supérieure à la valeur de délai d’expiration, il n’est pas garanti que la session TCP ou HTTP est maintenue entre le client et votre service cloud.

Lorsque la connexion est fermée, votre application cliente peut obtenir un message d’erreur de ce type : « La connexion sous-jacente a été fermée : le serveur a fermé une connexion qui devait être maintenue active ».

Une pratique courante consiste à utiliser TCP keep-alive. Cela permet de maintenir la connexion active pendant une période plus longue. Pour plus d’informations, consultez ces [exemples .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). avec keep-alive activé, les paquets sont envoyés au cours des périodes d’inactivité sur la connexion. Ces paquets keep-alive garantissent que la valeur de délai d’inactivité n’est jamais atteinte et que la connexion est maintenue pendant une longue période.

Ce paramètre fonctionne uniquement pour les connexions entrantes. Pour éviter la perte de la connexion, vous devez configurer TCP keep-alive sur un intervalle inférieur au paramètre de délai d’inactivité ou augmentez la valeur du délai d’inactivité. Pour prendre en charge ces scénarios, nous avons ajouté la prise en charge d’un délai d’inactivité configurable. Vous pouvez maintenant le définir sur une durée comprise entre 4 et 30 minutes.

TCP keep-alive fonctionne bien pour les scénarios où l’autonomie de la batterie n’est pas une contrainte. Il n’est pas recommandé de l’utiliser pour les applications mobiles. L’utilisation de TCP keep-alive depuis une application mobile peut décharger la batterie de l’appareil plus rapidement.

![Délai d’expiration TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Les sections suivantes décrivent comment modifier les paramètres de délai d’inactivité dans les machines virtuelles et les services cloud.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurer le délai d’expiration TCP pour votre adresse IP publique de niveau instance à 15 minutes

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` est facultatif. Si cela n'est pas défini, le délai d'expiration par défaut est de 4 minutes. La plage de délai d’expiration acceptable est comprise entre 4 et 30 minutes.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Définir le délai d’inactivité pendant la création d’un point de terminaison Azure sur une machine virtuelle

Pour modifier le paramètre de délai d’attente pour un point de terminaison, utilisez ce qui suit :

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Pour récupérer votre configuration du délai d’inactivité, utilisez la commande suivante :

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Définir le délai d’expiration TCP sur un jeu de points de terminaison d’équilibrage de charge

Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le délai d'expiration TCP doit être défini sur le jeu de points de terminaison d'équilibrage de charge. Par exemple :

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Modifier les paramètres de délai d’expiration pour les services cloud

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans le fichier .csdef. La mise à jour du délai d’expiration TCP pour le déploiement d’un service cloud requiert une mise à niveau du déploiement. L’exception est si le délai d’expiration TCP n’est spécifié que pour une adresse IP publique. Les paramètres d’adresse IP publique sont définis dans le fichier .cscfg et peuvent être mis à jour via une mise à jour et une mise à niveau du déploiement.

Les modifications apportées aux paramètres de point de terminaison dans .csdef sont les suivantes :

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Les modifications de .cscfg pour le paramètre de délai d’expiration sur des adresses IP publiques sont :

```xml
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
```

## <a name="rest-api-example"></a>Exemple d’API REST

Vous pouvez configurer le délai d’inactivité TCP à l’aide de l’API Gestion des services. Assurez-vous que l’en-tête `x-ms-version` est défini sur la version `2014-06-01` ou une version ultérieure. Mettre à jour la configuration des points de terminaison d’entrée d’équilibrage de charge spécifiés sur toutes les machines virtuelles d’un déploiement.

### <a name="request"></a>Demande

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Réponse

```xml
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
```

## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge accessible sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)



<!--HONumber=Nov16_HO3-->


