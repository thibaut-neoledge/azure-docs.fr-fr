---
title: "Intégration de Service Fabric Cluster Resource Manager et de Service Fabric Management | Microsoft Docs"
description: "Vue d’ensemble des points d’intégration entre Cluster Resource Manager et Service Fabric Management."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: b2428f93680c12c76000fa8eb1a7138f72a8efe7
ms.openlocfilehash: 9d67f089f4aba03e846a8fe020a91b6b1ac6ea48
ms.lasthandoff: 02/11/2017


---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Intégration de Cluster Resource Manager à la gestion de cluster Service Fabric
Dans Service Fabric, le composant Cluster Resource Manager n’a pas pour fonction principale de traiter les opérations de gestion (telles que les mises à niveau d’application). Toutefois, il y participe. La première manière dont Cluster Resource Manager aide à la gestion consiste à suivre l’état souhaité du cluster et des services qu’il contient. Cluster Resource Manager envoie des rapports d’intégrité lorsqu’il ne peut pas placer le cluster dans la configuration souhaitée. Par exemple : en cas de capacité insuffisante ou de règles conflictuelles sur l’emplacement d’un service. Le fonctionnement des mises à niveau est un autre composant de l’intégration. Lors des mises à jour, Cluster Resource Manager modifie légèrement son comportement. Nous allons parler de ces deux aspects de l’intégration ci-dessous.

## <a name="health-integration"></a>Intégration de l’intégrité
Cluster Resource Manager suit en permanence les règles que vous avez définies pour vos services et les capacités disponibles sur les nœuds et dans le cluster. S’il ne peut pas respecter ces règles ou si la capacité est insuffisante, des erreurs et des avertissements sont émis. Par exemple, si la capacité d’un nœud est dépassée, Cluster Resource Manager tente de résoudre la situation en déplaçant des services. S’il ne peut pas résoudre le problème, il émet un avertissement d’intégrité indiquant pour quel nœud la capacité a été dépassée, ainsi que les mesures concernées.

Les violations des contraintes de placement constituent un autre exemple des avertissements d’intégrité de Resource Manager. Par exemple, si vous avez défini une contrainte de placement (comme `“NodeColor == Blue”`) et que Resource Manager détecte une violation de cette contrainte, il émet un avertissement d’intégrité. Cela s’applique aux contraintes personnalisées et aux contraintes par défaut (comme les contraintes de domaine d’erreur et de mise à niveau).

Voici un exemple de rapport d’intégrité. Dans ce cas, le rapport d’intégrité concerne l’une des partitions de service du système. Le message d’intégrité indique que les réplicas de cette partition sont temporairement empaquetés dans des domaines de mise à niveau trop peu nombreux.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Voici ce que nous apprend ce message d’intégrité :

1. Tous les réplicas sont intègres (c’est la priorité numéro un de Service Fabric).
2. La contrainte de distribution de domaine de mise à niveau n’est pas respectée (ce qui signifie qu’un domaine de mise à niveau particulier comprend plus de réplicas que prévu pour cette partition).
3. Le nœud qui contient le réplica à l’origine de la violation (nœud avec l’ID 3d1a4a68b2592f55125328cd0f8ed477).
4. La date et l’heure du rapport (8/10/2015 19:13:02)

Ces informations alimentent des alertes qui se déclenchent en production pour vous indiquer qu’un problème est survenu. Dans ce cas, nous aimerions savoir pourquoi Resource Manager a décidé de placer les réplicas dans le domaine de mise à niveau. Peut-être que les nœuds dans les autres domaines de mise à niveau étaient en panne, par exemple.

Supposons que vous vouliez créer un service, ou que Resource Manager tente de trouver un emplacement pour certains services, mais qu’aucune solution ne fonctionne. Plusieurs raisons peuvent expliquer ceci, mais l’une des deux conditions suivantes est souvent à l’origine du problème :

1. Une situation temporaire empêche de placer correctement l’instance ou le réplica de ce service.
2. Les exigences du service sont mal configurées et ne peuvent pas être respectées.

Dans chacune de ces conditions, un rapport d’intégrité Cluster Resource Manager vous donne des informations pour vous aider à déterminer ce qui se passe et à comprendre pourquoi le service ne peut pas être placé. On parle de « séquence d’élimination de contrainte » pour désigner ce processus. Au cours de ce processus, le système passe en revue les contraintes configurées affectant le service et les enregistrements qu’elles éliminent. Lorsque les services ne peuvent pas être placés, vous pouvez ainsi voir quels nœuds ont été éliminés et pour quelle raison.

## <a name="constraint-types"></a>Types de contrainte
Examinons les différentes contraintes qui apparaissent dans ces rapports d’intégrité. La plupart du temps, ces contraintes n’éliminent pas les nœuds, car elles sont configurées par défaut à un niveau souple ou d’optimisation. Vous pouvez cependant voir des messages d’intégrité liés à ces contraintes si elles sont configurées en tant que contraintes strictes ou dans le cas rare où elles provoquent l’élimination de nœuds.

