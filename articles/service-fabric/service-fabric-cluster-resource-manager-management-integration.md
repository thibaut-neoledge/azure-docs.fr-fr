---
title: Intégration de Service Fabric Cluster Resource Manager et de Service Fabric Management | Microsoft Docs
description: Vue d’ensemble des points d’intégration entre Cluster Resource Manager et Service Fabric Management.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Intégration de Cluster Resource Manager à la gestion de cluster Service Fabric
Dans Service Fabric, le composant Cluster Resource Manager n’a pas pour fonction principale de traiter les opérations de gestion (telles que les mises à niveau d’application). Toutefois, il y participe. Tout d’abord, Cluster Resource Manager facilite la gestion par le suivi de l’état souhaité du cluster et des services internes du point de vue des ressources et de l’équilibrage, et par l’envoi de rapports d’intégrité lorsqu’il est impossible de définir la configuration souhaitée pour le cluster (si la capacité est insuffisante, en cas de conflit de règles sur l’emplacement d’un service). L’intégration est également liée au fonctionnement des mises à niveau : lors des mises à niveau, Cluster Resource Manager modifie son comportement. Examinons plus en détail ces deux points.

## Intégration de l’intégrité
Cluster Resource Manager suit en permanence les règles que vous avez définies pour vos services, ainsi que la capacité disponible sur les nœuds et dans le cluster, et il émet des erreurs et des avertissements d’intégrité s’il ne peut pas satisfaire à ces règles ou si la capacité est insuffisante. Par exemple, si un nœud est surchargé et que Cluster Resource Manager ne parvient pas à remédier au problème, il émet un avertissement d’intégrité indiquant le nœud surchargé et les mesures concernées.

Autre cas de figure : si vous avez défini une contrainte de placement (telle que « NodeColor == Blue ») et que Resource Manager détecte que cette contrainte n’est pas respectée, un avertissement d’intégrité est émis. Cela concerne les contraintes personnalisées et les contraintes par défaut (comme les contraintes de domaines d’erreur et de mise à niveau) appliquées automatiquement.

Voici un exemple de rapport d’intégrité. Dans ce cas, le rapport d’intégrité porte sur l’une des partitions du service système, car les réplicas de cette partition sont temporairement placés dans des domaines de mise à niveau trop peu nombreux (ce qui peut arriver en raison d’une chaîne d’échecs) :

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

Voici ce que nous apprend ce message d’intégrité :

1. Tous les réplicas sont intègres (c’est la priorité numéro un de Service Fabric).
2. La contrainte de distribution de domaine de mise à niveau n’est pas respectée (ce qui signifie qu’un domaine de mise à niveau particulier comprend plus de réplicas que prévu pour cette partition).
3. Le nœud qui contient le réplica à l’origine de la violation (nœud avec l’ID 3d1a4a68b2592f55125328cd0f8ed477).
4. Quand le problème s’est produit (le 10/08/2015 à 19:13:02)

Ces données utiles sont transmises à une alerte qui est déclenchée en phase de production. Elle indique qu’un problème s’est produit et que vous devriez probablement y jeter un œil. Par exemple, dans le cas présent, nous aimerions savoir pourquoi Resource Manager a décidé qu’il n’avait pas d’autre choix que de placer les réplicas dans le domaine de mise à niveau. Cela peut être dû au fait que tous les nœuds dans les autres domaines de mise à niveau sont arrêtés et qu’il n’y a pas assez de domaines de rechange, ou bien, si les domaines sont en nombre suffisant, qu’un autre élément (comme une stratégie de placement sur le service ou une capacité insuffisante) a entraîné l’invalidité des nœuds dans les autres domaines de mise à niveau.

Supposons toutefois que vous souhaitez créer un service, ou que Resource Manager tente de trouver un emplacement pour certains services, mais qu’aucune solution ne semble fonctionner. Plusieurs raisons peuvent expliquer ceci, mais l’une des deux conditions suivantes est souvent à l’origine du problème :

1. Une situation temporaire empêche de placer correctement l’instance ou le réplica de ce service.
2. Les exigences du service sont mal configurées et ne peuvent pas être respectées.

Dans chacune de ces conditions, un rapport d’intégrité Cluster Resource Manager vous donne des informations pour vous aider à déterminer ce qui se passe et à comprendre pourquoi le service ne peut pas être placé. On parle de « séquence d’élimination de contrainte » pour désigner ce processus. Au cours de ce processus, le système passe en revue les contraintes configurées affectant le service et les enregistrements qu’elles éliminent. Lorsque les services ne peuvent pas être placés, vous pouvez ainsi voir quels nœuds ont été éliminés et pour quelle raison.

## Types de contrainte
Examinons les différentes contraintes qui apparaissent dans ces rapports d’intégrité et ce qu’elles vérifient. Notez que vous les verrez rarement éliminer des nœuds, car elles sont configurées par défaut à un niveau faible ou d’optimisation (les priorités des contraintes sont abordées plus en détail ultérieurement dans cet article). Vous pouvez cependant voir des messages d’intégrité liés à ces contraintes s’ils sont configurés en tant que contraintes dures ou dans le cas rare dans lequel elles provoquent l’élimination de nœuds. Par souci d’exhaustivité, nous vous les présentons ci-dessous :

