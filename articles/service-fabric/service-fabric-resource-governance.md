---
title: "Gouvernance des ressources Azure Service Fabric pour les conteneurs et les services | Microsoft Docs"
description: "Azure Service Fabric vous permet de spécifier des limites de ressources pour les services en cours d’exécution à l’intérieur ou à l’extérieur de conteneurs."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: e490dfba28b8a270bf0e6022333f148c965bc6dc
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="resource-governance"></a>Gouvernance des ressources 

Lors de l’exécution de plusieurs services sur le même nœud ou cluster, il est possible qu’un service consomme davantage de ressources au détriment des autres services. C’est ce que l’on appelle le problème du « voisin bruyant ». Service Fabric permet au développeur de spécifier des réservations et des limites par service afin de garantir des ressources et également de limiter son utilisation des ressources. 

## <a name="resource-governance-metrics"></a>Mesures de la gouvernance des ressources 

Dans Service Fabric, la gouvernance des ressources est prise en charge par [package de service](service-fabric-application-model.md). Les ressources qui sont affectées au package de service peuvent être subdivisées entre les packages de code. Les limites de ressources spécifiées impliquent également la réservation des ressources. Service Fabric prend en charge la spécification des ressources processeur et mémoire par package de service à l’aide de deux [mesures](service-fabric-cluster-resource-manager-metrics.md) intégrées :

* Processeur (nom de la mesure `ServiceFabric:/_CpuCores`) : un cœur est un cœur logique disponible sur l’ordinateur hôte, et tous les cœurs de l’ensemble des nœuds sont pondérés de la même manière.
* Mémoire (nom de la mesure `ServiceFabric:/_MemoryInMB`) : la mémoire est exprimée en mégaoctets et elle est mise en correspondance avec la mémoire physique disponible sur l’ordinateur.

Seules les réservations réversibles sont garanties : le runtime refuse l’ouverture de nouveaux packages de service en cas de dépassement des ressources disponibles. Toutefois, si un autre exécutable ou conteneur est placé sur le nœud, cela peut violer les garanties de réservation d’origine.

Pour ces deux mesures, le [Gestionnaire de ressources de cluster](service-fabric-cluster-resource-manager-cluster-description.md) surveille la capacité totale du cluster, la charge sur chaque nœud du cluster ainsi que les ressources restantes dans le cluster. Ces deux mesures équivalent à n’importe quelle autre mesure utilisateur ou personnalisée et peuvent être utilisées avec toutes les fonctionnalités existantes :
* Le cluster peut être [équilibré](service-fabric-cluster-resource-manager-balancing.md) en fonction de ces deux mesures (comportement par défaut).
* Le cluster peut être [défragmenté](service-fabric-cluster-resource-manager-defragmentation-metrics.md) en fonction de ces deux mesures.
* Lors de la [description d’un cluster](service-fabric-cluster-resource-manager-cluster-description.md), la capacité mise en mémoire tampon peut être définie pour ces deux mesures.

Le [rapport de charge dynamique](service-fabric-cluster-resource-manager-metrics.md) n’est pas pris en charge pour ces mesures et les charges de ces mesures sont définies lors de la création.

## <a name="cluster-set-up-for-enabling-resource-governance"></a>Configuration du cluster pour l’activation de la gouvernance des ressources

La capacité doit être définie manuellement dans chaque type de nœud du cluster, comme suit :

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="ServiceFabric:/_CpuCores" Value="4"/>
        <Capacity Name="ServiceFabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```
 
La gouvernance des ressources est autorisée uniquement sur les services de l’utilisateur, et non sur les services système. Lorsque vous spécifiez la capacité, vous devez désallouer certains cœurs et une partie de la mémoire pour les services système. Pour des performances optimales, le paramètre suivant doit également être activé dans le manifeste du cluster : 

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


## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur le Gestionnaire de ressources de cluster, lisez cet [article](service-fabric-cluster-resource-manager-introduction.md).
* Pour en savoir plus sur le modèle d’application, les packages de service, les packages de code et leur mappage avec les réplicas, consultez cet [article](service-fabric-application-model.md).

