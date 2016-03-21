<properties
   pageTitle="Intégration de Service Fabric Cluster Resource Manager et de Service Fabric Management"
   description="Vue d’ensemble des points d’intégration entre Cluster Resource Manager et Service Fabric Management."
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
   ms.date="03/03/2016"
   ms.author="masnider"/>


# Intégration de la gestion
Dans Service Fabric, le composant Resource Manager n’a pas pour fonction principale de traiter les opérations de gestion (telles que les mises à niveau d’application). Toutefois, il y participe. Tout d’abord, Resource Manager assure le suivi de l’état désiré du cluster et de ses services du point de vue des ressources et de l’équilibre. Il peut également apporter des modifications par le biais du sous-système d’intégrité Service Fabric en cas de problème. Un autre élément de l’intégration a trait au mode de fonctionnement des mises à niveau. Plus précisément, le mode de fonctionnement de Resource Manager peut être amené à évoluer pendant les mises à niveau, et certains comportements spéciaux sont appelés. Examinons plus en détail ces deux points.

## Intégration de l’intégrité
Resource Manager suit en permanence les règles que vous avez définies pour vos services. S’il ne parvient pas à respecter ces règles, il émet des avertissements et des erreurs d’intégrité. Par exemple, si un nœud est surchargé et que Resource Manager ne parvient pas à remédier au problème, il émet un avertissement d’intégrité indiquant le nœud surchargé et les mesures concernées.

Autre cas de figure : si vous avez défini une contrainte de placement (telle que « NodeColor == Blue ») et que Resource Manager détecte que cette contrainte n’est pas respectée, un avertissement d’intégrité est émis. Cela concerne les contraintes personnalisées et les contraintes par défaut (comme la distribution des domaines d’erreur et de mise à niveau) que Resource Manager applique pour vous. Voici un exemple de rapport d’intégrité. Dans ce cas, le rapport d’intégrité porte sur l’une des partitions du service système, car les réplicas de cette partition sont temporairement placés dans des domaines d’erreur trop peu nombreux (ce qui peut arriver en raison d’une chaîne d’échecs) :

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_FaultDomain', HealthState='Warning', ConsiderWarningAsError=false.

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
                        Property              : ReplicaConstraintViolation_FaultDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: FaultDomain Details: Node -- 3d1a4a68b2592f55125328cd0f8ed477  Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Voici ce que nous apprend ce message d’intégrité :

1.	Tous les réplicas sont intègres (c’est la priorité numéro un de Service Fabric).
2.	La contrainte de distribution de domaine d’erreur n’est pas respectée (ce qui signifie qu’un domaine d’erreur particulier comprend plus de réplicas que prévu pour cette partition).
3.	Le nœud qui contient le réplica à l’origine de la violation (nœud avec l’ID 3d1a4a68b2592f55125328cd0f8ed477).
4.	Quand le problème s’est produit (le 10/8/2015 à 19:13:02) Ces données utiles sont transmises à une alerte qui est déclenchée en phase de production. Elle indique qu’un problème s’est produit et que vous devriez probablement y jeter un œil. Par exemple, dans le cas présent, nous aimerions savoir pourquoi Resource Manager a décidé qu’il n’avait pas d’autre choix que de placer les réplicas dans le domaine d’erreur. Cela peut être dû au fait que tous les nœuds dans les autres domaines d’erreur sont arrêtés et qu’il n’y a pas assez de domaines de rechange, ou bien, si les domaines sont en nombre suffisant, qu’un autre élément (comme une stratégie InvalidDomain sur le service) a entraîné la non-validité des nœuds dans les autres domaines d’erreur.

Supposons toutefois que vous souhaitez créer un service, ou que Resource Manager tente de trouver un emplacement pour certains services, mais qu’aucune solution ne semble fonctionner. Plusieurs raisons peuvent expliquer ceci, mais l’une des deux conditions suivantes est souvent à l’origine du problème :

1.	Une situation temporaire empêche de placer correctement l’instance ou le réplica de ce service.
2.	Les exigences du service sont mal configurées et ne peuvent pas être respectées.

