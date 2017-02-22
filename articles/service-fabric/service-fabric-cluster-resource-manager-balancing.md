---
title: "Équilibrer un cluster Azure Service Fabric | Microsoft Docs"
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
ms.date: 01/05/2017
ms.author: masnider
translationtype: Human Translation
ms.sourcegitcommit: bb27d279396aa7b670187560cebe2ed074576bad
ms.openlocfilehash: ee77a01b2bb17ab70099e891e00255d1cec676f6


---
# <a name="balancing-your-service-fabric-cluster"></a>Équilibrage de votre cluster Service Fabric
Service Fabric Cluster Resource Manager permet de modifier la charge dynamique, de réagir aux ajouts ou aux suppressions de nœuds ou de services, de corriger les violations de contrainte et de rééquilibrer le cluster. Mais à quelle fréquence ces opérations s’effectuent-elles, et que provoquent-elles ?

Le premier ensemble de contrôles autour d’équilibrage sont un ensemble de minuteurs. Ces minuteurs déterminent la fréquence à laquelle le Cluster Resource Manager examine l’état du cluster à la recherche de problèmes. Il existe trois catégories de travail, chacune avec son propre minuteur. Les voici :

1. Positionnement : cette étape a trait au placement des réplicas avec état ou des instances sans état manquants. Elle couvre non seulement les nouveaux services, mais aussi les réplicas avec état ou les instances sans état qui ont échoué. La suppression et l’annulation des réplicas ou des instances sont gérées ici.
2. Vérifications de contrainte : cette étape recherche les violations des différentes contraintes (règles) de placement au sein du système et apporte les corrections nécessaires. Le non-dépassement de la capacité des nœuds et le respect des contraintes de positionnement d’un service sont des exemples de règles.
3. Équilibrage : cette étape vérifie si le rééquilibrage proactif est nécessaire en fonction du niveau d’équilibrage désiré qui a été configuré pour différentes mesures. Si c’est le cas, elle tente de trouver une disposition plus équilibrée dans le cluster.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configuration des minuteurs et étapes de Cluster Resource Manager
Chacun de ces différents types de corrections offerts par Cluster Resource Manager est contrôlé par un minuteur différent qui détermine sa fréquence. Lorsque chaque minuteur se déclenche, la tâche est planifiée. Par défaut, Resource Manager :

* analyse son état et applique des mises à jour (comme l’enregistrement de l’arrêt d’un nœud) tous les dixièmes de seconde ;
* définit les indicateurs de vérification de positionnement et de contrainte toutes les secondes ;
* définit l’indicateur d’équilibrage toutes les cinq secondes.

Les informations de configuration suivantes reflètent cela :

ClusterManifest.xml :

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

À l’heure actuelle, Cluster Resource Manager effectue uniquement l’une de ces actions à la fois, séquentiellement (c’est pourquoi nous faisons référence à ces configurations en tant qu’« intervalles minimaux »). Par exemple, Cluster Resource Manager traite les demandes de création de services en attente avant d’équilibrer le cluster. Comme en témoignent les intervalles de temps par défaut spécifiés, Cluster Resource Manager analyse et vérifie fréquemment ce qu’il a à faire. Par conséquent, les modifications apportées à la fin de chaque étape sont généralement mineures. L’apport fréquent de modifications mineures rend Cluster Resource Manager réactif aux événements qui se produisent dans le cluster. Les compteurs par défaut assurent en partie un traitement par lot, car un grand nombre d’événements du même type ont tendance à se produire simultanément. Par défaut, Cluster Resource Manager n’analyse pas les modifications qui ont eu lieu sur plusieurs heures dans le cluster et n’essaie pas de les traiter toutes en même temps. En effet, cela entraînerait des pics d’activité momentanés.

Cluster Resource Manager a également besoin d’informations supplémentaires pour déterminer si le cluster est déséquilibré. Nous disposons pour cela de deux autres éléments de configuration : les *seuils d’équilibrage* et les *seuils d’activité*.

