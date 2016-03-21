<properties
   pageTitle="Équilibrage de votre cluster avec Azure Service Fabric Cluster Resource Manager"
   description="Présentation de l’équilibrage de votre cluster avec Service Fabric Cluster Resource Manager."
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

# Équilibrage de votre cluster Service Fabric
Service Fabric Cluster Resource Manager permet de créer des rapports sur la charge dynamique, de réagir aux modifications dans le cluster et de générer des plans d’équilibrage. Mais à quel moment ces opérations sont-elles effectuées ? Qu’est-ce qui déclenche vraiment le rééquilibrage du cluster si le placement des services est déterminé par la valeur de leur charge par défaut quand ils sont créés ? Plusieurs contrôles sont liés à ceci.

Le premier jeu de contrôles régissant l’équilibrage est un jeu de minuteurs, qui déterminent la fréquence à laquelle Cluster Resource Manager examine l’état du cluster à la recherche de situations à traiter. Ces minuteurs sont associés aux différentes étapes de travail d’un cluster type. Ces étapes sont les suivantes :

1.	Placement : cette étape a trait au placement des réplicas avec état ou des instances sans état manquants. Elle couvre non seulement les nouveaux services, mais aussi les réplicas ou les instances qui ont échoué et qui doivent être recréés. La suppression et l’annulation des réplicas ou des instances sont également traitées ici.
2.	Vérifications de contrainte : cette étape recherche les violations des différentes contraintes (règles) de placement au sein du système et apporte les corrections nécessaires. Le non-dépassement de la capacité des nœuds et le respect des contraintes de placement d’un service (plus d’informations à ce sujet plus loin) sont des exemples de règles.
3.	Équilibrage : cette étape vérifie si le rééquilibrage proactif est nécessaire en fonction du niveau d’équilibrage désiré qui a été configuré pour différentes métriques. Dans l’affirmative, elle tente de trouver une disposition dans le cluster qui est plus équilibré.

Chaque étape est contrôlée par un minuteur différent qui régit sa fréquence. Par exemple, vous pouvez très bien placer de nouvelles charges de travail de service dans le cluster toutes les heures (en vue d’un traitement par lot), tout en effectuant des contrôles réguliers de l’équilibrage à un intervalle de quelques secondes. Chaque fois qu’un minuteur est déclenché, un indicateur est défini pour vous informer de la nécessité de traiter cette partie des tâches de Resource Manager. L’indicateur est récupéré au prochain balayage global de la machine à états (c’est pourquoi ces configurations sont définies sous forme d’intervalles minimaux). Par défaut, Resource Manager analyse son état et applique des mises à jour tous les dixièmes de seconde, définit les indicateurs de vérification de contrainte et de placement toutes les secondes, et définit l’indicateur d’équilibrage toutes les cinq secondes.

ClusterManifest.xml :

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Aujourd’hui, nous effectuons une seule de ces actions à la fois, de manière séquentielle. Nous pouvons ainsi répondre à toutes les demandes de création de réplica avant de passer à l’équilibrage du cluster. Comme en témoignent les intervalles de temps par défaut spécifiés, nous pouvons analyser et vérifier très fréquemment quoi que ce soit. Cela qui signifie que chaque ensemble de modifications est généralement plus petit que le précédent (nous n’analysons pas des modifications étalées sur plusieurs heures dans le cluster et nous n’essayons pas de les corriger toutes en même temps ; nous pouvons gérer efficacement les problèmes à mesure qu’ils se présentent). De ce fait, Service Fabric Resource Manager est très réactif aux événements qui se produisent dans le cluster.

Il est aussi essentiel que Cluster Resource Manager sache à quel moment le cluster doit être considéré comme déséquilibré et quels réplicas déplacer pour résoudre les problèmes. Nous disposons pour cela de deux autres éléments de configuration majeurs : les seuils d’équilibrage et les seuils d’activité.

## Seuils d’équilibrage
Un seuil d’équilibrage est le principal contrôle utilisé pour déclencher le rééquilibrage proactif. Le seuil d’équilibrage définit, pour une métrique spécifique, le niveau de déséquilibrage d’un cluster. Quand ce niveau est atteint, Resource Manager considère que le cluster est déséquilibré et déclenche l’équilibrage à la prochaine exécution. Les seuils d’équilibrage sont définis par métrique dans le cadre du manifeste de cluster :

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Le seuil d’équilibrage pour une métrique est un ratio. Si la quantité de charge sur le nœud le plus chargé divisée par la quantité de charge sur le nœud le moins chargé dépasse ce ratio, le cluster est considéré comme déséquilibré. L’équilibrage est alors déclenché à la prochaine exécution du nœud d’état de Resource Manager.

![Exemple de seuil d’équilibrage][Image1]

Dans cet exemple simple, chaque service utilise uniquement une unité d’une métrique donnée. Dans l’exemple du haut, la charge maximale sur un nœud est 5 et la valeur minimale est 2. Supposons que le seuil d’équilibrage pour cette métrique soit égal à 3. Dans l’exemple du haut, le cluster est considéré comme équilibré. Aucun équilibrage n’est donc déclenché. Dans l’exemple du bas, la charge maximale sur un nœud est 10 et la valeur minimale 2. Le seuil d’équilibrage défini de 3 est donc dépassé. Dans ce cas, la charge sera très certainement distribuée à Node3 lors de la prochaine exécution de Resource Manager. Comme nous n’utilisons pas une approche gourmande, notez qu’une partie de la charge peut également se retrouver sur Node2, ce qui permettrait de réduire les différences globales entre les nœuds.

