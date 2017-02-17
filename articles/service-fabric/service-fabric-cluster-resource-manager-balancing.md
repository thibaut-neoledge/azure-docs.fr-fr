---
title: "Équilibrage de votre cluster avec Azure Service Fabric Cluster Resource Manager | Microsoft Docs"
description: "Présentation de l’équilibrage de votre cluster avec Service Fabric Cluster Resource Manager."
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: 030b1465-6616-4c0b-8bc7-24ed47d054c0
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: d278e0125d698306366713a6e11957dad2de10c5


---
# <a name="balancing-your-service-fabric-cluster"></a>Équilibrage de votre cluster Service Fabric
Service Fabric Cluster Resource Manager permet de créer des rapports sur la charge dynamique, de réagir aux modifications dans le cluster, de corriger des violations de limites et de rééquilibrer le cluster le cas échéant. Mais à quelle fréquence ces opérations s’effectuent-elles, et que provoquent-elles ? Plusieurs contrôles sont liés à ceci.

Le premier ensemble de contrôles autour d’équilibrage sont un ensemble de minuteurs. Ces minuteurs déterminent la fréquence à laquelle le Cluster Resource Manager examine l’état du cluster à la recherche de problèmes. Il existe trois catégories de travail, chacune avec son propre minuteur. Les voici :

1. Placement : cette étape a trait au placement des réplicas avec état ou des instances sans état manquants. Elle couvre non seulement les nouveaux services, mais aussi les réplicas avec état ou les instances sans état qui ont échoué et qui doivent être recréés. La suppression et l’annulation des réplicas ou des instances sont également traitées ici.
2. Vérifications de contrainte : cette étape recherche les violations des différentes contraintes (règles) de placement au sein du système et apporte les corrections nécessaires. Le non-dépassement de la capacité des nœuds et le respect des contraintes de placement d’un service (plus d’informations à ce sujet plus loin) sont des exemples de règles.
3. Équilibrage : cette étape vérifie si le rééquilibrage proactif est nécessaire en fonction du niveau d’équilibrage désiré qui a été configuré pour différentes métriques. Dans l’affirmative, elle tente de trouver une disposition dans le cluster qui est plus équilibré.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuration des minuteurs et étapes de Cluster Resource Manager
Chacun de ces différents types de corrections possibles par le Cluster Resource Manager est contrôlé par un minuteur différent qui détermine sa fréquence. Par exemple, vous pouvez très bien placer de nouvelles charges de travail de service dans le cluster toutes les heures (en vue d’un traitement par lot), tout en effectuant des contrôles réguliers de l’équilibrage à un intervalle de quelques secondes. Ce comportement est configurable. Lorsque chaque minuteur se déclenche, la tâche est planifiée. Par défaut, Resource Manager analyse son état et applique des mises à jour (en appliquant par lots les modifications effectuées depuis la dernière analyse, par exemple la détection d’un nœud défaillant) tous les dixièmes de seconde, définit les indicateurs de vérification de contrainte et de placement toutes les secondes, et définit l’indicateur d’équilibrage toutes les cinq secondes.

ClusterManifest.xml :

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Aujourd'hui, nous effectuons uniquement l’une de ces actions à la fois, séquentiellement (c’est pourquoi nous faisons référence à ces configurations en tant « qu’intervalles minimaux »)). Nous pouvons ainsi répondre à toutes les demandes de création de réplica en attente avant de passer à l’équilibrage du cluster. Comme en témoignent les intervalles de temps par défaut spécifiés, nous pouvons analyser et vérifier très fréquemment ce que nous voulons. Cela signifie que l’ensemble de modifications effectuées à la fin de chaque séquence est généralement plus petit : nous n’analysons pas les modifications sur plusieurs heures dans le cluster et nous n’essayons pas de les corriger toutes en même temps. L’idée est de tenter de traiter plus au moins les problèmes à mesure qu’ils se présentent, mais en privilégiant un traitement par lots dès lors que plusieurs événements surviennent simultanément. De ce fait, Service Fabric Resource Manager est très réactif aux événements qui se produisent dans le cluster.

Même si la plupart de ces tâches sont simples (s’il existe des violations de contraintes, elles doivent être corrigées, si des services doivent être créés, créez-les), le Cluster Resource Manager á également besoin d’informations supplémentaires afin de déterminer si le cluster déséquilibré. Nous disposons pour cela de deux autres éléments de configuration : les *seuils d’équilibrage* et les *seuils d’activité*.