* ReplicaExclusionStatic et ReplicaExclusionDynamic : il s’agit d’une des contrainte interne qui indique qu’une recherche a détecté une situation dans laquelle deux réplicas avec état ou instances sans état de la même partition doivent être placés sur le même nœud (ce qui n’est pas autorisé). ReplicaExclusionStatic et ReplicaExclusionDynamic appliquent pratiquement la même règle. En fait, la contrainte ReplicaExclusionDynamic indique qu’il est impossible de placer un réplica à un emplacement donné parce qu’un réplica de la seule solution proposée s’y trouve déjà. L’exclusion ReplicaExclusionStatic est différente, puisqu’elle indique non pas un conflit proposé, mais un conflit réel : il y a déjà un réplica sur le nœud. Déroutant ? Oui. Cela a-t-il beaucoup d’importance ? Non. Si vous constatez une séquence d’élimination de contrainte contenant la contrainte ReplicaExclusionDynamic ou ReplicaExclusionStatic, Cluster Resource Manager pense qu’il n’y a pas suffisamment de nœuds pour placer tous les réplicas. Des contraintes ultérieures peuvent généralement indiquer la cause première du nombre insuffisant de nœuds.
* PlacementConstraint : Si vous voyez ce message, cela signifie que certains nœuds ne correspondant pas aux contraintes de placement du service ont été éliminés. Nous avons suivi les contraintes de placement actuellement configurées dans le cadre de ce message. Ceci est normal si vous disposez de contraintes de placement. Mais, si un bogue s’est produit dans la contrainte de placement à l’origine de l’élimination d’un trop grand nombre de nœuds, vous pouvez y voir le résultat suivant.
* NodeCapacity : Si vous voyez cette contrainte, cela signifie que les réplicas n’ont pas été placés sur les nœuds indiqués pour ne pas occasionner de surcharge.
* Affinity : Cette contrainte indique que les réplicas n’ont pas été placés sur les nœuds affectés pour ne pas enfreindre la contrainte Affinity.
* FaultDomain et UpgradeDomain : cette contrainte élimine des nœuds si le placement du réplica sur les nœuds indiqués se traduit par une compression dans un domaine de mise à niveau ou d’erreur spécifique. Plusieurs exemples décrivant cette contrainte sont présentés dans la rubrique [Contraintes des domaines d’erreur et de mise à niveau, et comportement résultant](service-fabric-cluster-resource-manager-cluster-description.md)
* PreferredLocation : Cette contrainte supprime des nœuds de la solution. Toutefois, comme elle a par défaut un rôle d’optimisation, vous ne devriez pas la voir. En outre, la contrainte d’emplacement par défaut n’est généralement présente que pendant les mises à niveau (quand elle est utilisée pour replacer les réplicas à l’endroit où ils se trouvaient au démarrage de la mise à niveau). Mais il est quand même possible que vous la voyiez.

### Priorités de contrainte
Avec toutes ces contraintes, vous vous dites peut-être : « Je pense que, dans mon système, les contraintes les plus importantes sont les contraintes de placement. Il est fort possible que je ne respecte pas les autres contraintes, même liées à l’affinité et à la capacité, si cela me permet de toujours respecter les contraintes de placement. »

Eh bien, c’est possible ! Les contraintes peuvent être configurées en fonction de différents niveaux d’application, qui se résument le plus souvent à la valeur « strict » (0), « souple » (1), « optimisation » (2) et « désactivé » (-1). La plupart des contraintes que nous avons défini comme strictes par défaut (par exemple, la capacité), et presque toutes sont strictes ou souples. Toutefois, cela peut changer dans certains cas. Par exemple, si vous souhaitez vous assurer que l’affinité ne sera pas respectée afin de résoudre les problèmes de capacité de nœud, vous pouvez associer la contrainte d’affinité à la valeur « Souple » (1) et laisser la contrainte de capacité définie sur « Strict » (0). Les différentes priorités de contrainte expliquent également pourquoi des avertissements de violation de contrainte s’affichent plus souvent que d’autres : nous sommes prêts à assouplir (violer) temporairement certaines contraintes. Ces niveaux ne signifient pas qu’une contrainte donnée sera toujours respectée ou non, mais simplement qu’il existe une préférence pour l’ordre d’application des contraintes, ce qui nous permet d’aboutir à des compromis satisfaisants le cas échéant.

La configuration et les valeurs de priorité par défaut associées aux contraintes sont répertoriées ci-dessous :

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

Des contraintes sont définies pour les domaines de mise à niveau et d’erreur : la contrainte de domaine de mise à niveau est souple. Il existe également une contrainte PreferredLocation spécifique avec une priorité définie. Qu’est-ce que c’est ?