## <a name="balancing-thresholds"></a>Seuils d’équilibrage
Un seuil d’équilibrage est le principal contrôle utilisé pour déclencher le rééquilibrage proactif. Le minuteur MinLoadBalancingInterval détermine simplement la fréquence à laquelle Cluster Resource Manager doit effectuer des vérifications ; cela ne veut pas dire que quelque chose se produit. Le seuil d’équilibrage définit, pour une métrique spécifique, le niveau de déséquilibrage d’un cluster. Quand ce niveau est atteint, Cluster Resource Manager considère que le cluster est déséquilibré et déclenche l’équilibrage.

Les seuils d’équilibrage sont définis par métrique dans le cadre de la définition du cluster. Pour plus d’informations sur les mesures, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

Le seuil d’équilibrage pour une métrique est un ratio. Si la quantité de charge sur le nœud le plus chargé divisée par la quantité de charge sur le nœud le moins chargé dépasse cette valeur, le cluster est considéré comme déséquilibré. L’équilibrage est alors déclenché lorsque Cluster Resource Manager effectue sa vérification suivante.

<center>
Exemple de seuil d’équilibrage![][Image1]
</center>

Dans cet exemple, chaque service utilise une unité d’une mesure donnée. Dans l’exemple du haut, la charge maximale sur un nœud est égale à&5; et la valeur minimale à&2;. Supposons que le seuil d’équilibrage pour cette mesure soit égal à 3. Étant donné que le ratio dans le cluster est de 5/2 = 2,5, soit une valeur inférieure au seuil d’équilibrage de 3, le cluster est équilibré. Aucun équilibrage n’est déclenché lorsque Cluster Resource Manager effectue sa vérification.

Dans l’exemple du bas, la charge maximale sur un nœud est égale à&10; et la valeur minimale à&2;, ce qui donne un ratio de&5;. Cette valeur étant supérieure au seuil d’équilibrage de&3; spécifié pour cette mesure, l’exécution d’un rééquilibrage sera planifiée lors du prochain déclenchement du minuteur d’équilibrage. Dans une telle situation, une partie de la charge sera très probablement répartie sur le nœud Node3. Comme Service Fabric Cluster Resource Manager utilise une approche prudente, une partie de la charge peut également être répartie sur le nœud Node2. Cela permet de minimiser les différences globales entre les nœuds, ce qui est un des objectifs de Cluster Resource Manager.

<center>
Actions de l’exemple de seuil d’équilibrage![][Image2]
</center>

L’obtention d’une valeur inférieure au seuil d’équilibrage ne constitue pas un objectif explicite. Les seuils d’équilibrage servent simplement de *déclencheurs* indiquant à Cluster Resource Manager qu’il serait judicieux d’examiner le cluster afin d’identifier les améliorations qu’il pourrait éventuellement apporter. En effet, le déclenchement d’une analyse d’équilibrage ne signifie pas forcément qu’un élément sera déplacé. Il arrive parfois que le cluster soit déséquilibré, mais que la situation ne puisse pas être améliorée.

## <a name="activity-thresholds"></a>seuils d’activité
Parfois, même si les nœuds sont relativement déséquilibrés, la quantité *totale* de charge dans le cluster est faible. La charge insuffisante peut correspondre à une baisse temporaire ou être due au fait qu’il s’agit d’un nouveau cluster qui vient juste d’être amorcé. Dans les deux cas, il n’est pas forcément judicieux de passer du temps à équilibrer le cluster, car les gains seront minimes. Si le cluster a fait l’objet d’un équilibrage, vous consommerez des ressources réseau et de calcul pour déplacer des éléments sans que cela ait un impact *réel*. Pour éviter cela, il existe un autre contrôle appelé « seuil d’activité ». Les seuils d’activité vous permettent de spécifier une limite inférieure absolue pour l’activité. Lorsqu’aucun nœud ne dépasse ce seuil, l’équilibrage n’est pas déclenché, même si le seuil d’équilibrage est atteint.

Prenons pour exemple le schéma suivant. Supposons que nous ayons conservé notre seuil d’équilibrage de 3 pour cette mesure, mais que nous ayons aussi à présent un seuil d’activité de 1536. Dans le premier cas, bien que le cluster soit déséquilibré selon le seuil d’équilibrage, comme aucun nœud n’atteint le seuil d’activité, il ne se passe rien. Dans l’exemple du bas, le nœud Node1 dépasse largement le seuil d’activité. Comme le seuil d’équilibrage et le seuil d’activité pour la mesure sont tous deux dépassés, un équilibrage est planifié.