## <a name="balancing-thresholds"></a>Seuils d’équilibrage
Un seuil d’équilibrage est le contrôle principal pour déclencher un rééquilibrage proactif (n’oubliez pas que le minuteur détermine simplement la fréquence à laquelle le Cluster Resource Manager doit effectuer des vérifications, cela ne signifie pas que quelque chose se produit). Le seuil d’équilibrage définit, pour une métrique spécifique, le niveau de déséquilibrage d’un cluster. Quand ce niveau est atteint, Cluster Resource Manager considère que le cluster est déséquilibré et déclenche l’équilibrage.

Les seuils d’équilibrage sont définis par métrique dans le cadre de la définition du cluster. Pour plus d'informations sur les métriques, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

Le seuil d’équilibrage pour une métrique est un ratio. Si la quantité de charge sur le nœud le plus chargé divisée par la quantité de charge sur le nœud le moins chargé dépasse ce nombre, le cluster est considéré comme déséquilibré. L’équilibrage est alors déclenché à la prochaine analyse du Cluster Resource Manager (par défaut, toutes les 5 secondes, comme déterminé par MinLoadBalancingInterval ci-dessus).

![Exemple de seuil d’équilibrage][Image1]

Dans cet exemple simple, chaque service utilise une unité d’une métrique donnée. Dans l’exemple du haut, la charge maximale sur un nœud est 5 et la valeur minimale est 2. Supposons que le seuil d’équilibrage pour cette métrique soit égal à 3. Par conséquent, dans cet exemple, on considère que le cluster est équilibré et qu’aucun équilibrage ne sera donc déclenché lorsque le Cluster Resource Manager effectue une analyse (étant donné que le ratio au niveau du cluster est de 5/2 = 2,5, soit moins que le seuil d’équilibrage spécifié de 3).

Dans l’exemple du bas, la charge maximale sur un nœud est 10 et la valeur minimale 2, ce qui donne un ratio de 5. Le cluster est alors placé au-dessus du seuil d’équilibrage de 3 défini pour cette mesure. Par conséquent, l’exécution d’un rééquilibrage globale sera planifiée au prochain déclenchement d’un minuteur d’équilibrage. Notez que le déclenchement d’une analyse d’équilibrage ne signifie pas forcément qu’un élément sera déplacé. Parfois, le cluster est déséquilibré, mais le problème ne peut pas être réglé. Dans une telle situation (au moins par défaut) une partie de la charge sera certainement distribuée sur le Node3. Comme nous utilisons une approche prudente, notez qu’une partie de la charge peut également être distribuée sur le Node2, ce qui permettrait de réduire les différences globales entre les nœuds. On peut cependant s’attendre à ce que l’essentiel de la charge soit transférée sur le Node3.

![Actions de l’exemple de seuil d’équilibrage][Image2]

Notez que le but n’est pas explicitement de se trouver en-deçà du seuil d’équilibrage : les seuils d’équilibrage servent simplement de *déclencheurs* indiquant à Cluster Resource Manager qu’il serait judicieux d’examiner le cluster afin d’identifier les améliorations qu’il pourrait éventuellement apporter, le cas échéant.

## <a name="activity-thresholds"></a>seuils d’activité
Parfois, même si les nœuds sont relativement déséquilibrés, la quantité *totale* de charge dans le cluster est faible. Cela peut être simplement dû à l’heure de la journée ou au fait qu’il s’agit d’un nouveau cluster qui vient juste d’être amorcé. Dans les deux cas, ne consacrez pas trop de temps à l’équilibrage du cluster, car les gains liés au déplacement sont minimes. Ce qui est sûr, c’est que vous allez consommer des ressources réseau et de calcul sans réellement apporter de modification. Pour éviter cela, Resource Manager comprend un autre contrôle, appelé « seuil d’activité ». Celui-ci permet de spécifier une limite inférieure absolue pour une activité. Si aucun nœud ne dispose au moins de cette quantité de charge, l’équilibrage n’est pas déclenché, et ce même si le seuil d’équilibrage est atteint.

