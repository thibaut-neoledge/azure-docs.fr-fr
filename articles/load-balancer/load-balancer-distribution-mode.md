---
title: "Configuration d’un mode de distribution d’équilibrage de charge | Microsoft Docs"
description: "Procédure de configuration du mode de distribution d’équilibrage de charge Azure pour prendre en charge l'affinité d’IP source"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: a6bba6b3b924564fe7ae16fa1265dd4d93bd6b94
ms.openlocfilehash: a6b3c346358e0aed4c60c4903932236edc237379
ms.contentlocale: fr-fr
ms.lasthandoff: 09/28/2017

---

# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configuration du mode de distribution de l’équilibrage de charge

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Mode de distribution basé sur le hachage

L’algorithme de distribution par défaut est un hachage à 5 tuples (IP source, port source, IP de destination, port de destination, type de protocole) pour mapper le trafic vers des serveurs disponibles. Il fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session sont dirigés vers la même instance IP (DIP) de centre de données derrière le point de terminaison d’équilibrage de charge. Lorsque le client démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison DIP.

![équilibrage de charge basé sur le hachage](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figure 1 : distribution 5 tuples

## <a name="source-ip-affinity-mode"></a>Mode d’affinité d’IP source

Nous vous avons un autre mode de distribution appelé « affinité d’IP source » (également connu sous le nom d’« affinité de session » ou d’« affinité d’IP client »). L’équilibrage de charge Azure peut être configuré pour utiliser 2 tuples (IP source, IP de destination) ou 3 tuples (IP Source, adresse de destination, protocole) pour mapper le trafic vers les serveurs disponibles. En utilisant l’affinité d’IP source, les connexions initiées depuis le même ordinateur client s’orientent vers le même point de terminaison DIP.

Le schéma suivant illustre une configuration à 2 tuples. Notez comment la distribution 2 tuples passe de l’équilibrage de charge à la machine virtuelle 1 (VM1), puis est sauvegardée par VM2 et VM3.

![affinité de session](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figure 2 : distribution 2 tuples

L’affinité d’IP source permet de résoudre une incompatibilité entre l’équilibrage de charge Azure et la passerelle du Bureau à distance (RD). Vous pouvez maintenant générer une batterie de passerelles des services Bureau à distance dans un seul service cloud.

Le chargement de médias constitue un autre cas d’utilisation où le chargement des données se produit via UDP, mais le plan de contrôle via TCP :

* Un client établit d’abord une session TCP avec l'adresse publique d'équilibrage de charge et est ensuite dirigé vers une adresse DIP spécifique. Ce canal est laissé actif pour surveiller l'état de connexion
* Une nouvelle session UDP à partir du même ordinateur client est initialisée au niveau du même point de terminaison public de l’équilibrage de charge, le but ici étant que cette connexion soit également dirigée vers le même point de terminaison DIP que la connexion TCP précédente afin que le téléchargement de média puisse être exécuté à haut débit tout en conservant un canal de contrôle via le TCP.

> [!NOTE]
> Lorsqu’un jeu d'équilibrage de charge (suppression ou ajout d'une machine virtuelle) est modifié, la distribution des demandes du client est recalculée. Vous ne pouvez pas dépendre de nouvelles connexions à partir de clients existants qui se retrouvent sur le même serveur. En outre, le mode de distribution d'affinité d’IP source peut entraîner une distribution inégale du trafic. Les clients qui s’exécutent derrière des serveurs proxy peuvent être vu comme une application cliente unique.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configuration des paramètres d'affinité d’IP source pour l'équilibrage de charge

Pour les machines virtuelles, vous pouvez utiliser PowerShell pour modifier les paramètres de délai d'attente :

Ajouter un point de terminaison Azure à une machine virtuelle et définir le mode de distribution d'équilibrage de charge

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

LoadBalancerDistribution peut être défini avec la valeur sourceIP pour un équilibrage de charge à 2 tuples (IP source, IP de destination), sourceIPProtocol pour un équilibrage de charge à 3 tuples (IP source, IP de destination, protocole) ou none si vous préférez le comportement par défaut de l’équilibrage de charge à 5 tuples.

Utilisez ce qui suit pour récupérer la configuration du mode de distribution d'équilibrage de charge d'un point de terminaison :

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

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Définir le mode de distribution sur un jeu de points de terminaison d'équilibrage de charge

Si les points de terminaison font partie d'un jeu de points de terminaison d'équilibrage de charge, le mode de distribution doit être défini sur le jeu de points de terminaison d'équilibrage de charge :

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuration du service cloud pour modifier le mode de distribution

Vous pouvez utiliser le Kit de développement logiciel (SDK) Azure pour .NET 2.5 afin de mettre à jour votre service cloud. Les paramètres de point de terminaison des services cloud sont définis dans .csdef. Pour mettre à jour le mode de distribution d'équilibrage de charge pour un déploiement de services cloud, une mise à niveau du déploiement s'impose.
Voici un exemple de modifications apportées aux paramètres de point de terminaison dans .csdef :

```xml
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
```

## <a name="api-example"></a>Exemple d’API

Vous pouvez configurer la distribution d’équilibrage de charge à l’aide de l’API Gestion des services. Veillez à ajouter l’en-tête `x-ms-version` et à le définir sur la version `2014-09-01` ou une version ultérieure.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Mettre à jour la configuration du jeu d'équilibrage de la charge spécifié dans un déploiement

#### <a name="request-example"></a>Exemple de demande

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
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

La valeur de LoadBalancerDistribution peut être sourceIP pour une affinité à 2 tuples, sourceIPProtocol pour une affinité à 3 tuples ou none (aucune affinité. par exemple 5 tuples)

#### <a name="response"></a>Réponse

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Étapes suivantes

[Présentation de l’équilibrage de charge interne](load-balancer-internal-overview.md)

[Prise en main de la configuration d’un équilibrage de charge sur Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

