---
title: "Gouvernance des ressources Azure Service Fabric pour les conteneurs et les services | Microsoft Docs"
description: "Azure Service Fabric vous permet de spécifier des limites de ressources pour les services en cours d’exécution à l’intérieur ou à l’extérieur de conteneurs."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 62ce17533f8c195b873431089e1f1f47cb0bcbec
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---

# <a name="resource-governance"></a>Gouvernance des ressources 

Lors de l’exécution de plusieurs services sur le même nœud ou cluster, il est possible qu’un service consomme davantage de ressources au détriment des autres services. C’est ce que l’on appelle le problème du « voisin bruyant ». Service Fabric permet au développeur de spécifier des réservations et des limites par service afin de garantir des ressources et également de limiter son utilisation des ressources.

>
> Avant de poursuivre la lecture de cet article, vous avez tout intérêt à vous familiariser avec le [modèle d’application Service Fabric](service-fabric-application-model.md) et le [modèle d’hébergement Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Mesures de la gouvernance des ressources 

Dans Service Fabric, la gouvernance des ressources est prise en charge par [package de service](service-fabric-application-model.md). Les ressources qui sont affectées au package de service peuvent être subdivisées entre les packages de code. Les limites de ressources spécifiées impliquent également la réservation des ressources. Service Fabric prend en charge la spécification des ressources processeur et mémoire par package de service à l’aide de deux [mesures](service-fabric-cluster-resource-manager-metrics.md) intégrées :

* Processeur (nom de la mesure `servicefabric:/_CpuCores`) : un cœur est un cœur logique disponible sur l’ordinateur hôte, et tous les cœurs de l’ensemble des nœuds sont pondérés de la même manière.
* Mémoire (nom de la mesure `servicefabric:/_MemoryInMB`) : la mémoire est exprimée en mégaoctets et elle est mise en correspondance avec la mémoire physique disponible sur l’ordinateur.

Pour ces deux mesures, le [Gestionnaire de ressources de cluster](service-fabric-cluster-resource-manager-cluster-description.md) surveille la capacité totale du cluster, la charge sur chaque nœud du cluster ainsi que les ressources restantes dans le cluster. Ces deux mesures équivalent à n’importe quelle autre mesure utilisateur ou personnalisée et peuvent être utilisées avec toutes les fonctionnalités existantes :
* Le cluster peut être [équilibré](service-fabric-cluster-resource-manager-balancing.md) en fonction de ces deux mesures (comportement par défaut).
* Le cluster peut être [défragmenté](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en fonction de ces deux mesures.
* Lors de la [description d’un cluster](service-fabric-cluster-resource-manager-cluster-description.md), la capacité mise en mémoire tampon peut être définie pour ces deux mesures.

Le [rapport de charge dynamique](service-fabric-cluster-resource-manager-metrics.md) n’est pas pris en charge pour ces mesures et les charges de ces mesures sont définies lors de la création.

## <a name="resource-governance-mechanism"></a>Mécanisme de la gouvernance des ressources

Pour l’heure, le runtime Service Fabric n’assure pas la réservation de ressources. Quand un processus ou un conteneur est ouvert, le runtime définit les limites des ressources en fonction des charges définies au moment de la création. De plus, le runtime rejette l’ouverture de nouveaux packages de services quand la quantité de ressources disponibles est dépassée. Pour mieux comprendre le fonctionnement du processus, prenons l’exemple d’un nœud constitué de 2 cœurs de processeur (le mécanisme de la gouvernance de mémoire est comparable) :

1. Dans un premier temps, un conteneur est placé sur le nœud, demandant 1 cœur de processeur. Le runtime ouvre le conteneur et définit la limite de processeur à 1 cœur. Ainsi, le conteneur ne peut pas utiliser plus de 1 cœur.
2. Ensuite, un réplica de service est placé sur le nœud, et le package de services correspondant spécifie une limite de 1 cœur de processeur. Le runtime ouvre le package de code et définit sa limite de processeur à 1 cœur.

À ce stade, la somme des limites correspond à la capacité du nœud, et un processus et un conteneur s’exécutent avec 1 cœur chacun et n’interfèrent pas entre eux. Service Fabric ne place pas d’autres conteneurs ou réplicas s’ils spécifient une limite de processeur. Cependant, il existe deux situations où d’autres processus peuvent entrer en concurrence pour un processeur, et un processus et un conteneur de notre exemple peuvent faire l’expérience du syndrome du voisin bruyant :

* Combinaison de services et de conteneurs régis et non régis : si l’utilisateur crée un service sans spécifier une quelconque gouvernance des ressources, le runtime considère qu’il ne consomme pas de ressources et peut donc le placer sur le nœud de notre exemple. Dans ce cas, ce nouveau processus consomme en réalité certaines ressources de processeur aux dépens des services qui s’exécutent déjà sur le nœud. La solution à ce problème consiste soit à ne pas combiner de services régis et non régis dans le même cluster, soit à utiliser des [contraintes de placement](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) pour éviter qu’ils se trouvent sur le même ensemble de nœuds.
* Dans le cas où un autre processus démarre sur le nœud, en dehors de Service Fabric (par exemple, un service du système d’exploitation), ce processus entre alors aussi en concurrence avec les services existants pour le processeur. La solution à ce problème consiste à configurer correctement la capacité des nœuds de façon à prendre en compte la surcharge du système d’exploitation, comme indiqué dans la section suivante.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuration du cluster pour l’activation de la gouvernance des ressources

Quand le nœud démarre et rejoint le cluster, Service Fabric détecte la quantité de mémoire disponible et le nombre de cœurs disponibles et définit la capacité des nœuds pour ces deux ressources. Pour ménager un peu d’espace de mémoire tampon pour le système d’exploitation et les autres processus susceptibles de s’exécuter sur le nœud, Service Fabric utilise seulement 80 % des ressources disponibles sur le nœud. Ce pourcentage est configurable et peut être modifié dans le manifeste du cluster. Voici un exemple qui montre comment ordonner à Service Fabric d’utiliser 50 % de la capacité de processeur disponible et 70 % de la quantité de mémoire disponible : 

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Dans les cas où il est nécessaire de procéder à une configuration entièrement manuelle de la capacité des nœuds, il est aussi possible d’utiliser le mécanisme standard de description des nœuds du cluster. Voici un exemple de configuration du nœud avec 4 cœurs et 2 Go de mémoire : 

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Quand la détection automatique des ressources disponibles est activée et que la capacité du nœud est définie manuellement dans le manifeste du cluster, Service Fabric vérifie si le nœud a suffisamment de ressources pour prendre en charge la capacité définie par l’utilisateur :
* Si la capacité du nœud définie dans le manifeste est inférieure ou égale aux ressources disponibles sur le nœud, Service Fabric utilise la capacité spécifiée dans le manifeste.
* Si la capacité du nœud définie dans le manifeste est supérieure aux ressources disponibles, Service Fabric utilise les ressources disponibles comme capacité du nœud.

La détection automatique des ressources disponibles peut être entièrement désactivée si elle n’est pas nécessaire en modifiant le paramètre suivant :

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Pour des performances optimales, le paramètre suivant doit également être activé dans le manifeste du cluster : 

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" /> 
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```


## <a name="specifying-resource-governance"></a>Spécification de la gouvernance des ressources 

Les limites de gouvernance des ressources sont spécifiées dans le manifeste d’application (section ServiceManifestImport), comme indiqué dans l’exemple suivant :

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'>
  <Parameters>
  </Parameters>
  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric will sum the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```
  
Dans cet exemple, le package de service ServicePackageA obtient un cœur sur les nœuds où il est placé. Ce package de service contient deux packages de code (CodeA1 et CodeA2), et tous deux spécifient le paramètre `CpuShares`. La proportion de CpuShares 512:256 divise le cœur entre les deux packages de code. Dans cet exemple, CodeA1 obtient donc les deux tiers d’un cœur et CodeA2 obtient un tiers d’un cœur (avec une garantie de réservation identique). Lorsque les CpuShares ne sont pas spécifiés pour les packages de code, Service Fabric divise les cœurs équitablement entre eux.

Les limites de mémoire sont absolues, ce qui signifie que les deux packages de code sont limités à 1 024 Mo de mémoire (avec une garantie de réservation identique). Les packages de code (conteneurs ou processus) ne sont pas en mesure d’allouer plus de mémoire que cette limite. Toute tentative en ce sens conduit à une exception de mémoire insuffisante. Pour pouvoir appliquer la limite de ressources, des limites de mémoire doivent être spécifiées pour tous les packages de code au sein d’un package de service.

## <a name="other-resources-for-containers"></a>Autres ressources pour les conteneurs
En plus du processeur et de la mémoire, il est possible de spécifier d’autres limites de ressources pour les conteneurs. Ces limites sont spécifiées au niveau du package de code et s’appliquent au démarrage du conteneur. Contrairement au processeur et à la mémoire, Cluster Resource Manager ne perçoit pas ces ressources et n’assure aucune tâche de vérification de ressources ou d’équilibrage de charge sur celles-ci. 

* MemorySwapInMB : quantité de mémoire d’échange qu’un conteneur peut utiliser.
* MemoryReservationInMB : limite non stricte de la gouvernance de mémoire qui s’applique uniquement quand une contention de mémoire est détectée sur le nœud.
* CpuPercent : pourcentage de la capacité du processeur que le conteneur peut utiliser. Si des limites de processeur sont spécifiées pour le package de services, ce paramètre est en réalité ignoré.
* MaximumIOps : nombre maximal d’E/S par seconde qu’un conteneur peut utiliser (en lecture et écriture).
* MaximumIOBytesps : nombre maximal d’E/S (octets par seconde) qu’un conteneur peut utiliser (en lecture et écriture).
* BlockIOWeight : poids des E/S de bloc par rapport aux autres conteneurs.

Ces ressources peuvent être combinées avec le processeur et la mémoire. Voici un exemple illustration la définition de ressources supplémentaires pour des conteneurs :

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur le Gestionnaire de ressources de cluster, lisez cet [article](service-fabric-cluster-resource-manager-introduction.md).
* Pour en savoir plus sur le modèle d’application, les packages de service, les packages de code et leur mappage avec les réplicas, consultez cet [article](service-fabric-application-model.md).