Prenons un exemple : nos rapports indiquent les totaux de consommation suivants sur ces nœuds. Notre seuil d’équilibrage est toujours de 3, mais notre seuil d’activité est désormais de 1536 pour cette métrique. Dans le premier cas, bien que le cluster soit déséquilibré selon le seuil d’équilibrage, aucun nœud ne répond au seuil minimum d’activité. Donc, nous ne changeons rien. Dans l’exemple du bas, Node1 dépasse largement le seuil d’activité. L’équilibrage sera dont effectué (étant donné que le seuil d’équilibrage et le seuil d’activité de la mesure sont tous les deux dépassés)

![Exemple de seuil d’activité][Image3]

Au même titre que les seuils d’équilibrage, les seuils d’activité sont définis par métrique par l’intermédiaire de la définition du cluster :

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Notez que les seuils d’équilibrage et d’activité sont tous deux liés à la métrique : l’équilibrage ne sera déclenché que si les seuils d’équilibrage et d’activité sont tous deux dépassés pour la même métrique. Par conséquent, si nous dépassons le seuil d’équilibrage pour la mémoire et le seuil d’activité pour l’UC, l’équilibrage ne se déclenchera pas tant que les seuils restants (seuil d’équilibrage pour l’UC et seuil d’activité pour la mémoire) ne seront pas dépassés.

## <a name="balancing-services-together"></a>Équilibrage de plusieurs services en même temps
Il est intéressant de noter que l’état de déséquilibrage du cluster est une décision qui porte sur l’ensemble du cluster, mais que la procédure que nous suivons pour corriger cette situation consiste à déplacer individuellement des instances et des réplicas de service. Logique, n’est-ce pas ? Si la mémoire est empilée sur un nœud, plusieurs réplicas ou instances peuvent être impliqués. Il peut donc être nécessaire de déplacer l’un de ces réplicas à état ou l’une de ces instances sans état qui utilisent la métrique déséquilibrée affectée.

Il arrive qu’un client nous appelle ou soumette un ticket pour nous dire qu’un service non déséquilibré a été déplacé. Comment se peut-il alors qu’un service soit déplacé, même si ses métriques sont parfaitement équilibrées, au moment de l’autre déséquilibre ? Examinons cela de plus près.

Prenons par exemple quatre services Service1, Service2, Service3 et Service4. Service1 rend compte des mesures Metric1 et Metric2, Service2 des métriques Metric2 et Metric3, Service3 des métriques Metric3 et Metric4 et Service4 de certaines métriques Metric99. Vous voyez certainement où je veux en venir. Nous avons une chaîne ! Du point de vue de Cluster Resource Manager, nous n’avons pas vraiment quatre services indépendants. Nous avons plusieurs services qui sont liés (Service1, Service2 et Service3) et un qui est indépendant.

![Équilibrage de plusieurs services en même temps][Image4]

Il est donc possible qu’un déséquilibre dans Metric1 provoque le déplacement de réplicas ou d’instances appartenant à Service3. En général, ces mouvements sont assez limités, mais ils peuvent être plus importants selon le déséquilibre exact de Metric1 et des modifications qui ont dû être apportées dans le cluster pour le corriger. Nous pouvons également affirmer avec certitude qu’un déséquilibre dans la métrique 1, 2 ou 3 ne provoque jamais de mouvements dans Service4. Cela n’aurait aucun sens, puisque le déplacement de réplicas ou d’instances appartenant à Service4 n’a aucune incidence sur l’équilibre de la métrique 1, 2 ou 3.

Cluster Resource Manager détermine automatiquement les services qui sont associés, car il peut arriver que des services soient ajoutés ou supprimés ou que la configuration de leur métrique soit modifiée. Par exemple, entre deux équilibrages, Service2 peut être reconfiguré pour supprimer Metric2. La chaîne entre Service1 et Service2 est alors rompue. Au lieu de deux groupes de services, vous en avez à présent trois :

![Équilibrage de plusieurs services en même temps][Image5]

## <a name="next-steps"></a>Étapes suivantes
* Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces métriques et la façon de les configurer, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md)
* Le coût du mouvement est une façon de signaler à Cluster Resource Manager que certains services sont plus coûteux à déplacer que d’autres. Pour en savoir plus sur le coût du déplacement, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md)
* Cluster Resource Manager a plusieurs limitations que vous pouvez configurer pour ralentir l’évolution dans le cluster. Elles ne sont normalement pas nécessaires mais, si vous en avez besoin, vous pouvez en savoir plus sur ces limitations [ici](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Dec16_HO2-->