![Actions de l’exemple de seuil d’équilibrage][Image2]

Notez que l’obtention d’une valeur inférieure au seuil d’équilibrage ne constitue pas un objectif explicite : les seuils d’équilibrage jouent simplement le rôle de déclencheur. Seuils d’activité Parfois, même si des nœuds sont relativement déséquilibrés, la quantité totale de charge dans le cluster est faible. Cela peut être simplement dû à l’heure de la journée ou au fait qu’il s’agit d’un nouveau cluster qui vient juste d’être amorcé. Dans les deux cas, ne consacrez pas trop de temps à l’équilibrage, car les gains liés au déplacement sont minimes. Ce qui est sûr, c’est que vous allez consommer des ressources réseau et de calcul. Resource Manager comprend un autre contrôle, appelé « seuil d’activité ». Celui-ci permet de spécifier une limite inférieure absolue pour une activité. Si aucun nœud ne dispose au moins de cette quantité de charge, l’équilibrage n’est pas déclenché, et ce même si le seuil d’équilibrage est atteint. Prenons un exemple : nos rapports indiquent les totaux de consommation suivants sur ces nœuds. Notre seuil d’équilibrage est toujours de 3, mais notre seuil d’activité est désormais de 1 536. Dans le premier cas, bien que le cluster soit déséquilibré selon le seuil d’équilibrage, aucun nœud ne répond au seuil minimum d’activité. Donc, nous ne changeons rien. Dans l’exemple du bas, Node1 dépasse largement le seuil d’activité : l’équilibrage a donc bien lieu.

![Exemple de seuil d’activité][Image3]

Au même titre que les seuils d’équilibrage, les seuils d’activité sont définis par métrique par l’intermédiaire du manifeste de cluster :

``` xml
      <Section Name="MetricActivityThresholds">
        <Parameter Name="Memory" Value="1536"/>
      </Section>
```

## Équilibrage de plusieurs services en même temps
Il est intéressant de noter que l’état de déséquilibrage du cluster est une décision qui porte sur l’ensemble du cluster, mais que la procédure que nous suivons pour corriger cette situation consiste à déplacer individuellement des instances et des réplicas de service. Logique, n’est-ce pas ? Si la mémoire est empilée sur un nœud, plusieurs réplicas ou instances peuvent être impliqués. Il peut donc être nécessaire de déplacer l’ensemble des réplicas ou des instances qui utilisent la métrique déséquilibrée affectée.

Il arrive qu’un client nous appelle ou soumette un ticket pour nous dire qu’un service non déséquilibré a été déplacé. Comment cela peut-il arriver si toutes les métriques du service sont équilibrées, même parfaitement équilibrées, au moment de l’autre déséquilibre ? Examinons cela de plus près.

Prenons par exemple quatre services : S1, S2, S3 et S4. S1 est associé aux métriques M1 et M2, S2 aux métriques M2 et M3, S3 aux métriques M3 et M4, et S4 à la métrique M99. Vous voyez certainement où je veux en venir. Nous avons une chaîne ! Du point de vue de Resource Manager, nous n’avons pas vraiment quatre services indépendants. Nous avons plusieurs services qui sont liés (S1, S2 et S3) et un qui est indépendant.

![Équilibrage de plusieurs services en même temps][Image4]

Il est donc possible qu’un déséquilibre dans Metric1 provoque le déplacement de réplicas ou d’instances appartenant à Service3. En général, ces mouvements sont assez limités, mais ils peuvent être plus importants selon le déséquilibre exact de Metric1 et des modifications qui ont dû être apportées dans le cluster pour le corriger. Nous pouvons également affirmer avec certitude qu’un déséquilibre dans la métrique 1, 2 ou 3 ne provoque jamais de mouvements dans Service4. Cela n’aurait aucun sens, puisque le déplacement de réplicas ou d’instances appartenant à Service4 n’a aucune incidence sur l’équilibre de la métrique 1, 2 ou 3.

Chaque fois qu’il s’exécute, Resource Manager détermine automatiquement les services qui sont associés, car il peut arriver que des services soient ajoutés ou supprimés ou que la configuration de leur métrique soit modifiée. Par exemple, entre deux équilibrages, Service2 peut être reconfiguré pour supprimer Metric2. La chaîne entre Service1 et Service2 est alors rompue. Au lieu de deux groupes de services, vous en avez à présent trois :

![Équilibrage de plusieurs services en même temps][Image5]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [En savoir plus sur les métriques](service-fabric-cluster-resource-manager-metrics.md)
- [En savoir plus sur les limitations de Resource Manager](service-fabric-cluster-resource-manager-advanced-throttling.md)
- [En savoir plus sur le coût du mouvement de service](service-fabric-cluster-resource-manager-movement-cost.md)


[Image1]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]: ./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png

<!---HONumber=AcomDC_0309_2016-->