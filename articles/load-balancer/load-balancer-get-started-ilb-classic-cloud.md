---
title: "Créer un équilibreur de charge interne pour les services cloud dans un modèle de déploiement classique | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 57966056-0f46-4f95-a295-483ca1ad135d
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85e22954c19d7d51579029c7426f0ee79b780504

---

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Prise en main de la création d’un équilibreur de charge interne (Classic) pour les services cloud

[!INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]

Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurer l’équilibreur de charge interne pour les services cloud

L’équilibreur de charge interne est pris en charge pour les machines virtuelles et les services cloud. Un point de terminaison d’équilibreur de charge interne créé dans un service cloud hors d’un réseau virtuel régional est accessible uniquement au sein du service cloud.

La configuration d’équilibreur de charge interne doit être définie lors de la création du premier déploiement dans le service cloud, comme illustré dans l’exemple ci-dessous.

> [!IMPORTANT]
> Un réseau virtuel déjà créé pour le déploiement de cloud est requis pour exécuter les étapes ci-dessous. Pour créer l’équilibrage de charge interne, vous avez besoin du nom du réseau virtuel et du nom de sous-réseau.

### <a name="step-1"></a>Étape 1

Ouvrez le fichier de configuration du service (.cscfg) pour votre déploiement cloud dans Visual Studio et ajoutez la section suivante pour créer l’équilibrage de charge interne sous l’élément «`</Role>`» pour la configuration du réseau.

```xml
    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>
```

Nous allons ajouter les valeurs pour le fichier de configuration du réseau pour que vous voyiez à quoi il ressemble. Dans l'exemple, supposons que vous avez créé un sous-réseau appelé « test_vnet » avec un sous-réseau 10.0.0.0/24 appelé test_subnet et une adresse IP statique 10.0.0.4. L'équilibrage de charge sera nommé testLB.

```xml
    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>
```

Vous trouverez d’autres informations sur le schéma d’équilibrage de charge sous [Ajouter un équilibrage de charge](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Étape 2

Modifiez le fichier de définition de service (.csdef) pour ajouter des points de terminaison à l’équilibrage de charge interne. Au moment où une instance de rôle est créée, le fichier de définition de service ajoute les instances de rôle à l’équilibrage de charge interne.

```xml
    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>
```

En conservant les valeurs de l’exemple ci-dessus, ajoutons les valeurs au fichier de définition de service

```xml
    <WorkerRole name="WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>
```

Le trafic réseau est équilibré à l’aide de l’équilibrage de charge testLB utilisant le port 80 pour les requêtes entrantes, avec envoi aux instances de rôle de travail sur le port 80 également.

## <a name="next-steps"></a>Étapes suivantes

[Configurer le mode de distribution d’équilibreur de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO2-->


