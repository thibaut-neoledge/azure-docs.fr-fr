<properties
   pageTitle="Service Fabric Cluster Resource Manager - Stratégies de positionnement | Microsoft Azure"
   description="Vue d’ensemble des autres règles et stratégies de positionnement pour les services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# Stratégies de positionnement pour les services Service Fabric
Il existe de nombreuses règles supplémentaires qui peuvent vous intéresser si votre cluster Service Fabric s’étend sur plusieurs zones géographiques, par exemple plusieurs centres de données ou régions Azure, ou si votre environnement s’étend sur plusieurs zones de contrôle géopolitique (ou d’autres cas de figure avec des limites juridiques ou en matière de stratégie, ou encore si les distances ont un impact réel sur la latence ou les performances). La plupart d’entre elles peuvent être configurées par le biais des propriétés de nœud et des contraintes de positionnement, mais certaines sont plus complexes. Pour simplifier les choses, nous fournissons ces commandes supplémentaires. Tout comme d’autres contraintes de placement, les stratégies de positionnement peuvent être configurées dans chaque instance de service nommé.

## Spécification de domaines non valides
La stratégie de positionnement InvalidDomain vous permet de spécifier qu’un domaine d’erreur particulier n’est pas valide pour cette charge de travail. Cette stratégie garantit qu’un service particulier ne s’exécute jamais dans une zone particulière, par exemple pour des raisons de stratégie géopolitiques ou d’entreprise. Vous pouvez spécifier plusieurs domaines non valides par le biais de différentes stratégies.

![Exemple de domaine non valide][Image1]

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
## Spécification de domaines obligatoires
La stratégie de positionnement de domaine obligatoire requiert que tous les réplicas à état ou les instances de service sans état figurent dans le domaine spécifié pour le service. Vous pouvez spécifier plusieurs domaines obligatoires par le biais de différentes stratégies.

![Exemple de domaine requis][Image2]

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

## Spécification d’un domaine préféré pour les réplicas principaux
Le domaine principal préféré est un contrôle intéressant, car il permet dans la mesure du possible de sélectionner le domaine d’erreur dans lequel le serveur principal doit figurer. Lorsque tout est sain, le serveur principal se retrouve dans ce domaine. Si le domaine ou le réplica principal échoue ou est arrêté pour une raison quelconque, le serveur principal est migré vers un autre emplacement. Si cet emplacement ne se trouve pas dans le domaine préféré, le Cluster Resource Manager le redéplace si possible dans le domaine préféré. Naturellement, ce paramètre convient uniquement aux services avec état. Cette stratégie est particulièrement utile dans les clusters qui sont fractionnés entre plusieurs régions Azure ou centres de données. Dans ce cas, vous utilisez tous les emplacements pour assurer la redondance, mais vous préférez que les réplicas principaux soient placés dans un emplacement donné afin de fournir une plus faible latence pour les opérations destinées au serveur principal (les accès en écriture, ainsi que, par défaut, tous les accès en lecture sont pris en charge par le serveur principal).

![Basculement et domaines principaux préférés][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## Demander à ce que les réplicas soient répartis entre tous les domaines et interdire la compression
Une autre stratégie que vous pouvez spécifier consiste à exiger que les réplicas soient toujours distribués dans les domaines d’erreur disponibles. Cela se produit par défaut dans la plupart des cas où le cluster est intègre. Cependant, il existe des cas dégénérés dans lesquels les réplicas pour une partition donnée peuvent être temporairement empaquetés dans un domaine de mise à niveau ou d’erreur. Par exemple, supposons que, même si le cluster a 9 nœuds dans 3 domaines d’erreur (0, 1 et 2) et votre service a 3 réplicas, les nœuds qui ont été utilisés pour ces réplicas dans les domaines d’erreur 1 et 2 ont connu une défaillance, et qu’aucun autre nœud dans ces domaines n’est valide en raison de problèmes de capacité. Si Service Fabric crée des remplacements pour ces réplicas, le cluster Resource Manager doit les placer dans le domaine d’erreur 0, ce qui crée une situation dans laquelle la contrainte de domaine d’erreur est violée. Il existe également une grande chance que le jeu entier de réplicas soit perdu (si FD 0 devait être définitivement perdu). (Pour plus d’informations sur les contraintes et les priorités de contrainte en général, consultez [cette rubrique](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities) )

Si vous avez déjà vu un avertissement d’intégrité tel que « Le Load Balancer a détecté une violation de contrainte pour ce réplica  : fabric : / < nom de certains services > Partition secondaire < certains ID de partition > viole la contrainte : FaultDomain », vous avez rencontré cette situation ou quelque chose de semblable. Ces situations sont généralement temporaires (les nœuds ne restent pas longtemps inactifs, ou, si c’est le cas et que nous devons générer des remplacements, il existe d’autres nœuds dans les domaines d’erreur corrects qui sont valides), mais il existe certaines charges de travail pour lesquelles la disponibilité peut entraîner un risque de perte de tous les réplicas. Pour cela, nous pouvons spécifier la stratégie « RequireDomainDistribution », ce qui garantit que deux réplicas de la même partition ne sont jamais autorisés dans le même domaine d’erreur ou de mise à niveau.

Pour certaines charges de travail, le nombre cible de réplicas (copies de l’état) est préférable à tout moment (en espérant qu’aucune défaillances complète du domaine ne se produira et en sachant qu’elles peuvent généralement récupérer l’état local), tandis que d’autres encourent plutôt un temps d’arrêt que de risquer des problèmes d’exactitude et une perte de données. Étant donné que la plupart des charges de travail de production exécutent plus de 3 réplicas, le comportement par défaut consiste à ne pas exiger la distribution de domaines et de permettre à l’équilibrage et au basculement de gérer normalement les différents cas de figure, même si cela signifie que plusieurs réplicas sont temporairement compressés dans un domaine.

Code :

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Est-il possible d’utiliser ces configurations pour les services d’un cluster qui ne sont pas répartis géographiquement ? Bien sûr. Mais il n’existe aucune raison particulière de le faire. En particulier, évitez les configurations de domaine préféré requises et non valides, sauf si vous exécutez réellement un cluster fractionné géographiquement. Il n’y a aucun sens à tenter de forcer une charge de travail à s’exécuter dans un seul rack ou à préférer certains segments de votre cluster local plutôt que d’autres sauf s’il existe différents types de matériel ou différentes segmentations de la charge de travail. Ces cas peuvent être gérés par le biais de contraintes de placement normales.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0824_2016-->