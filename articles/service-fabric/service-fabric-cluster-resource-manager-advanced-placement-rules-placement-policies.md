---
title: "Service Fabric Cluster Resource Manager - Stratégies de positionnement | Microsoft Docs"
description: "Vue d’ensemble des autres règles et stratégies de positionnement pour les services Service Fabric"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dafaf29b6827a6f1c043af3d6bfe62d480d31ad5
ms.openlocfilehash: 7fa35de8b99132ad1c10229a9bb2231f11fdbaa0


---
# <a name="placement-policies-for-service-fabric-services"></a>Stratégies de positionnement pour les services Service Fabric
Il existe de nombreuses règles supplémentaires que vous devrez peut-être configurer dans certains cas rares. Voici quelques exemples de ces scénarios :
* Si votre cluster Service Fabric est réparti sur différentes zones géographiques, par exemple avec plusieurs centres de données locaux ou dans différentes régions Azure
* Si votre environnement s’étend sur plusieurs zones de contrôle géopolitique (ou d’autres cas de figure avec des limites juridiques ou en matière de stratégie)
* Il existe des points à prendre en compte pour les performances/la latence réelles en raison des grandes distances parcourues pour les communications dans cluster, ou du passage par certains réseaux plus lents ou moins fiables.

Dans de telles situations, il peut être important qu’un service donné s’exécute toujours ou jamais dans certaines régions. De même, il peut être important d’essayer de placer le serveur principal dans une région déterminée pour réduire la latence pour l’utilisateur final.

Les stratégies de placement avancées sont les suivantes :

1. Domaines non valides
2. Domaines requis
3. Domaines par défaut
4. Désactivation de la compression de réplica

La plupart des commandes suivantes peuvent être configurées par le biais des propriétés de nœud et des contraintes de positionnement, mais certaines sont plus complexes. Pour simplifier les choses, Service Fabric Cluster Resource Manager fournit ces stratégies de positionnement supplémentaires. Tout comme d’autres contraintes de placement, les stratégies de positionnement peuvent être configurées dans chaque instance de service nommé et mises à jour de façon dynamique.

## <a name="specifying-invalid-domains"></a>Spécification de domaines non valides
La stratégie de positionnement InvalidDomain vous permet de spécifier qu’un domaine d’erreur particulier n’est pas valide pour cette charge de travail. Cette stratégie garantit qu’un service particulier ne s’exécute jamais dans une zone particulière, par exemple pour des raisons de stratégie géopolitiques ou d’entreprise. Vous pouvez spécifier plusieurs domaines non valides par le biais de différentes stratégies.

<center>
![Exemple de domaine non valide][Image1]
</center>

Code :

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Spécification de domaines obligatoires
La stratégie de positionnement de domaine obligatoire requiert que tous les réplicas à état ou les instances de service sans état figurent dans le domaine spécifié pour le service. Vous pouvez spécifier plusieurs domaines obligatoires par le biais de différentes stratégies.

<center>
![Exemple de domaine requis][Image2]
</center>

Code :

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas"></a>Spécification d’un domaine préféré pour les réplicas principaux
Le domaine principal préféré est un contrôle intéressant, car il permet dans la mesure du possible de sélectionner le domaine d’erreur dans lequel le serveur principal doit figurer. Le domaine principal se retrouve dans ce domaine lorsque tout est normal. Si le domaine ou le réplica principal échoue ou est arrêté pour une raison quelconque, le serveur principal est migré vers un autre emplacement. Si ce nouvel emplacement ne se trouve pas dans le domaine préféré, le Cluster Resource Manager le redéplace dans le domaine préféré dès que possible. Naturellement, ce paramètre convient uniquement aux services avec état. Cette stratégie est particulièrement utile dans les clusters qui sont répartis entre plusieurs centres de données ou régions Azure, lorsque vous préférez que les réplicas principaux soient placés dans un emplacement donné. Conserver les serveurs principaux à proximité des utilisateurs permet de proposer une latence plus faible, en particulier pour les lectures.

<center>
![Basculement et domaines principaux préférés][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replicas-to-be-distributed-among-all-domains-and-disallowing-packing"></a>Demander à ce que les réplicas soient répartis entre tous les domaines et interdire la compression
Les réplicas sont _normalement_ distribués sur les domaines lorsque le cluster est intègre, mais il existe des cas où les réplicas pour une partition donnée peuvent se retrouver temporairement rassemblés dans un seul domaine. Par exemple, supposons que le cluster comporte neuf nœuds dans trois domaines d’erreur (fd:/0, fd:/1 et fd:/2) et que votre service a trois réplicas. Supposons que les nœuds utilisés pour ces réplicas dans fd:/1 et fd:/2 sont injoignables. En temps normal, Cluster Resource Manager préférerait les autres nœuds dans ces mêmes domaines d’erreur. Dans ce cas, supposons qu’en raison de problèmes de capacité, aucun des autres nœuds dans ces domaines n’est valide. Si Cluster Resource Manager génère des réplicas de remplacement, il devra choisir des nœuds dans fd:/0. Toutefois, faire _cela_ créerait une situation dans laquelle la contrainte de domaine d’erreur est enfreinte. Il existe également une grande chance que le jeu entier de réplicas soit inaccessible ou perdu (si FD 0 devait être définitivement perdu). Pour plus d’informations sur les contraintes et les priorités de contraintes en général, consultez [cette rubrique](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).

Si vous avez déjà vu un avertissement d’intégrité comme `The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`, vous avez rencontré cette situation ou quelque chose de semblable. Ce cas est rare, mais il peut se produire, et ces situations sont généralement temporaires étant donné que les nœuds redeviennent disponibles. Si les nœuds restent indisponibles et que Cluster Resource Manager doit générer des remplacements, il existe en général d’autres nœuds disponibles dans les domaines d’erreur idéaux.

Pour certaines charges de travail, il est préférable d’avoir toujours le nombre cible de réplicas, même si ces derniers sont rassemblés dans moins de domaines. Ces charges de travail sont supposent que des pannes de domaine simultanées totales ne surviendront pas, et peuvent généralement récupérer l’état local. D’autres charges de données seront indisponibles plus rapidement pour éviter les erreurs et pertes de données. Étant donné que la plupart des charges de travail s’exécutent avec plus de trois réplicas, plus de trois domaines d’erreur et plusieurs nœuds valides par domaine d’erreur, la valeur par défaut est de ne pas exiger la distribution sur les domaines. Cette option laisse l’équilibrage et le basculement normaux gérer ces cas, même si cela implique qu’un domaine peut temporairement contenir plusieurs réplicas.

Si vous souhaitez désactiver ce type de regroupement pour une charge de travail spécifique, vous pouvez spécifier la stratégie « RequireDomainDistribution » sur le service. Lorsque cette stratégie est définie, Cluster Resource Manager garantit que deux réplicas de la même partition ne sont jamais autorisées dans le même domaine d’erreur ou de mise à niveau.

Code :

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Est-il possible d’utiliser ces configurations pour les services d’un cluster qui ne sont pas répartis géographiquement ? Bien sûr. Mais il n’y a pas de bonne raison de le faire. La configuration de domaines requis, non valides et préférés doit être évitée, sauf si vous exécutez réellement un cluster réparti sur plusieurs zones géographiques. Il n’y a aucune raison de tenter de forcer une charge de travail à s’exécuter dans un seul rack ou de préférer un segment de votre cluster local plutôt qu’un autre. Différentes configurations matérielles doivent être réparties sur plusieurs domaines, et ceux-ci sont gérés par les contraintes de placement et les propriétés de nœud normales.

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la page [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png



<!--HONumber=Jan17_HO1-->