<center>
Exemple de seuil d’activité![][Image3]
</center>

Au même titre que les seuils d’équilibrage, les seuils d’activité sont définis par métrique par l’intermédiaire de la définition du cluster :

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.json pour les déploiements autonomes ou Template.json pour les clusters hébergés sur Azure :

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Les seuils d’équilibrage et d’activité sont tous deux liés à une mesure spécifique : l’équilibrage n’est déclenché que si le seuil d’équilibrage et le seuil d’activité sont dépassés pour la même mesure.

## <a name="balancing-services-together"></a>Équilibrage de plusieurs services en même temps
Il est intéressant de noter que la détermination de l’état de déséquilibre du cluster est une décision qui porte sur l’ensemble du cluster. Cependant, la procédure suivie pour corriger un déséquilibre consiste à déplacer des instances et des réplicas de service individuels. Logique, n’est-ce pas ? Si la mémoire est empilée sur un nœud, plusieurs réplicas ou instances peuvent être impliqués. Pour corriger le déséquilibre, il peut donc être nécessaire de déplacer les réplicas avec état ou les instances sans état qui utilisent la mesure déséquilibrée.

Cependant, il arrive parfois qu’un service qui n’était pas déséquilibré soit déplacé. Comment se peut-il alors qu’un service soit déplacé, même si ses métriques sont parfaitement équilibrées, au moment de l’autre déséquilibre ? Examinons cela de plus près.

Prenons par exemple quatre services Service1, Service2, Service3 et Service4. Service1 rend compte des mesures Metric1 et Metric2, Service2 des métriques Metric2 et Metric3, Service3 des métriques Metric3 et Metric4 et Service4 de certaines métriques Metric99. Vous voyez certainement où je veux en venir. Nous avons une chaîne ! Nous n’avons pas vraiment quatre services indépendants. Nous avons plusieurs services qui sont liés (Service1, Service2 et Service3) et un qui est indépendant.

<center>
![Équilibrage de plusieurs services en même temps][Image4]
</center>

Il est donc possible qu’un déséquilibre de la mesure Metric1 provoque le déplacement de réplicas ou d’instances appartenant au service Service3 (qui ne signale pas la mesure Metric1). En général, ces mouvements sont assez limités, mais ils peuvent être plus importants selon le déséquilibre exact de la mesure Metric1 et les modifications qui ont dû être apportées dans le cluster pour corriger la situation. Nous pouvons également affirmer avec certitude qu’un déséquilibre de la mesure Metric1, Metric2 ou Metric3 ne peut pas provoquer de déplacements pour le service Service4. Cela n’aurait aucun intérêt, puisque le déplacement de réplicas ou d’instances appartenant au service Service4 n’a pas la moindre incidence sur l’équilibre de la mesure Metric1, Metric2 ou Metric3.

Cluster Resource Manager détermine automatiquement les services qui sont liés, car il se peut que des services aient été ajoutés ou supprimés ou que leur configuration de mesures ait été modifiée. Par exemple, entre deux équilibrages, la mesure Metric2 peut avoir été supprimée de la configuration du service Service2. Cette modification rompt le lien entre les services Service1 et Service2. Au lieu de deux groupes de services liés, vous en avez à présent trois :

<center>
Équilibrage de plusieurs services en même temps![][Image5]
</center>

## <a name="next-steps"></a>Étapes suivantes
* Les métriques représentent la façon dont Service Fabric Cluster Resource Manager gère la consommation et la capacité du cluster. Pour en savoir plus sur ces mesures et sur leur configuration, consultez [cet article](service-fabric-cluster-resource-manager-metrics.md).
* Le coût du mouvement est une façon de signaler à Cluster Resource Manager que certains services sont plus coûteux à déplacer que d’autres. Pour plus d’informations sur le coût lié aux déplacements, reportez-vous à [cet article](service-fabric-cluster-resource-manager-movement-cost.md).
* Cluster Resource Manager a plusieurs limitations que vous pouvez configurer pour ralentir l’évolution dans le cluster. Elles ne sont normalement pas nécessaires mais, si vous en avez besoin, vous pouvez en savoir plus sur ces limitations [ici](service-fabric-cluster-resource-manager-advanced-throttling.md)

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png



<!--HONumber=Jan17_HO4-->