* **ReplicaExclusionStatic** et **ReplicaExclusionDynamic** : cette contrainte indique qu’une recherche a détecté une situation dans laquelle deux réplicas avec état ou instances sans état de la même partition doivent être placés sur le même nœud (ce qui n’est pas autorisé). ReplicaExclusionStatic et ReplicaExclusionDynamic appliquent pratiquement la même règle. En fait, la contrainte ReplicaExclusionDynamic indique qu’il est impossible de placer un réplica à un emplacement donné parce qu’un réplica de la seule solution proposée s’y trouve déjà. La contrainte ReplicaExclusionStatic est différente, puisqu’elle indique non pas un conflit proposé, mais un conflit réel. Dans ce cas, il y a déjà un réplica sur le nœud. Déroutant ? Oui. Cela a-t-il beaucoup d’importance ? Non. Si vous constatez une séquence d’élimination de contrainte contenant la contrainte ReplicaExclusionDynamic ou ReplicaExclusionStatic, Cluster Resource Manager pense qu’il n’y a pas suffisamment de nœuds. Des contraintes ultérieures peuvent généralement indiquer la cause du nombre insuffisant de nœuds.
* **PlacementConstraint** : si vous voyez ce message, cela signifie que certains nœuds ne correspondant pas aux contraintes de placement du service ont été éliminés. Nous avons suivi les contraintes de placement actuellement configurées dans le cadre de ce message. Ceci est normal si des contraintes de placement sont définies. Toutefois, s’il existe un bogue dans la contrainte de placement à l’origine d’un trop grand nombre de nœuds éliminés, c’est à cet endroit que vous pouvez le constater.
* **NodeCapacity** : cette contrainte signifie que Cluster Resource Manager n’a pas pu placer les réplicas sur les nœuds indiqués pour ne pas occasionner de surcharge.
* **Affinity** : cette contrainte indique que les réplicas n’ont pas été placés sur les nœuds affectés pour ne pas enfreindre la contrainte Affinity. Plus d’informations sur la contrainte Affinity, lisez [cet article](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** et **UpgradeDomain** : cette contrainte élimine des nœuds si le placement du réplica sur les nœuds indiqués se traduit par une compression dans un domaine de mise à niveau ou d’erreur spécifique. Plusieurs exemples décrivant cette contrainte sont présentés dans la rubrique [Contraintes des domaines d’erreur et de mise à niveau, et comportement résultant](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation** : cette contrainte supprime des nœuds de la solution. Toutefois, comme elle a par défaut un rôle d’optimisation, vous ne devriez pas la voir. En outre, la contrainte d’emplacement par défaut est généralement présente au cours des mises à niveau. Au cours de la mise à niveau, elle est utilisée pour déplacer à nouveau les réplicas vers l’emplacement où ils étaient lorsque la mise à niveau a démarré.

### <a name="constraint-priorities"></a>Priorités de contrainte
Avec toutes ces contraintes, vous vous dites peut-être : « Je pense que, dans mon système, les contraintes les plus importantes sont les contraintes de placement. Il est fort possible que je ne respecte pas les autres contraintes, même liées à l’affinité et à la capacité, si cela me permet de toujours respecter les contraintes de placement. »

Eh bien, c’est possible ! Les contraintes peuvent être configurées en fonction de différents niveaux d’application, qui se résument le plus souvent à la valeur « strict » (0), « souple » (1), « optimisation » (2) et « désactivé » (-1). La plupart des contraintes définies ici sont strictes par défaut. Par exemple, la capacité, et presque toutes sont strictes ou souples.

Les différentes priorités de contrainte expliquent également pourquoi des avertissements de violation de contrainte s’affichent plus souvent que d’autres : nous sommes prêts à assouplir (violer) temporairement certaines contraintes. Ces niveaux ne signifient pas vraiment qu’une contrainte donnée sera enfreinte, mais seulement qu’il existe un ordre préférentiel d’application. Ceci permet à Cluster Resource Manager d’effectuer les bons compromis s’il est impossible de satisfaire toutes les contraintes.

Dans des situations avancées, les priorités des contraintes peuvent être modifiées. Supposons, par exemple, que vous vouliez vous assurer que la contrainte Affinity sera enfreinte pour résoudre les problèmes de capacité de nœud. Pour ce faire, vous pouvez définir la priorité de la contrainte Affinity sur « souple » (1) et laisser la contrainte Capacity sur « stricte » (0).

Les valeurs de priorité par défaut associées aux contraintes sont indiquées dans le fichier config:

ClusterManifest.xml

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Contraintes de domaine d’erreur et de domaine de mise à niveau
Cluster Resource Manager présente le souhait de conserver les services répartis entre les domaines d’erreur et de mise à niveau sous la forme d’une contrainte dans le moteur de Resource Manager. Pour plus d’informations sur leur utilisation, consultez l’article sur la [configuration de cluster](service-fabric-cluster-resource-manager-cluster-description.md).

Nous avons parfois dû être stricts concernant les domaines d’erreur et de mise à niveau afin d’éviter un problème. Nous avons également parfois dû les ignorer entièrement (bien que brièvement !). En règle générale, la flexibilité de l’infrastructure de priorité de contrainte a bien fonctionné, mais elle n’est pas souvent nécessaire. La plupart du temps, les priorités par défaut sont conservées. Les domaines de mise à niveau restent une contrainte souple. Cluster Resource Manager peut avoir à regrouper deux réplicas dans un domaine de mise à niveau pour faire face à une mise à niveau, des échecs ou des violations de contraintes. Normalement, cela ne se produit que lorsqu’il existe plusieurs défaillances ou toute autre attrition dans le système empêchant un positionnement correct. Si l’environnement est correctement configuré, toutes les contraintes, y compris les contraintes d’erreur et de mise à niveau, sont entièrement respectées, même pendant les mises à niveau. L’essentiel est que Cluster Resource Manager surveille vos contraintes et le signale immédiatement lorsqu’il détecte des violations.

## <a name="the-preferred-location-constraint"></a>La contrainte d’emplacement par défaut
La contrainte PreferredLocation est un peu différente : il s’agit de la seule contrainte définie sur « optimisation ». Nous utilisons cette contrainte lors de mises à niveau pour tenter de replacer les services là où nous les avions trouvés avant la mise à niveau. Il existe toutes sortes de raisons pour lesquelles cette méthode ne fonctionne pas dans la pratique, mais c’est une optimisation intéressante.

## <a name="upgrades"></a>Mises à niveau
Cluster Resource Manager est également utile durant les mises à niveau d’applications et de cluster, où il exécute deux tâches :

* s’assurer que les règles et les performances du cluster ne sont pas compromises ;
* essayer de faire en sorte que la mise à niveau se déroule sans problème.

### <a name="keep-enforcing-the-rules"></a>Application continue des règles
Le point principal à retenir est que les règles (les contraintes strictes, comme des contraintes de placement) sont toujours appliquées pendant des mises à niveau. Les contraintes de placement s’assurent que vos charges de travail s’exécutent uniquement où elles y sont autorisées, notamment pendant les mises à niveau. Si votre environnement compte de nombreuses contraintes, les mises à niveau peuvent prendre un certain temps. Cela est dû au fait que les options sont limitées pour déplacer un service si celui-ci (ou le nœud où il se trouve) doit être arrêté pour une mise à jour.

### <a name="smart-replacements"></a>Remplacements actifs
Lorsqu’une mise à niveau démarre, Resource Manager prend une capture instantanée de l’organisation actuelle du cluster. Lorsque chaque domaine de mise à niveau est terminé, il tente de rétablir la disposition d’origine. Ainsi, il existe au maximum deux transitions lors de la mise à niveau (le déplacement hors du nœud concerné et le replacement sur celui-ci). Le fait de rétablir le cluster tel qu’il était avant la mise à niveau garantit également que la mise à niveau n’affecte pas la disposition du cluster. Si le cluster était bien organisé avant la mise à niveau, il le sera également après, ou du moins son organisation ne sera pas pire.

### <a name="reduced-churn"></a>Évolution limitée
Pendant les mises à niveau, Cluster Resource Manager désactive l’équilibrage de l’entité mise à niveau. Cela signifie que si vous avez deux instances d’application différentes et que vous lancez une mise à niveau sur l’une d’entre elles, l’équilibrage est suspendu sur cette instance d’application, mais pas sur l’autre. Le fait de suspendre l’équilibrage empêche les réactions inutiles envers la mise à niveau, comme le déplacement de services vers des nœuds qui ont été vidés pour la mise à niveau. Si la mise à niveau en question est une mise à niveau du cluster, alors l’ensemble du cluster n’est pas équilibré au cours de la mise à niveau. Les vérifications de contrainte (le fait de s’assurer que les règles sont appliquées) restent actives. Seul le rééquilibrage est désactivé.

### <a name="relaxed-rules"></a>Règles assouplies
En règle générale, vous souhaitez que la mise à niveau se termine même si le cluster est limité ou complet. Lors des mises à niveau, il est encore plus important que d’habitude de gérer la capacité du cluster. En effet, la capacité baisse environ de 5 à 20 % lorsque la mise à niveau est déployée sur le cluster. Ce travail doit donc généralement être dirigé vers un autre emplacement. C’est là que la notion de [capacité de mise en mémoire tampon](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) entre en jeu. Alors que la capacité de mise en mémoire tampon est respectée dans le cas d’une opération normale (autorisant ainsi une certaine surcharge), Cluster Resource Manager exploite la capacité totale (en occupant le tampon) pendant les mises à niveau.

## <a name="next-steps"></a>Étapes suivantes
* Commencez au début pour [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