Dans chacune de ces conditions, un rapport d’intégrité Resource Manager vous donne des informations pour vous aider à déterminer ce qui se passe et comprendre pourquoi le service ne peut pas être placé. On parle de « séquence d’élimination de contrainte » pour désigner ce processus. Au cours de ce processus, nous passons en revue les contraintes configurées affectant le service et nous regardons ce qu’elles éliminent. Ainsi, quand des éléments ne peuvent pas être placés, vous pouvez voir quels nœuds ont été éliminés et pour quelle raison. Examinons les différentes contraintes qui apparaissent dans ces rapports d’intégrité et ce qu’elles vérifient. Notez que vous les verrez rarement éliminer des nœuds, car elles sont configurées par défaut à un niveau faible ou d’optimisation (comme indiqué précédemment). Vous pouvez éventuellement les voir si elles sont inversées ou traitées comme contraintes dures. Nous les présentons donc ici pour être complets :
-	ReplicaExclusionStatic et ReplicaExclusionDynamic sont des contraintes internes qui, au cours d’une recherche que nous déterminons, indiquent que deux réplicas doivent être placés sur le nœud (ce qui n’est pas autorisé). ReplicaExclusionStatic et ReplicaExclusionDynamic appliquent pratiquement la même règle. En fait, la contrainte ReplicaExclusionDynamic indique qu’il est impossible de placer un réplica à un emplacement donné parce qu’un réplica de la seule solution proposée s’y trouve déjà. L’exclusion ReplicaExclusionStatic est différente, puisqu’elle indique non pas un conflit proposé, mais un conflit réel : il y a déjà un réplica sur le nœud. Déroutant ? Oui. Cela a-t-il beaucoup d’importance ? Non. Si vous constatez une séquence d’élimination de contrainte contenant la contrainte ReplicaExclusionDynamic ou ReplicaExclusionStatic, Resource Manager pense qu’il n’y a pas suffisamment de nœuds pour placer tous les réplicas. Des contraintes ultérieures peuvent généralement indiquer la cause première du nombre insuffisant de nœuds.
-	PlacementConstraint : Si vous voyez ce message, cela signifie que certains nœuds ne correspondant pas aux contraintes de placement du service ont été éliminés. Nous avons suivi les contraintes de placement actuellement configurées dans le cadre de ce message.
-	NodeCapacity : Si vous voyez cette contrainte, cela signifie que les réplicas n’ont pas été placés sur les nœuds indiqués pour ne pas occasionner de surcharge.
-	Affinity : Cette contrainte indique que les réplicas n’ont pas été placés sur les nœuds affectés pour ne pas enfreindre la contrainte Affinity.
-	FaultDomain et UpgradeDomain : Cette contrainte élimine des nœuds si le placement du réplica sur les nœuds indiqués se traduit par une compression dans un domaine de mise à niveau ou d’erreur particulier.
-	PreferredLocation : Cette contrainte supprime des nœuds de la solution. Toutefois, comme elle a par défaut un rôle d’optimisation, vous ne devriez pas la voir. En outre, la contrainte d’emplacement par défaut n’est généralement présente que pendant les mises à niveau (quand elle est utilisée pour replacer les réplicas à l’endroit où ils se trouvaient au démarrage de la mise à niveau). Mais il est quand même possible que vous la voyiez.

## Mises à niveau
Resource Manager intervient également pendant les mises à niveau d’application et de cluster pour garantir leur bon déroulement, mais aussi pour vérifier que les règles et les performances du cluster ne sont pas compromises.

### Application continue des règles
La principale chose à retenir, c’est que les règles (contrôles stricts, comme des contraintes de placement) sont toujours appliquées pendant les mises à niveau. Vous pensez peut-être que cela va sans dire, mais mieux vaut être explicite à ce sujet. L’avantage, c’est que si vous souhaitez vous assurer que certaines charges de travail ne s’exécutent pas sur certains nœuds, il en sera de même pendant la mise à niveau, sans aucune interaction des opérations. Si votre environnement fait l’objet de nombreuses contraintes, les mises à niveau peuvent prendre du temps. En effet, si un service doit être mis hors service en vue d’une mise à jour corrective, les options de déplacement sont limitées.

### Remplacements actifs
Au démarrage d’une mise à niveau, Resource Manager prend un instantané de la disposition actuelle du cluster. Une fois la mise à niveau terminée, il tente de faire revenir les différents éléments à leur état d’origine. Le raisonnement derrière cette approche est simple : tout d’abord, elle garantit qu’il n’y a que quelques transitions pour chaque service dans le cadre de la mise à niveau (sortie du nœud affecté, puis son retour). Deuxièmement, elle garantit que la mise à niveau proprement dite a peu d’impact sur la disposition du cluster. Si le cluster était bien organisé avant la mise à niveau, il le sera aussi après.

### Des variations limitées
Pendant les mises à niveau, Resource Manager désactive l’équilibrage de l’entité mise à niveau. Par conséquent, si vous avez deux instances d’application différentes et que vous lancez une mise à niveau sur l’une d’entre elles, l’équilibrage est suspendu sur cette instance d’application, mais pas sur l’autre. Le fait d’empêcher l’équilibrage réactif permet d’éviter à Resource Manager de réagir à la mise à niveau et de vouloir à tout prix remplir le nœud vide de toutes sortes de choses. Ceci engendrerait un grand nombre de déplacements supplémentaires pour les services du cluster, mouvements de courtes durées puisqu’ils seraient ensuite annulés lors du rapatriement des services sur les nœuds après la mise à niveau. Si la mise à niveau en question est une mise à niveau de cluster, l’équilibrage de l’ensemble du cluster est suspendu pendant la mise à niveau (les vérifications de contrainte, qui veillent au respect des règles, restent actives).

### Des règles assouplies
Bien souvent, vous souhaitez terminer une mise à niveau même si le cluster est plutôt limité, voire plein. Nous avons déjà vu comment procéder, mais la différence ici, c’est que vous avez généralement entre 5 et 20 % du cluster qui n’est pas disponible au moment de la mise à niveau. Cette charge de travail doit bien aller quelque part. C’est là que la notion de capacité de mise en mémoire tampon que nous avons citée précédemment entre en jeu. Alors que la capacité de mise en mémoire tampon est respectée pendant une opération normale, Resource Manager exploite la capacité totale pendant les mises à niveau.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Découvrir Service Fabric Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)

<!------HONumber=AcomDC_0309_2016-->