Tout d’abord, nous présentons le souhait de conserver les services répartis entre les domaines d’erreur et de mise à niveau sous la forme d’une contrainte dans le moteur de Resource Manager. À plusieurs reprises, nous avons eu besoin d’un placement très strict concernant les domaines d’erreur et de mise à niveau, et il fallait parfois les ignorer totalement (mais brièvement !). Nous avons donc apprécié ce choix de conception et la flexibilité de l’infrastructure de contrainte. La plupart du temps, cette contrainte est souple : si Resource Manager a temporairement besoin de regrouper quelques réplicas dans un domaine de mise à niveau afin de traiter, par exemple, une mise à niveau en cours, ou une série d’échecs simultanés ou d’autres violations de contrainte (de type strict), il n’y a aucun problème. Mais généralement, ce n’est pas le cas, à moins qu’il n’existe de nombreuses défaillances ou d’autres évolutions dans le système empêchant un positionnement correct. De plus, si l’environnement est configuré correctement, l’état stable est associé à des domaines de mise à niveau entièrement respectés.

La contrainte PreferredLocation est un peu différente : il s’agit de la seule contrainte définie sur « optimisation ». Nous utilisons cette contrainte lors de mises à niveau afin de tenter de replacer les services là où nous les avons trouvés avant la mise à niveau. Il existe toutes sortes de raisons pour lesquelles cette méthode ne fonctionne pas dans la pratique, mais c’est une optimisation intéressante. Nous aborderons ce sujet plus en détail lorsque nous parlerons de l’utilité de Cluster Resource Manager lors de mises à niveau.

## Mises à niveau
Cluster Resource Manager intervient également pendant les mises à niveau d’application et de cluster pour garantir leur bon déroulement, mais aussi pour vérifier que les règles et les performances du cluster ne sont pas compromises.

### Application continue des règles
Le point principal à retenir est que les règles (les contraintes strictes, comme des contraintes de placement) sont toujours appliquées pendant des mises à niveau. Vous pensez peut-être que cela va sans dire, mais mieux vaut être explicite à ce sujet. Cela présente un avantage notable : si vous souhaitez vous assurer que certaines charges de travail ne s’exécutent pas sur certains nœuds, ces règles seront automatiquement appliquées, même pendant la mise à niveau. Si votre environnement fait l’objet de nombreuses contraintes, les mises à niveau peuvent prendre du temps. En effet, si un service (ou le nœud sur lequel il se trouve) doit être mis hors service en vue d’une mise à jour, les options de déplacement sont limitées.

### Remplacements actifs
Au démarrage d’une mise à niveau, Resource Manager prend un instantané de la disposition actuelle du cluster. Une fois la mise à niveau terminée, il tente de faire revenir les différents éléments à leur état d’origine. Le raisonnement derrière cette approche est simple : tout d’abord, elle garantit qu’il n’y a que quelques transitions pour chaque réplica ou instance de service dans le cadre de la mise à niveau (en sortie du nœud affecté, puis retour). Deuxièmement, elle garantit que la mise à niveau proprement dite a peu d’impact sur la disposition du cluster. Si le cluster était bien organisé avant la mise à niveau, il le sera aussi après, ou en tout cas, la situation ne se dégradera pas.

### Évolution limitée
Pendant les mises à niveau, Cluster Resource Manager désactive l’équilibrage de l’entité mise à niveau. Par conséquent, si vous avez deux instances d’application différentes et que vous lancez une mise à niveau sur l’une d’entre elles, l’équilibrage est suspendu sur cette instance d’application, mais pas sur l’autre. Empêcher un équilibrage réactif évite des réactions inutiles à la mise à niveau proprement dite (« Oh non ! et de vouloir Il faut à tout prix le remplir avec toutes sortes de choses !). Cela empêcherait un grand nombre de déplacements supplémentaires pour les services du cluster, qui devront être annulés lors du rapatriement des services sur les nœuds après la mise à niveau. Si la mise à niveau en question est une mise à niveau de cluster, l’équilibrage de l’ensemble du cluster est suspendu pendant la mise à niveau (les vérifications de contrainte, qui veillent au respect des règles, restent actives, l’équilibrage proactif est désactivé).

### Règles assouplies
Bien souvent, vous souhaitez terminer une mise à niveau même si le cluster est plutôt limité, voire plein. Pendant les mises à niveau, il est encore plus important de gérer la capacité du cluster que d’habitude, car la capacité est généralement réduite de 5 à 20 % lorsque la mise à niveau est exécutée dans le cluster, et cette charge de travail doit généralement aller quelque part. C’est là que la notion de [capacité de mise en mémoire tampon](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) entre en jeu. Alors que la capacité de mise en mémoire tampon est respectée dans le cas d’une opération normale (autorisant ainsi une certaine surcharge), Cluster Resource Manager exploite la capacité totale (en occupant le tampon) pendant les mises à niveau.

## Étapes suivantes
* Commencez au début pour [obtenir une présentation de Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

<!---HONumber=AcomDC_0824_2016-->