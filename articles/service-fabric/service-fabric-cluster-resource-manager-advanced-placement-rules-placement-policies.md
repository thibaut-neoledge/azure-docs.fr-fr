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
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Stratégies de positionnement pour les services Service Fabric
Il existe de nombreuses règles supplémentaires qui peuvent vous intéresser si votre cluster Service Fabric s’étend sur plusieurs zones géographiques, par exemple plusieurs centres de données ou régions Azure, ou si votre environnement s’étend sur plusieurs zones de contrôle géopolitique (ou d’autres cas de figure avec des limites juridiques ou en matière de stratégie). La plupart d’entre elles peuvent être configurées par le biais des propriétés de nœud et des contraintes de positionnement (dont nous avons parlé plus haut), mais certaines sont plus complexes. En tout état de cause, nous vous les fournissons. Tout comme les contraintes de positionnement, les stratégies de positionnement peuvent être configurées dans chaque service.

## Spécification de domaines non valides
La stratégie de positionnement InvalidDomain vous permet de spécifier qu’un domaine d’erreur particulier n’est pas valide pour cette charge de travail. Cette stratégie garantit qu’un service particulier ne s’exécute jamais dans une zone particulière, par exemple pour des raisons de stratégie géopolitiques ou d’entreprise. Vous pouvez spécifier plusieurs domaines non valides.

![Exemple de domaine non valide][Image1]

Code :

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Spécification de domaines obligatoires
La stratégie de positionnement de domaine obligatoire requiert que tous les réplicas figurent dans le domaine spécifié. Vous pouvez spécifier plusieurs domaines obligatoires non valides.

![Exemple de domaine requis][Image2]

Code :

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## Spécification d’un domaine préféré pour les réplicas principaux
Le domaine principal préféré est un contrôle intéressant, car il permet dans la mesure du possible de sélectionner le domaine d’erreur dans lequel le serveur principal doit figurer. Lorsque tout est sain, le serveur principal se retrouve dans ce domaine. Si le domaine ou le réplica principal échoue ou est arrêté pour une raison quelconque, le serveur principal est migré vers un autre emplacement. Le cas échéant, il est possible de basculer à nouveau vers le domaine préféré. Naturellement, ce paramètre convient uniquement aux services avec état. Cette stratégie est particulièrement utile dans les clusters fractionnés géographiquement où vous utilisez d’autres emplacements pour assurer la redondance, mais vous préférez que les réplicas principaux soient placés dans un emplacement donné afin de fournir une plus faible latence pour les opérations destinées au serveur principal (les accès en écriture et également par défaut tous les accès en lecture sont pris en charge par le serveur principal).

![Basculement et domaines principaux préférés][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Demander à ce que les réplicas soient répartis entre tous les domaines et interdire la compression
Une autre stratégie que vous pouvez spécifier est de demander la répartition systématique des réplicas entre les domaines disponibles. Notez que dans l’exemple ci-dessus, il est possible que plusieurs réplicas (formant un quorum) soient compressés temporairement dans le même centre de données. Dans ce cas, ce n’est vraisemblablement pas un problème, car le serveur principal n’est pas dans le même contrôleur de domaine que la majorité des réplicas, mais cela indique qu’il est possible que nous soyons temporairement soumis à une configuration que nous aurions voulu éviter. Cela dépend d’autres conditions dans le cluster à la fois. Vous pouvez toutefois éviter ce cas de figure en vérifiant que vos réplicas sont toujours situés dans des domaines distincts. N’oubliez pas que si vous sélectionnez cette option, vous obtiendrez des temps d’arrêt/échecs plutôt que d’autoriser le service à poursuivre son exécution sur les nœuds restants. La stratégie RequireDomainDistribution configure ce comportement. Notez toutefois que cela peut signifier que nous ne positionnons aucun réplica si cela entraîne une compression. Cela signifie que vous fonctionnez temporairement en dessous du nombre cible de réplicas tant que les nœuds d’un domaine viable ne sont pas revenus. Certains clients préfèrent avoir le nombre cible de réplicas (copies de l’état) à tout moment alors que d’autres ne veulent pas rencontrer de problèmes de disponibilité. Si nous compressons les réplicas dans un domaine d’erreur et que ce domaine échoue, tous les réplicas sont perdus simultanément. Pour un petit nombre de réplicas, cela peut entraîner une perte de quorum. Dans le cas peu probable où le domaine ne revienne jamais, cela peut entraîner une perte de données. Étant donné que la plupart des utilisateurs exécutent plus de 3 réplicas, le comportement par défaut est de ne pas exiger la distribution de domaines et de permettre à l’équilibrage et au basculement de gérer normalement les différents cas de figure, même si cela signifie que plusieurs réplicas sont temporairement compressés dans un domaine.

Code :

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell :

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Est-il possible d’utiliser ces configurations pour les services d’un cluster qui ne sont pas répartis géographiquement ? Bien sûr. Mais cela n’est absolument pas nécessaire. Plus spécifiquement, la configuration de domaines requis, non valides et préférés doit être évitée, sauf si vous exécutez réellement un cluster réparti géographiquement.

## Étapes suivantes
- Pour plus d’informations sur les autres options disponibles pour la configuration des services, consultez la rubrique sur les autres configurations de Cluster Resource Manager disponibles [En savoir plus sur la configuration des services](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0316_2016-->